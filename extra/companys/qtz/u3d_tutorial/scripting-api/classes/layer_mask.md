### LayerMask



#### 描述


#### 变量

- value 返回layer mask的int值。

#### 静态方法

- GetMask 传入layer名字的集合，返回layer的mask值。
	
	```
	Debug.Log(LayerMask.GetMask("UserLayerA", "UserLayerB"));
	```
	
- LayerToName 传入layer的index值，返回layer的名字。
	
	```
	Debug.Log(LayerMask.LayerToName(1));
	```
	
- NameToLayer 传入一个layer名字，返回layer的索引。
	
	```
	Debug.Log(LayerMask.NameToLayer("TransparentFX"));
	```

#### 操作符

- LayerMask 精确的转换int到LayerMask

	```
int num = 1;
LayerMask mask = num;
Debug.Log (mask);
	```

