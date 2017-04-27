> - 像 **Behavior Designer** 那样，把插件的核心代码导出成 **dll**，然后给别人使用
> - 插件用 **c#** 写，就不需要像 **c/c++** 那样担心跨平台问题，直接导出 **dll** 给编辑器用就行了

---
## 1. 新建 c# 类库
- 打开 **vs2015**，新建个 **c#** 类库

	![这里写图片描述](http://img.blog.csdn.net/20161022112405733)

- 设置unity使用的 .Net 库，前提时需要安装好一个vs的unity插件 **VSTU**（安装教程：[http://blog.csdn.net/yangxuan0261/article/details/52264833](http://blog.csdn.net/yangxuan0261/article/details/52264833)）
	
	**右键工程** -> **属性**

	![这里写图片描述](http://img.blog.csdn.net/20161022114631763)

---
## 2. 引入 Unity 库
- 右键 **引用** -> **添加引用**

	![这里写图片描述](http://img.blog.csdn.net/20161022112535864)

- 添加 **UnityEngine.dll**，路径unity安装路径下可以找到：D:\Unity\Editor\Data\Managed，（如果是编辑器dll，则选择 **UnityEditor.dll**）

	![这里写图片描述](http://img.blog.csdn.net/20161022112741425)

---
## 3. 随便写点代码，并生成
在 **Class1.cs** 文件中写
```csharp
namespace DLLTest //一般加入命名空间来区分这个插件库
{
    public class Class1
    {
        private int a;
        public int total
        {
            get { return 5; }
        }

        public int Add(int a, int b)
        {
            return a + b;
        }

        public static int Clamp(int val, int min, int max)
        {
            return UnityEngine.Mathf.Clamp(val, min, max); //调用unity引擎的api
        }

    }
}
```

然后生成，在 DLLTest\DLLTest\bin\Debug 路径下可以找到生成dll：**DLLTest.dll**

---
## 4. Unity添加 **DLLTest.dll** 并使用
1. 把 **DLLTest.dll** unity工程的 **Assets** 目录下

2. 然后随笔写个脚本测试

	```csharp
	using UnityEngine;
	using System.Collections;
	using DLLTest;
	
	public class testDll : MonoBehaviour {
		void Start () {
	
	        Class1 c = new Class1();
	        Debug.LogFormat("--- add:{0}", c.Add(2,4));
	        Debug.LogFormat("--- total:{0}", c.total);
	        Debug.LogFormat("--- clamp:{0}", Class1.Clamp(1, 3, 5));
	    }
	}
	```

3. done

	![这里写图片描述](http://img.blog.csdn.net/20161022113745400)
