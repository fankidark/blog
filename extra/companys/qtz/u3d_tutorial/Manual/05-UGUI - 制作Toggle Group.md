> 应用场景- 难度选择

---
效果图  
![这里写图片描述](http://img.blog.csdn.net/20160820163331156)

---
## 1、 制作单个toggle
1. 创建一个空对象Toggle，挂image和toggle组件，image绘制背景![这里写图片描述](http://img.blog.csdn.net/20160820163845980) ，
2. 创建个image对象，绘制选中对象![这里写图片描述](http://img.blog.csdn.net/20160820164108817) ，
3. 把Toggle拖进身上的Toggle组件的Target Graphic中，把image对象拖进Graphic中(选中即绘制image)
![这里写图片描述](http://img.blog.csdn.net/20160820163638759)

4. 然后复制多分出来
	![这里写图片描述](http://img.blog.csdn.net/20160820164555257)

---
## 2、创建toggle group对象
1. 创建一个空对象对象Difficult，挂上一个toggle group组件
2. 全部Toggle对象挂在Difficult下作为子节点，并把Diffcult拖进全部Toggle对象中。（选中全部Toggle，把Difficult对象拖到Toggle组件的Group中）
	![这里写图片描述](http://img.blog.csdn.net/20160820165032946)

3. done