> 使用 Render Texture 和 Camera 制作小地图（MinMap）

---
效果图
![这里写图片描述](http://img.blog.csdn.net/20161017150059483)

---
- 思路：  
	1. 增加一个 **摄像机A**（Camera），将 摄像机A 所看到的画面 绘制在一张 **贴图B**（Render Texture）上
	2. UI Canvas 上创建一个 **Raw Image** ，在 Raw Image 组件上贴上 **贴图B**

- 摄像机 详解
	- 传送门：[http://blog.csdn.net/st75033562/article/details/52330241](http://blog.csdn.net/st75033562/article/details/52330241)
	- 官网：[https://docs.unity3d.com/Manual/class-Camera.html](https://docs.unity3d.com/Manual/class-Camera.html)

---
## 1. 创建环境
创建一个 环境如下，地面、绿方(**IgnoreCube**)、绿圆(**Shpere**)、红方(**Cube**)
然后创建一个空对象 **MinMap_Camera**，并挂上组件 **Camera**，把 **MinMap_Camera** 放在 **Cube** 下作为 **子节点**（跟随 Cube 移动）

![这里写图片描述](http://img.blog.csdn.net/20161017151148216)

---
## 2. 创建一个用来存放 **MinMap_Camera** 中的摄像机所看到画面的 贴图（Render Texture）
1. 直接在 **Project** 窗口，右键 **Create** -> **Render Texture**，命名为 **MinMap**

2. **MinMap_Camera** 的 **Camera** 组件中指定这个 **Render Texture**

	![这里写图片描述](http://img.blog.csdn.net/20161017152319726)

- 官网 Render Texture：[https://docs.unity3d.com/Manual/class-RenderTexture.html](https://docs.unity3d.com/Manual/class-RenderTexture.html)

---
## 3. 创建绘制这个 **MinMap** 的 UI
直接在场景中创建一个 **Raw Image** 对象，并在 **Raw Image** 组件中指定这个 **MinMap**

 ![这里写图片描述](http://img.blog.csdn.net/20161017152601384)

基本就ok了
done

---
- 为啥 **MinMap** 中看不到 绿方(**IgnoreCube**) 呢

	![这里写图片描述](http://img.blog.csdn.net/20161017152859457)

	因为新加了一个 **Layer**，把绿色方块指定这个 Layer ，并在 **摄像机** 中忽略这个 **Layer**  

	1. 创建新 Layer
		**Edit** -> **Project Settings** -> **Tags And Layers**
		新加一个 Layer ： **MyIgnore**
		![这里写图片描述](http://img.blog.csdn.net/20161017153308216)
		
	2. 绿色放开指定这个 Layer
	![这里写图片描述](http://img.blog.csdn.net/20161017153415966)
	
	3. 摄像机中忽略这个 Layer，取消勾选 **MyIgnore** 即可
	![这里写图片描述](http://img.blog.csdn.net/20161017153533997)
	
	
---
- 另附一个比较好的教程
	传送门：[http://blog.theknightsofunity.com/implementing-minimap-unity/](http://blog.theknightsofunity.com/implementing-minimap-unity/)