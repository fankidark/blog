> - 使用的是工具是 **unity5.4**，**Android Studio 2.0**(后面简称 **AS**)
> - 网上找到的教程大部分都是导出jar给unity调用，极少是用AS导出 **aar** 包给unity调用
> - 用AS导出jar需要修改一下 **build.gradle** 文件，但这里主要是说导出 aar包
> - 实现unity掉java里面的代码有两种方式
	>  1. 第一种方式，自己写个java类，jni需要的 中转站cpp、 Android.mk、Application.mk，然后用ndk打成so库（无疑巨麻烦，官网例子直接可以下，传送门
	[http://docs.unity3d.com/Manual/PluginsForAndroid.html](http://docs.unity3d.com/Manual/PluginsForAndroid.html)）
	> 2. 第二种方式，直接使用unity分装好的类 **AndroidJavaClass** 等，无需自己打so库（巨方便，本文将的就是这种）

---
因为现在eclipse已经停止维护了，官方推荐是用AS来构建Android app，然后导出 **aar** 包给unity使用
可以用cocos2dx的方式去理解，也是用一个 **MainActivity** 去继承 unity封装好的 **UnityPlayerActivity** ，当前应用就是的主线程就是跑在 **MainActivity** 中

---
## 1、使用AS构建一个app工程
1. File->New->New Project
2. 包名 **Package Name** 一定要和unity中打包参数 **Bundle Identifier** 中的包名一致，这里用 **com.test.yangx**
3. 设置 **mini sdk**，
4. 选个 **Empty Activity**
5. 默认的 **MainActivity**即可，然后 **Finish**

---
## 2、导入unity的jar到AS工程中
- 在unity5中，在 
**D:\Unity\Editor\Data\PlaybackEngines\AndroidPlayer\Variations\mono\Release\Classes** 的路径下有个 **classes.jar**
把这个 **classes.jar** 丢进AS工程的 **libs** 中  
![这里写图片描述](http://img.blog.csdn.net/20160904000707439)

- 工程引用这个 **classes.jar**，
	- 右键 工程-> **Open Module Settings**  
	- ![这里写图片描述](http://img.blog.csdn.net/20160904001110725)

---
## 3、编写 **MainActivity** 代码
完整代码如下
```java
package com.test.yangx;

import android.app.AlertDialog;
import android.os.Vibrator;
import android.os.Bundle;
import android.widget.Toast;

import com.unity3d.player.UnityPlayer;
import com.unity3d.player.UnityPlayerActivity;

public class MainActivity extends UnityPlayerActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

    }

    public String ShowDialog(final String _title, final String _content){

        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
                builder.setTitle(_title).setMessage(_content).setPositiveButton("Down", null);
                builder.show();
            }
        });

        return "Java return";
    }

    // 定义一个显示Toast的方法，在Unity中调用此方法
    public void ShowToast(final String mStr2Show){
        // 同样需要在UI线程下执行
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(getApplicationContext(),mStr2Show, Toast.LENGTH_LONG).show();
            }
        });
    }


    //  定义一个手机振动的方法，在Unity中调用此方法
    public void SetVibrator(){
        Vibrator mVibrator=(Vibrator)getSystemService(VIBRATOR_SERVICE);
        mVibrator.vibrate(new long[]{200, 2000, 2000, 200, 200, 200}, -1); //-1：表示不重复 0：循环的震动
    }

    // 第一个参数是unity中的对象名字，记住是对象名字，不是脚本类名
    // 第二个参数是函数名
    // 第三个参数是传给函数的参数，目前只看到一个参数，并且是string的，自己传进去转吧
   public void callUnityFunc(String _objName , String _funcStr, String _content)
   {
       UnityPlayer.UnitySendMessage(_objName, _funcStr, "Come from:" + _content);
   }
}
```

---
## 4、修改 **AndroidManifest.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.test.yangx">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true">
        <activity android:name="com.test.yangx.MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <!-- 一定要加上这句 -->
            <meta-data android:name="unityplayer.UnityActivity" android:value="true" />
        </activity>
    </application>
    
</manifest>
```

---
## 5、修改 **build.gradle**
1. `apply plugin: 'com.android.application'`
修改为
`apply plugin: 'com.android.library'`
这样才能导出一个 **aar** 包

2. 删除掉这句代码 `applicationId "com.test.yangx"`

3. 完整代码
	```
	apply plugin: 'com.android.library'
	
	android {
	    compileSdkVersion 23
	    buildToolsVersion "23.0.3"
	
	    defaultConfig {
	        minSdkVersion 19
	        targetSdkVersion 23
	        versionCode 1
	        versionName "1.0"
	    }
	    buildTypes {
	        release {
	            minifyEnabled false
	            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	        }
	    }
	}
	
	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])
	    compile 'com.android.support:appcompat-v7:23.3.0'
	    compile files('libs/classes.jar')
	}
	```

---
## 6、生产 **aar** 包
**Build->Build APK**
成功会在 **app\build\outputs\aar** 目录下出现一个 **app-debug.aar** 包
然后用 解压软件打开这个包，删掉 **libs** 下的 **classes.jar** (没错，就是之前重unity中拷过来的)
因为unity打包时会重新把自带的 **classes.jar** 打进去，如果不删掉它会打包报错，冲突

然后这就是最终需要的 **aar** 包

然后把这个 **aar**包 和 **AndroidManifest.xml** 文件 丢进unity的 **Assets\Plugins\Android** 目录下

## 7、写个c# 测试

```csharp
using UnityEngine;
using System.Runtime.InteropServices;
using UnityEngine.UI;

