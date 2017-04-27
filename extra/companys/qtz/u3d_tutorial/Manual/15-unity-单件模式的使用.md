> - 在游戏中很多地方都用了全局唯一的单件，像什么技能管理器、buff管理器等等...
> - Google了一下不外乎都是在unity中 **实例** 一个对象gameobject，往这个gameobject身上挂上所有的Mgr组件，然后设置这个gameobject在切场景的时候不让销毁（**DontDestroyOnLoad**）

---
ps: 有些人喜欢在一个总控制器里加上一个dictionary来保存所有的mgr，但是我想如果使用了unity的 **组件模式** 的话，直接就可以通过它的api **GetComponent** 来获取对应的mgr

---
## 1. 先来个基础的mgr- **BaseMgr**，让所有的mgr都继承它，相同的特性都写在里面
```csharp
public class BaseMgr : MonoBehaviour {

    public virtual void Awake()
    {
        Debug.Log("BaseMgr.Awake");
    }

    public int mCounter = 0;

    public virtual void Print()
    {
        Debug.LogFormat("--- name:{0}, counter:{1}", this.GetType().Name, mCounter);
        ++mCounter;
    }

    public virtual void Clear()
    {
        Debug.LogFormat("--- name:{0}, Clear", this.GetType().Name);
    }
}
```

---
## 2. 具体的mgr- **SkillMgr** 、 **BuffMgr**
```csharp
public class SkillMgr : BaseMgr 
{
    public override void Awake()
    {
        //base.Awake();
        Debug.Log("--- SkillMgr.Awake");
    }
}
```

```csharp
public class BuffMgr : BaseMgr
{
    public override void Awake()
    {
        //base.Awake();
        Debug.Log("--- BuffMgr.Awake");
    }
}
```

---
## 3. 最后是总控制器 **GameMgr**，同样也是继承 **BaseMgr** 挂在gameobject身上 ，但负责更多的职责
```csharp
public class GameMgr : BaseMgr {

    public static string gNodeName = "GameMgrNode";
    private static GameObject gGameObj = null;
    private static GameMgr gGameMgr = null;

    /// <summary>
    /// 游戏开始时实例宿主对象gameobject，全局唯一，切场景不被销毁
    /// </summary>
    public static void Init()
    {
        GameObject obj = Resources.Load("Prefabs/GameMgrNode") as GameObject;
        obj = Instantiate(obj);
        obj.name = gNodeName;
        DontDestroyOnLoad(obj);
    }

    public static GameObject Obj
    {
        get { return gGameObj; }
    }

    public static GameMgr Ins
    {
        get { return gGameMgr; }
    }

    public override void Awake()
    {
        base.Awake();
        gGameObj = gameObject;
        gGameMgr = this;
    }

    public void Start()
    {
        Debug.Log("--- GameMgr.Start");
    }

    /// <summary>
    /// 可能要求清除顺序
    /// </summary>
    public override void Clear()
    {
        BaseMgr[] comps = gameObject.GetComponents<BaseMgr>();
        for (int i = 0; i< comps.Length; ++i)
        {
            if (!(comps[i] is GameMgr)) //跳过自己，最后请自己
            {
                comps[i].Clear();
            }
        }
        base.Clear();
    }

    public void Quit()
    {
        Debug.Log("--- GameMgr.Quit");
        Clear();

        DestroyObject(gGameObj);
    }
}
```

---
## 4、制作个空对象的预制件，把所有mgr都挂上去（当然你也可以代码动态挂上去）  
![这里写图片描述](http://img.blog.csdn.net/20160910013956789)

---
## 5. 游戏开始的入口脚本，直接挂在摄像机上
```csharp
public class Main : MonoBehaviour {

    // Use this for initialization
    void Start () {
        GameObject go = GameObject.Find(GameMgr.gNodeName);
        if (null == go)
        {
            GameMgr.Init();
            GameMgr.Ins.Start();
        }
	}
}
```

---
## 6、测试下  
![这里写图片描述](http://img.blog.csdn.net/20160910010046787)