> 编辑出一个动画文件（.anim），多个物体或ui使用
> 可视化编辑动画，就不需要用DOTween插件了

---
效果图（这里只做了scale x的动画）  
![这里写图片描述](http://img.blog.csdn.net/20160824213343225)

--- 
## 1、创建个ui做动画载体
先创建个ui-image，然后在image身上挂个 **Animation组件**  
![这里写图片描述](http://img.blog.csdn.net/20160824214422847)

---
## 2、创建动画
打开动画编辑器, ***Window->Animation***，或者 ***ctrl+6***  
![这里写图片描述](http://img.blog.csdn.net/20160824213811418)

点击 **Create**， 创建个动画 **anim111.anim** 文件（保存的是动画相关数据），然后拖至image的Animation组件中    
![这里写图片描述](http://img.blog.csdn.net/20160824214850510)

---
## 3、编辑动画
添加编辑对象的一个属性   
![这里写图片描述](http://img.blog.csdn.net/20160824215011840)

编辑曲线，点击 **curves** （比较平滑），这里只编辑了scaleX，  
![这里写图片描述](http://img.blog.csdn.net/20160824215339906)

---
## 4、代码中播放
```cpp
Animation myAnim = GetComponent<Animation>();
myAnim.Play("anim111");
```

---
## 5、添加触发事件
image对象脚本中加入
```cpp
void MyEvent1() {
    Debug.Log("--- myEvent1");
}
```

在红色区域中右键  
![这里写图片描述](http://img.blog.csdn.net/20160824215833865)

![这里写图片描述](http://img.blog.csdn.net/20160824215916253)
 