public class testDll : MonoBehaviour {

    private Text mText;

    void Start()
    {
        //int ret = MyAddFunc(200, 200);
        //Debug.LogFormat("--- ret:{0}", ret);
        mText = GameObject.Find("MsgText").GetComponent<Text>();
    }

    public void MyShowDialog()
    {
        // Android的Java接口  
        AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
        AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject>("currentActivity");
        // 参数  
        string[] mObject = new string[2];
        mObject[0] = "Jar4Android";
        mObject[1] = "Wow,Amazing!It's worked!";
        // 调用方法  
        string ret = jo.Call<string>("ShowDialog", mObject);
        setMsg(ref ret);
    }

    public void MyShowToast()
    {
        AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
        AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject>("currentActivity");
        jo.Call("ShowToast", "Showing on Toast");
    }
	
	/// <summary>
    /// 测试 unity->java->unity
    /// </summary>
    public void MyInteraction()
    {
        AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
        AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject>("currentActivity");
        jo.Call("callUnityFunc", "U2J2U", "BeCallFunc", "yangx");
    }

    public void BeCallFunc(string _content)
    {
        setMsg(ref _content);
    }

    private void setMsg(ref string _str)
    {
        mText.text = _str;
    }
}
```

---
## 8、打包apk
修改测试参数
要和AS工程中的 **Package Name** 一致
**mini sdk** 一致  
![这里写图片描述](http://img.blog.csdn.net/20160904003203768)

---
## 9、done
装在模拟器上测试以下  
![这里写图片描述](http://img.blog.csdn.net/20160904003532038)

---
## 10、遇到的小坑
1. unity导出apk
      File->Build Run
      当导出apk时，可能遇到下面的问题：

         Unable to find unity activity in manifest. You need to make sure orientation attribute is set to fullSensor manually.
      需在AndroidManifest中增加一行：
`<meta-data android:name="unityplayer.UnityActivity" android:value="true" />`

2. 删除AndroidManifest.xml 中app的主题，否则unity打包apk是关联的主题会报找不到错误，应该是有些库没打进去（有待研究）

3. 删除生产的arr文件里的libs下的classes.jar，这个是之前从u3d中拷过去的，打包时会重新打进去，所以要删除，不删除打包会报错