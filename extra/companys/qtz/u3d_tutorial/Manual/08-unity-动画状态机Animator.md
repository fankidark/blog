> unity可以用两种方式控制动画
> 1. Animations，这种方式简单，直接 **Play("Idle")** 或者 **CorssFade("Idle")** 就可以播放动画
> 2. Animator，5.x之后推荐使用这种方式，因为里面可以加上混合动画，让动画切换更加平滑

---
## 1、导入模型及动作，做成预制件（perfab）
直接把整个模型文件夹拷入对应位置，unity会自动import进来

1. 把模型拖进场景
2. 然后报贴图拖到模型的身上   
![这里写图片描述](http://img.blog.csdn.net/20160827100615536)

3. 将 **Hierarchy** 中，新建一个 **空对象xiaoGO**，把模型丢到xiaoGO作为子节点，然后把xiaoGO拖入 **Project** 中做成预制件  
![这里写图片描述](http://img.blog.csdn.net/20160827102748249)

---
## 2、将模型骨骼转换成unity中的 **Humanoid**  
![这里写图片描述](http://img.blog.csdn.net/20160827101831433)

- 第4步点击之后，有个弹窗，点 **Save** 即可。
- 如果转换成功 **Configure** 前面显示 v ，失败则显示 x
- 第5步则更新相关的动画

---
## 3、添加状态机控制器
添加控制器脚本 **animCtrl2**  
![这里写图片描述](http://img.blog.csdn.net/20160827103144058)

为模型aisake(aisakeGO对象下的aisake节点)添加 **Animator** 组件，拖入 **animCtrl2** 到 **Controller** ，选择人物镜像**Avatar**（转换为Humanoid之后才会出现在选择列表中）  
![这里写图片描述](http://img.blog.csdn.net/20160827103619986)

---
## 4、编辑状态机控制器
- 编辑状态机控制器

1. 双击 **animCtrl2**，会出来一个 **Animator** 面板，拖入三个动作作为状态（第一个拖入的作为默认状态，不过可以右键 **Default State** 设置其他状态为默认状态）
2. 右键 **AnyState**，**Make Transition** 连接到三个状态
3. 添加状态控制参数 **AnimState**，是个整形（一般来说用枚举，不过这里没枚举类型，但可以强转成int）  
	![这里写图片描述](http://img.blog.csdn.net/20160827104105848)

- 编辑切换状态的条件
	点击连线，添加个条件，这个条件只会显示刚才添加的状态控制参数 **AnimState**，设置等于0是切到这个状态  
	![这里写图片描述](http://img.blog.csdn.net/20160827104739471)

- 取消勾选 **Can Transition To Self**，不然动画会出现抖动  
	![这里写图片描述](http://img.blog.csdn.net/20160827170618893)

- 其他两个一样，设置不同的条件即可

关于 AnyState中的Transitions，看这里 [http://blog.csdn.net/yangxuan0261/article/details/52334359](http://blog.csdn.net/yangxuan0261/article/details/52334359)  
![这里写图片描述](http://img.blog.csdn.net/20160827105439701)

---
## 4、代码中控制状态
加个按钮去控制，按钮脚本
```csharp
public class btnCtrl : MonoBehaviour {

    private int mAnimIndex = 0;
    private int mAnimTotal = 3;

    void Start () {
        OnClicked1();
    }
	
    public void OnClicked1()
    {
        GameObject go1 = GameObject.Find("aisakeGO");
        GameObject go2 = GameObject.Find("xiaoGO");
        Animator ator1 = go1.GetComponentInChildren<Animator>();
        Animator ator2 = go2.GetComponentInChildren<Animator>();

        if(ator1 && ator2)
        {
            //Debug.LogFormat("--- has ator1 && ator2, animIndex:{0}", mAnimIndex % mAnimTotal);
            ator1.SetInteger("AnimState", mAnimIndex % mAnimTotal); //设置状态控制参数值，用来切状态
            ator2.SetInteger("AnimState", mAnimIndex % mAnimTotal);
        }
        mAnimIndex++;
    }
}
```

---
## 5、done   
![这里写图片描述](http://img.blog.csdn.net/20160827110621264)

---
## 注意事项
1. 动作循环。不然如果没有下个状态切换，直接停止动作  
	![这里写图片描述](http://img.blog.csdn.net/20160827110903687)

2. **Animator** 中的 **Avater** 没有对应模型，那是因为没有模型没有转换成unity中的 **Humanoid**，参照第二步

3. 使用 **Animator组件** 后就不需要 **Animation组件**

4. Has Exit Time，如果勾选了，则表示在该动作完成后才允许切换，但是一般我们要的都是立即切换，所以这里 **不要勾选**  
	![这里写图片描述](http://img.blog.csdn.net/20160827113357198)

5. 取消勾选 **Can Transition To Self**，不然动画会出现抖动

<br>
参考
官方文档忘了保存了，其他的
[http://answers.unity3d.com/questions/864734/using-humanoid-animations-with-generic-avatar.html](http://answers.unity3d.com/questions/864734/using-humanoid-animations-with-generic-avatar.html)
[http://answers.unity3d.com/questions/956031/legacy-animations-and-unity-5-whats-the-deal.html](http://answers.unity3d.com/questions/956031/legacy-animations-and-unity-5-whats-the-deal.html)
[http://www.ceeger.com/Manual/AnimationSoloMute.html](http://www.ceeger.com/Manual/AnimationSoloMute.html)

youtube里的相关很好视频教程（能不能科学上网就看你自己了）
[https://www.youtube.com/watch?v=WSkI3Px20Q4](https://www.youtube.com/watch?v=WSkI3Px20Q4)



