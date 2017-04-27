> - 在两个 **不连通** 的寻路网格间，如果想在这两个网格间行走，就需要添加跳跃的连接 **Off-mesh Link**
> - 官网地址：[https://docs.unity3d.com/Manual/nav-CreateOffMeshLink.html](https://docs.unity3d.com/Manual/nav-CreateOffMeshLink.html)

---
效果图

![这里写图片描述](http://img.blog.csdn.net/20161015112054688)

---
1. 创建两个跳跃点（那两个红色球）

2. 在其中一个身上挂一个组件 **Off-mesh Link**，然后把两个球分别放进 **Start**、**End** （顺序无关）

	![这里写图片描述](http://img.blog.csdn.net/20161015112529553)
