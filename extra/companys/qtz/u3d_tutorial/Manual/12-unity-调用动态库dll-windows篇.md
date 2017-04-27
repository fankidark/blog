> - 其实就是c#调用dll
> - [Android篇](http://blog.csdn.net/yangxuan0261/article/details/52420833)


---
## 1、生成64位的dll

1. 用vs新建个工程，随便写个函数
	**NaviteCode.h**
	```cpp
		#ifndef __NativeCode_H__
		#define __NativeCode_H__
		
		#ifndef EXPORT_DLL
		#define EXPORT_DLL __declspec(dllexport) //导出dll声明
		#endif
		
		extern "C" {
			EXPORT_DLL int MyAddFunc(int _a, int _b);
		}
		#endif
	```
	
	**NaviteCode.cpp**
	```cpp
	#include "NaviteCode.h"
	
	extern "C" {
		int MyAddFunc(int _a, int _b)
		{
			return _a + _b;
		}
	}
	```
	
	extern "C" 就不用说明了吧，指定c编译器编译，后面打Android的so库也是用相同的代码


2. 修改vs导出配置，导出为64位Release的dll（应为用的是unity是64位的）  

	![这里写图片描述](http://img.blog.csdn.net/20160903011215814)
	
	![这里写图片描述](http://img.blog.csdn.net/20160903010922973)

3. 生成，就出来了这个 **NativeCode.dll**
	
	
---
## 2、拷贝 **NativeCode.dll** 到unity工程中

- unity存放动态库是由规则的不同平台放置的目录不同，官网传送门，[点我](https://docs.unity3d.com/Manual/PluginInspector.html)

- 所以按照规则，把 **NativeCode.dll** 放入 **Assets\Plugins\x86_64** 目录中

---
## 3、c#中调用一下，随便create个c#挂在场景的对象中
```csharp
using UnityEngine;
using System.Collections;
using System.Runtime.InteropServices; //DllImport需要的namespace

public class testDll : MonoBehaviour {

    [DllImport("NativeCode")] //这里就是调用的dll名字
    public static extern int MyAddFunc(int x, int y);

    // Use this for initialization
    void Start () {
        int ret = MyAddFunc(200, 200);
        Debug.LogFormat("--- ret:{0}", ret);
	}
}
```

done  
![这里写图片描述](http://img.blog.csdn.net/20160903012547207)

	