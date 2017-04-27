> 在动画状态机中某个状态时，播放某个动作，加入一个曲线，根据动画播放的时间来取曲线对应的值
> 应用场景比如一个刀光的 **拖尾** 特效

---
效果图  
![这里写图片描述](http://img.blog.csdn.net/20160827235216403)


---
## 1、添加曲线
1. 在 **Animator** 面板中增加一个参数 **MyCv**(这个就是用曲线控制的参数)
2. 双击某个状态，在 **Curves** 中添加一个曲线，命名为 **MyCv** (恩，控制的就是它了)
3. 如图  
	![这里写图片描述](http://img.blog.csdn.net/20160827235802816)

---
## 2、代码中获取这个参数
```csharp
private Animator mAtor;
public float mCv;

mAtor = GetComponentInChildren<Animator>();
mCv = mAtor.GetFloat("MyCv");
```

---
## 3、添加一个拖尾特效
在模型骨骼上添加一个空对象**TrailSlot**，**TrailSlot** 挂一个 **TrailRender** 组件，随便创建一个颜色的材质球丢进去，调一下参数，如图：  
![这里写图片描述](http://img.blog.csdn.net/20160828000354985)

---
## 4、用曲线值控制 **TrailRender** 组件的 **Time** 值
代码如下
```csharp
public class charCtrl : MonoBehaviour {

	//private Animation anim;
    private Animator mAtor;
    public float mCv; //用来测试曲线是否变化
    private TrailRenderer mTrail;

    void Start () {
        mAtor = GetComponentInChildren<Animator>();
        mTrail = GetComponentInChildren<TrailRenderer>();
    }

    void Update () {
        mCv = mAtor.GetFloat("MyCv");
        mTrail.time = mCv;
    }
}
```
<br>
done