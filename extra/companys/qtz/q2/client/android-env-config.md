## Android 环境的配置

> Q2项目采用的是ant编译的方式，如果想使用gradle编译请自行查阅gradle相关的资料，本教程主要针对window版本，mac版本会简单的提及。

#### 环境变量的配置

#### 安装配置Java

###### windows

去官网下载适合的Java版本，目前Q2使用的是Java7，下载的时候请注意是32位和64位的版本。目前提供了64位版本。

[Java7_windows_x86_64](http://192.168.0.166:8200/jdk-7u21-windows-x64.exe)

安装好了配置环境变量JAVA_HOME，例如路径D:\Program Files\Java\jdk1.7.0_21，具体的路径请参考你安装的路径。

配置PATH环境变量，找到环境PATH，在末尾添加";%JAVA_HOME%\bin;"，注意PATH末尾有";"的只需要添加"%JAVA_HOME%\bin;"即可。

配置好之后，然后打开命令行，输入java，如果有java相关的信息出现说明已经安装成功。

###### mac 

版本使用brew search java，找到对应的Java版本 然后在使用brew install JavaXXX即可。


#### 安装配置ant

###### windows

ant使用免安装的版本，[点我下载](http://192.168.0.166:8200/apache-ant-1.9.0.zip)，下载完成之后解压，然后放到你需要的目录。然后找到环境变量PATH，在末尾添加";D:\Program Files (x86)\apache-ant-1.9.0\bin;"，这里的目录是你的解压之后放置的目录，另外第一个";"参照上面处理。

配置好之后，然后打开命令行，输入ant，如果有ant相关的信息出现说明已经安装成功。

###### mac

输入brew install ant即可。


#### 安装配置android sdk

###### windows

下载android sdk，解压， 然后放到存放的目录。 设置PATH环境变量，添加"E:\work\android_env\sdk\tools"和"E:\work\android_env\sdk\platform-tools"路径，注意这里是你自己的路径。

配置好之后，然后打开命令行，依次输入android和adb，如果有相关的信息出现说明已经安装成功。

64位下载地址： [adt-bundle-windows-x86_64-20140321](http://192.168.0.166:8200/adt-bundle-windows-x86_64-20140321.zip)

###### mac

输入brew install android-sdk 安装，安装完成之后请输入android，然后出现界面安装相应版本的sdk即可。

#### 初体验

进入项目的projs.android目录下面的tools，调用update_android_project.py脚本，根据提示选择，完成之后进入工程目录，ant clean  & ant debug，到此android相关的环境已经配置好了。