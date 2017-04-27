> 在模型的动作中，插入个事件回调，常用于碰撞框开启和关闭、播放特效等
> animator动画状态机模式（[传送门](http://blog.csdn.net/yangxuan0261/article/details/52334151)）和animation模式下加入事件

---
## 1、animator模式
1. 首先在模型上挂个脚本  
	![这里写图片描述](http://img.blog.csdn.net/20160827154629689)
	
	代码如下
	```csharp
	public class charEvents : MonoBehaviour {

    public void ActionEvent1(string _arg1)
    {
        Debug.LogFormat("--- arg3:{0}", _arg1);
    }
}
    ```
    
2. 双击状态，在 **Events** 中添加事件，输入方法名 **ActionEvent1**，随便个参数  
	![这里写图片描述](http://img.blog.csdn.net/20160827155041022)

3. 注意事项
	- 只能在 **ActionEvent1** 声明0或1个参数，否则会报错
	- Failed to call AnimationEvent ActionEvent1 of class charEvents.
The function must have either 0 or 1 parameters and the parameter can only be: string, float, int, enum, Object and AnimationEvent.

---
## 2、animation模式
在该模式下，会发现在模型的动画都是 **read-only**，不能编辑及插入事件，而自定义的animation就可以  
![这里写图片描述](http://img.blog.csdn.net/20160827155629224)

解决方法，
直接把对应的动画重新拖进对应的动画中，就可以编辑加事件回调了，具体加事件的方法 [点我](http://blog.csdn.net/yangxuan0261/article/details/52304448#t4)  
![这里写图片描述](http://img.blog.csdn.net/20160827155947362)