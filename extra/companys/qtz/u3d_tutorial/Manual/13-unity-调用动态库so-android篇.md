> - 在Android环境下，调用so库
> - [Windows编](http://blog.csdn.net/yangxuan0261/article/details/52420755)

---
## 1、生产so库
- 用的代码和Windows篇的产不多，不过稍作修改，不需要导出dll了,
	**NaviteCode.h**
	```cpp
		#ifndef __NativeCode_H__
		#define __NativeCode_H__
		
		#if 0
		#define EXPORT_DLL __declspec(dllexport) //导出dll声明
		#else
		#define EXPORT_DLL 
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

- 新建一个文件夹native，把 **NaviteCode.h** 和 **NaviteCode.cpp** 都丢进去，同时新建两个文件 **Android.mk** 和 **Application.mk**（ndk编译需要的文件）
	**Android.mk**
	```makefile
	LOCAL_PATH := $(call my-dir)
	include $(CLEAR_VARS)
	
	LOCAL_MODULE     :=  NativeCode
	LOCAL_C_INCLUDES := $(LOCAL_PATH)
	LOCAL_SRC_FILES  := NaviteCode.cpp
	LOCAL_LDLIBS     := -llog -landroid
	LOCAL_CFLAGS	:= -DANDROID_NDK
	
	include $(BUILD_SHARED_LIBRARY)
	```
	生成的so全名是 **libNativeCode.so**，但在c#中加载还是按 **NativeCode** 加载
	
	**Application.mk**
	```makefile
	APP_STL := gnustl_static
	APP_CPPFLAGS := -frtti -std=c++11
	APP_PLATFORM := android-19
	APP_CFLAGS += -Wno-error=format-security
	APP_BUILD_SCRIPT := Android.mk
	APP_ABI := armeabi-v7a x86
	```
	指定生产的不同cpu架构的so库 **armeabi-v7a** 和 **x86**，（其实APP_CPPFLAGS 中没用到c++11的东西和运行时识别rtti，不过无妨）

- cmd中cd到native目录下，运行命令编译
	`ndk-build NDK_PROJECT_PATH=. NDK_APPLICATION_MK=Application.mk`  
	![这里写图片描述](http://img.blog.csdn.net/20160903031603900)

	生产两个so
	`C:\Users\Wilker\Desktop\native\libs\armeabi-v7a\libNativeCode.so`
	`C:\Users\Wilker\Desktop\native\libs\x86\libNativeCode.so`

- 附，在Linux中查看一下这个so中的函数
	把 **libNativeCode.so** 对到Linux中，输入命令 `# nm -D libNativeCode.so `  
	![这里写图片描述](http://img.blog.csdn.net/20160903034318383)
	

---
## 2、拷贝 **libs** 目录到 **Assets\Plugins\Android** 目录下
动态库不同平台的存放规则，请看官网，[https://docs.unity3d.com/Manual/PluginInspector.html](https://docs.unity3d.com/Manual/PluginInspector.html)

---
## 3、设置打包参数
**File->Build Settings**  
![这里写图片描述](http://img.blog.csdn.net/20160903032405786)

然后点击 **Player Settings**，设置详细参数  
![这里写图片描述](http://img.blog.csdn.net/20160903032817808)

其他的就没什么好设置的了，自己慢慢玩，这里只测试so用

---
## 4、生产apk  
![这里写图片描述](http://img.blog.csdn.net/20160903033021425)

---
## 5、测试以下
我是安装到 **夜神模拟器** 上测试的，然后用 **Android Studio** 调试  
![这里写图片描述](http://img.blog.csdn.net/20160903033145113)

从输出日志上看调用成功

done

---
## 6、遇到的小坑
1. Found plugins with same names x86_64
	出现原因可能是之前有导出过x84的dll，而且存放so也不是在Android（大意了没照官方规则）

2. **Android Studio** 连接不上 **夜神模拟器** 调试
	解决办法：
	1. 打开到夜神安装目录（如cd D:\Program Files\NOX\Nox\bin）
	2. 执行命令：nox_adb.exe connect 127.0.0.1:62001，连接模拟器，