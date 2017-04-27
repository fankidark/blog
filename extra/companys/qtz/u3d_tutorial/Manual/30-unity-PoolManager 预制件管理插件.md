> - 插件版本 PoolManager v5.8.1

---
**预制件Prefab 管理池**

- 里面的核心代码就三个文件，也相对比简单。不好一点就是很多代码（多个类）挤在一个文件中。
- 使用也很简单，直接创建个空对象 **Pool_Shapes**，挂上 **Spawn Pool** 脚本组件
	![这里写图片描述](http://img.blog.csdn.net/20161022120611508)
	
	有个叫 **Shapers** 的生产池，里面有两个预制件队列 **Cube** 和 **Cylinder**
	
	比较核心的几个参数
	- Don't Destroy On Load：切场景时不销毁 **Pool_Shapes**
	- Send Message：这个是我加上去的，稍后说明
	- prefab：Cube预制件的引用
	- preload Amount：预生产预制件 实例对象 的个数
	- preload Time：生产实例前是否等待一些时间
	- limit Instances：是否限制 预制件队列 中的总量
	- cull Despawned：是否开启剔除实例功能
	- cull Above：预制件队列 中的实例对象超过这个数量时，开始剔除实例到这个数量
	- cull Delay：每剔除时的间隔时间
	- cull Max Pre Pass：执行一次剔除时，剔除的总量

---
**大致的结构**

**PoolMangaer** 中有个总池 **SpawnPoolsDict** （哈希表），里面放了N个 生产池 **SpawnPool**（列队），
**SpawnPool** 中放了N个 **PrefabPool** （预制件队列）
**PrefabPool** 中放了N个 预制件实例
所有的实例都是从 **PrefabPool** 队列中取出来的

---
**有几处认为可以改进的地方**

代码放到了git上，具体看 **日志**，传送门：[https://github.com/yangxuan0261/PoolManager.git](https://github.com/yangxuan0261/PoolManager.git)

1. 就是上面说的 **Send Message**，这个是我加上去的，用来控制是否在 Spawn or Despawn 时 Send 一个 **OnSpawn** or **OnDespawn** Message，默认是会send的，总所周知这个 **Send Message** api用了反射，效率并不高，所以加了这个控制

2. 在 Despawn 时，加了个 **Send Message** - **OnReset**，一般对象回收时，都需要重置一下数据

3. 在剔除实例的功能中，在一次剔除过程，剔除N个实例时（也就是这个参数 **cull Max Pre Pass**），加了分帧处理，防止剔除一个实例时该实例过大。也就加了行代码 `yield return null`

---
代码中 Spawn实例 和 Despawn实例
```
SpawnPool shapesPool = PoolManager.Pools[this.poolName]; //获取到对应的生产池

Transform inst = shapesPool.Spawn(this.testPrefab); //获取这个与testPrefab预制件相同的实例

shapesPool.Despawn(inst);  // 把这个实例从场景中取消绘制
```