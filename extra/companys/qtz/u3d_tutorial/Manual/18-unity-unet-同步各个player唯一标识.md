> - **Multiplayer Game** 中所有 player 都有一个唯一标识。在unet中可以通过 **Network Identity** 组件获取到该 player 在整个网络整的 **唯一** 的连接 **id**
> - 这里测试则随便加了一个前缀(**Player_**)+**id** 作为唯一的 **name** 赋值给 transform.name，然后可以在 **Runtime** 时 **Hierarchy** 中可以观察到

---
## 1. 整体思路：
		
1. **OnStartLocalPlayer** 时，本机通过 **Network Identity** 组件的 **id** 生产唯一 **name**，设置的 **transform.name** 并 send 到服务端同步给其他客户端
2. 本机生成的其他玩家则在 **Start** 时通过服务端同步过来的name 设置为该玩家的 name

---
## 2. 代码：
```csharp
using UnityEngine;
using System.Collections;
using UnityEngine.Networking;

public class Player_Id : NetworkBehaviour
{
    [SyncVar]
    private string playerUniqueIdentity;
    private NetworkInstanceId playerNetId;
    private Transform myTransform;

    public override void OnStartLocalPlayer()
    {
        GetNetIdentity();
        SetIdentity();
    }

    public void Start()
    {
        if (myTransform.name == "" || myTransform.name == "Player(Clone)")
        {
            SetIdentity(); //这里设置唯一name的是本机客户端中的其他玩家，本机玩家已经在OnStartLocalPlayer设置好了
        }
    }

    public void Awake()
    {
        myTransform = transform;
    }

    /// <summary>
    /// 获取唯一id，生产一个唯一的name，并告诉服务端，同步到其他客户端
    /// </summary>
    void GetNetIdentity()
    {
        playerNetId = GetComponent<NetworkIdentity>().netId;
        CmdTellServerMyIdentity(MakeUniqueIdentiy());
    }

    /// <summary>
    /// 如果是其他玩家，则从网络中同步唯一name
    /// 如果是本机玩家，则直接自己生成唯一name
    /// </summary>
    void SetIdentity()
    {
        if (!isLocalPlayer)
        {
            myTransform.name = playerUniqueIdentity;
        }
        else
        {
            myTransform.name = MakeUniqueIdentiy();
        }
    }

    string MakeUniqueIdentiy()
    {
        string uniqueName = "Player_" + playerNetId.ToString();
        return uniqueName;
    }

    [Command]
    void CmdTellServerMyIdentity(string name)
    {
        playerUniqueIdentity = name;
    }
}
```

---
## 3. 测试  
![这里写图片描述](http://img.blog.csdn.net/20160916132800080)

ok，done