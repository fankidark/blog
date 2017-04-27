> - 在寻路计算中，每个连通点都有个代价属性，在实施A*时根据代价估算决定这个这点是否丢进个行走路点队列中。

---
For examples:

1. 如果陆地网格的代价是1，河流代价是5，从 **A点** 到 **B点**，同时可以同过连通点 **C点**（在陆地）和 **D点**（在河流），那么计算时代价估算时 **C点** 小于 **D点**，那么 **C点** 就会丢进行走路点队列中

2. 如果有道门是禁止通过的，可以标记这个区域 **Door**，在 行走对象 的寻路代理中设置计算区域忽略这个 **Door**，则计算连通点的时候，直接忽略到这个区域所有连通点。

2  的效果可以通过 1 的代价值调到很大来达到

---
效果图

- 红色区域 代价值为100 
	![这里写图片描述](http://img.blog.csdn.net/20161015120408652)

- 红色区域 代价值为1，等于 蓝色区域 的代价值
	![这里写图片描述](http://img.blog.csdn.net/20161015120421512)

---
## 1. 增加自定区域 **Door** 和 对应的代价值
在 **Navigation** 窗口中，增加个 **Door**，代价值 **100**

![这里写图片描述](http://img.blog.csdn.net/20161015120802169)

前三个是系统內建值，获取这些值需要 **位移** 一下才能获取到正确的值，下面有说

问题参考：[http://answers.unity3d.com/questions/323005/unity-navmeshsampleposition-failing.html](http://answers.unity3d.com/questions/323005/unity-navmeshsampleposition-failing.html)

---
## 2. 创建对象，标记为 **Door**
1. 在场景中创建一个Cube对象，并 **选中**

2. 在 **Navigation** 窗口的 **Object** 中标记该区域为 **Door** 

	![这里写图片描述](http://img.blog.csdn.net/20161015121537266)

完成，然后就通过调整代价值来看到不同的寻路效果

---
如果想 行走对象中，禁止行走 **Door** 区域，可以在 行走对象 的 **Nav Mesh Agent** 组件中不够选这个 **Door**，默认是 **Everything**
![这里写图片描述](http://img.blog.csdn.net/20161015122023471)

---
获取当前的区域是否是 **Door** 区域
代码如下
```csharp
    public void Update()
    {
        NavMeshHit navMeshHit;
        if (NavMesh.SamplePosition(agent.transform.position, out navMeshHit, 0.8f,
            1 << NavMesh.GetAreaFromName("Door")))
        {
            Debug.LogFormat("--- agent currMask:{0}", navMeshHit.mask);
        }
    }
```
