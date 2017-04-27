> - 看了些文章，大牛对同问题的解决方案
> - 传送门：[Fast-Paced Multiplayer](http://www.gabrielgambetta.com/fast_paced_multiplayer.html)

---
文中大体的思路：

1. A玩家 移动时，本机自行移动，并发送移动指令给服务端，假设移动是成功的，服务端同步其他客户端 B玩家，B玩家 中用一个队列 **Queue** 来装服务端来的移动指令，然后客户端在updata中做插值 (**lerp** ) 处理，这样 A玩家 在 B玩家客户端中移动起来就比较平滑

2. 如果 A玩家 移动很频繁，B玩家 中的 指令队列 **Queue** 会堆积的很大，这里可以做个优化，就是当 **Queue** 的 **size** 超过某个临界值 （**threshold**）时，加快插值（**lerp**）的速率

3. A玩家 移动时，本机自行移动 并保留一份此次移动的 副本 （**copy**）到一个 **队列** 中，并发送移动指令给服务端，如果服务端判定移动是失败的（比如穿墙之类的），则服务端下发指令给 A玩家 修复此次移动的位置，然后 **队列** 中移除此次移动的副本

4. 关于攻击时的同步，客户端A 中自行播放攻击动作并上行给服务的此次攻击的指令，服务端同步其他 客户端B 播放攻击动作，同时同步给所有客户端（客户端A和B）扣血指令，为防止客户端作弊必须有服务端运行计算实际扣血量。

---
下面是部分关于位置同步的代码
```csharp
using UnityEngine;
using System.Collections;
using UnityEngine.Networking;
using System.Collections.Generic;

/// <summary>
/// 可以通过
/// 1. 减少发包率（意思就是 增大 sendInterval 发包间隔）
/// 2. 增大 closeEnough 距离
/// 3. 增大 normalLerpRate、fasterLerpRate 插值速率
/// </summary>

[NetworkSettings(channel = 0, sendInterval = 0.1f)]
public class SmoothMove : NetworkBehaviour
{

    [SyncVar(hook = "SyncPostionsValues")]
    private Vector3 syncPos; //同步变量

    [SerializeField]
    Transform myTransform; //SerializeField用于inspector中显示非public变量
    private float lerpRate;
    private float normalLerpRate = 16.0f;
    private float fasterLerpRate = 27.0f;

    private Vector3 lastPos;
    private float threshold = 0.5f;

    private List<Vector3> syncPosList = new List<Vector3>();
    [SerializeField]
    private bool useHistoriicalLerping = false; //是否启用平滑插值的开关，直接在 inspector 中设置
    private float closeEnough = 0.11f;

    public void Start()
    {
        lerpRate = normalLerpRate;
    }

    public void Update()
    {
        LerpPosition(); //因为方法利用了Time.deltaTime，所以只能在 Updata中调用
    }

    public void FixedUpdate() //1. server 和 client 都执行FixedUpdate
    {
        TransmitPosition(); //2. 因为是 ClientCallback，所以只有客户端调用
    }

    void LerpPosition()
    {
        if (!isLocalPlayer) //5. 只有非本机玩家才进行插值移动到最新的 syncPos 位置
        {
            if (useHistoriicalLerping) //更加平滑
            {
                HistoryLerping();
            }
            else
            {
                OrdinaryLerping();
            }
        }
    }

    [Command]
    void CmdProvidePositionToServer(Vector3 pos)
    {
        syncPos = pos; //4. 服务端收到信息同步给所有客户端的该对象的syncPos变量
    }

    [Client]
    void TransmitPosition()
    {
        if (isLocalPlayer && Vector3.Distance(myTransform.position, lastPos) > threshold) //3. 只用本机玩家才提交位置信息到server上
        {
            CmdProvidePositionToServer(myTransform.position);
        }
    }

    [Client]
    public void SyncPostionsValues(Vector3 lastPos)
    {
        syncPos = lastPos;
        syncPosList.Add(syncPos); //将所有服务端同步过来的 pos 全都保存在队列中
    }

    void OrdinaryLerping() //普通插值，有卡顿现象
    {
        myTransform.position = Vector3.Lerp(myTransform.position, syncPos, Time.deltaTime * lerpRate);
    }

    void HistoryLerping() //平滑插值
    {
        if (syncPosList.Count > 0)
        {
            //取出队列中的第一个设为插值的目标
            myTransform.position = Vector3.Lerp(myTransform.position, syncPosList[0], Time.deltaTime * lerpRate);

            //位置足够接近，从队列中移除第一个，紧接着就是第二个
            if (Vector3.Distance(myTransform.position, syncPosList[0]) < closeEnough)
            {
                syncPosList.RemoveAt(0);
            }

            //如果同步队列过大，加快插值速率，使其更快到达目标点
            if (syncPosList.Count > 10)
            {
                lerpRate = fasterLerpRate;
            }
            else
            {
                lerpRate = normalLerpRate;
            }

            Debug.LogFormat("--- syncPosList, count:{0}", syncPosList.Count);
        }
    }
}
```