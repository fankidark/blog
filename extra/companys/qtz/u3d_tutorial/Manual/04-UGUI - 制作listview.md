> 应用场景- 排行榜、任务列表、邮件列表 等等，真尼玛比cocos方便多了

---
效果图  
![这里写图片描述](http://img.blog.csdn.net/20160820153555285)

---
## 1、编号一个item  
![这里写图片描述](http://img.blog.csdn.net/20160820153801429)

---
## 2、创建一个垂直布局
创建一个空对象(Create Empty)，挂一个垂直布局组件(Add Component->Vertical Layout Group)  
![这里写图片描述](http://img.blog.csdn.net/20160820154211322)

---
## 3、把item丢进布局里面
如果直接把item丢进layout里，会发现item会被填满布局，为防止item拉伸变形，需要创建一个空对象，把item挂在空对象之下，然后再把空对象丢进布局里，这样拉伸的就是空对象，item则不会变形。（ps：水平布局和网格布局的处理方式也是相同）  
![这里写图片描述](http://img.blog.csdn.net/20160820154859387)

然后复制多个Gameobject（item的父对象）  
![这里写图片描述](http://img.blog.csdn.net/20160820155200498)  

![这里写图片描述](http://img.blog.csdn.net/20160820155214308)

---
## 4、创建滚动控件，把布局丢进去作为content
1. 创建一个空对象，往里面挂几个组件（image，scoll rect，mask）  
     ![这里写图片描述](http://img.blog.csdn.net/20160820155335481)

2. 把布局拖到scroll rect中的content，取消勾选Horizontal（因为要的是垂直滚动），取消mask组件的show mask（不需要绘制image中的纹理，透明就好）

3. done  
![这里写图片描述](http://img.blog.csdn.net/20160820161110960)

 ![这里写图片描述](http://img.blog.csdn.net/20160820160010740)
    
