> -工具版本：Unity 5.4.0f3， vs2015，VSTU2.3
> - 此方式适合unity5.2+版本，之前的版本直接去官网看看，[传送门](https://msdn.microsoft.com/en-us/library/dn940025.aspx)

---
VSTU2.3下载地址 : [https://blogs.msdn.microsoft.com/visualstudio/2016/07/14/visual-studio-tools-for-unity-2-3/](https://blogs.msdn.microsoft.com/visualstudio/2016/07/14/visual-studio-tools-for-unity-2-3/)
下载对应的版本：***Visual Studio 2015 Tools for Unity***

![这里写图片描述](http://img.blog.csdn.net/20160821014856373)

---
## 1、安装
安装前先 **关闭vs2015**，然后傻瓜式的点点点 ***vstu2015.msi***

--- 
## 2、配置
官网传送门 ： [点我](https://msdn.microsoft.com/en-us/library/dn940025.aspx) ，（喜欢的可以看看）

1. 修改外部编辑器是vs2015，unity菜单栏 ***Edit->Preferences***   
	![这里写图片描述](http://img.blog.csdn.net/20160821020007392)
	
2. 设置脚本可以Debugging， ***File->Build Settings***  
	![这里写图片描述](http://img.blog.csdn.net/20160821020817036)

3. 确认一下是否安装配置成功，***Help->About Unity***  
	![这里写图片描述](http://img.blog.csdn.net/20160821022039621)

---
## 3、开始调试
1. 双击c#脚本来打开vs2015，设置个断点，点击调试栏的 ***Attach to Unity***  
	![这里写图片描述](http://img.blog.csdn.net/20160821021455270)
	
2. 在unity中 ***ctrl+p*** 运行一下

3. done  
	![这里写图片描述](http://img.blog.csdn.net/20160821021648494)

---
## 4、**MonoBehavior** 的api提示1
![这里写图片描述](http://img.blog.csdn.net/20160910210600414)
通过使用 **MonoBehavior** 向导创建 MonoBehavior 方法的空定义

1. 在 Visual Studio 中，将光标放置在你可能要插入方法的位置，然后按 **Ctrl+Shift+M** 启动 MonoBehavior 向导。或者，如果要在已实现的方法后插入新方法，则可在稍后指定此操作；只需按 **Ctrl+Shift+M** 即可。

2. 选择要重载的方法。在“创建脚本方法”窗口中的“**Select method to create**”下，标记每个要重载的方法的名称旁的复选框。

3. 确保“**API version**”下拉列表中显示的版本与你正在使用的版本匹配。如果不匹配，将下拉列表中的值更改为要使用的版本。

4. 选择将插入方法的位置。默认情况下，将在光标位置处插入这些方法；如果要在其他位置插入，可以选择在类中已实现的任何方法后进行插入。若要选择其中一个位置，需将“**Insertion point**" 下拉列表中的值更改为所需位置。

5. 如果需要向导为所选方法生成注释，则标记“**Generate method comments**”复选框。这些注释是为了帮助你了解方法的调用时间以及其常规责任。

6. 选择“**OK**”按钮退出向导，并将方法插入代码中。
	 
---
## 5、**MonoBehavior** 的api提示2  
![这里写图片描述](http://img.blog.csdn.net/20160910211123906)

通过使用快速 **MonoBehavior** 向导创建 MonoBehavior 方法的空定义

1. 在 Visual Studio 中，将光标放置在要插入方法的位置，然后按 **Ctrl+Shift+Q** 启动快速 MonoBehavior 向导。与另一种 MonoBehavior 向导不同，使用此向导时，必须有意地放置光标，因为将始终在此位置插入新方法。

2. 确保“**Quick MonoBehaviours**”窗口右上角显示的  **Framework 版本** 与你正在使用的版本匹配。如果不匹配，将下拉列表中的值更改为要使用的版本。

3. 查找要重载的方法。在“**Quick MonoBehaviours**”窗口中，开始在文本框中键入方法的名称。将出现与输入的名称相匹配的方法的列表。

4. 选择要重载的方法。所需方法显示在列表中时，使用鼠标或箭头键选择它，然后按 Enter。如果它是列表中的唯一方法，可以直接 **按 Enter**。将该方法插入你的代码中。