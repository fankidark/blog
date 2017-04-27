> - 寻路模块，Navigation
> - 和ue4中的很相似 [http://blog.csdn.net/yangxuan0261/article/details/50272423](http://blog.csdn.net/yangxuan0261/article/details/50272423)

---
效果图

![这里写图片描述](http://img.blog.csdn.net/20161014183954785)

---
- 官网地址：[https://docs.unity3d.com/Manual/nav-NavigationSystem.html](https://docs.unity3d.com/Manual/nav-NavigationSystem.html)

- 打开寻路设置窗口
**Window** -> **Navigation**

	![这里写图片描述](http://img.blog.csdn.net/20161014183933050)

---
## 1. 建立地形，生成寻路网格

- 随便 Create **Plane**、 **Cube** ，然后在 **Navigation** 窗口点击 **Bake** 生成寻路网格。这种静态的增加障碍物，动态增加在这里 [http://blog.csdn.net/yangxuan0261/article/details/52821833](http://blog.csdn.net/yangxuan0261/article/details/52821833)

![这里写图片描述](http://img.blog.csdn.net/20161014184507179)

可行通区域就出来了
![这里写图片描述](http://img.blog.csdn.net/20161015102845829)

---
## 2. 创建一个行走的 **Actor**(黑色眼镜那个) 和几个行走点（绿色球）

---
## 3. 给 **Actor** 添加行走
1. 在 **Actor** 身上挂个 **Nav Mesh Agent**

	![这里写图片描述](http://img.blog.csdn.net/20161015103411252)

2. 添加个行走控制的脚本

	![这里写图片描述](http://img.blog.csdn.net/20161015103848428)

	**NavMove.cs**
	```csharp
	using UnityEngine;
	using System.Collections;
	using System.Collections.Generic;
	
	public class NavMove : MonoBehaviour
	{
	
	    public NavMeshAgent agent;
	    public Transform[] destPos = new Transform[] { };
	    int currentPoint = 0;
	
	    // Use this for initialization
	    void Start()
	    {
	        agent.Stop();
	        StartCoroutine(Move());
	    }
	
	    IEnumerator Move()
	    {
	        //enable agent updates
	        agent.Resume();
	        agent.updateRotation = true;
	
	        agent.SetDestination(destPos[currentPoint].position);
	        yield return StartCoroutine(WaitForDestination());
	
	        StartCoroutine(NextWaypoint());
	    }
	
	    IEnumerator WaitForDestination()
	    {
	        yield return new WaitForEndOfFrame();
	        while (agent.pathPending)
	            yield return null;
	        yield return new WaitForEndOfFrame();
	
	        float remain = agent.remainingDistance;
	        while (remain == Mathf.Infinity || remain - agent.stoppingDistance > float.Epsilon
	        || agent.pathStatus != NavMeshPathStatus.PathComplete)
	        {
	            remain = agent.remainingDistance;
	            yield return null;
	        }
	
	        Debug.LogFormat("--- PathComplete to pos:{0}", currentPoint);
	    }
	
	    IEnumerator NextWaypoint()
	    {
	        currentPoint++;
	        currentPoint = currentPoint % destPos.Length;
	        Transform next = destPos[currentPoint];
	        agent.SetDestination(next.position);
	        yield return StartCoroutine(WaitForDestination());
	
	        StartCoroutine(NextWaypoint());
	    }
	}
	```

	这种使用 **Coroutine** 移动到目的点 的方式是在 **Simple Waypoint System** 插件中的 **navMove.cs** 中看到的。利用 **Coroutine** 来达到 **分帧执行** 使得代码变得非常简洁。  
	
done