> - 在场景中添加障碍物，需要点Bake重新烘焙出新的 **导航网格**，不是运行时。
> - 如果在运行时添加障碍物动态Bake出新的导航网格，就需要使用 **Nav Mesh Obstacle**
> - 官网传送门：[https://docs.unity3d.com/Manual/nav-CreateNavMeshObstacle.html](https://docs.unity3d.com/Manual/nav-CreateNavMeshObstacle.html)

---
效果图

![这里写图片描述](http://img.blog.csdn.net/20161015105838154)

---
## 创建障碍物
创建个Cube对象 **Obstacle1**

1. 身上挂一个组件 **Nav Mesh Obstacle**

	![这里写图片描述](http://img.blog.csdn.net/20161015110321040)

2. 再挂一个刚体组件 **Rigidbody**，并约束位置和旋转（因为不希望被撞飞）

	![这里写图片描述](http://img.blog.csdn.net/20161015110519776)

然后可以制作成预制件**Prefab**，在运行时动态 **Instantiate** 实例化出来，**寻路网格** 会重新生成