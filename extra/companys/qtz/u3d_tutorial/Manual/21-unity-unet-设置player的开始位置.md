- 当客户端连接成功并验证通过时（你自己的验证逻辑，后面有这样的文章），会通知服务端spawn 一个该 **player** ，该 **player** 的 **transform** 可以自定义设置

---
## 使用
1. 直接在 **Scene** 中丢几个空对象，挂上一个 **Network Start Position** 组件
		![这里写图片描述](http://img.blog.csdn.net/20160924204833829)

2. 然后在 **Network Manager** 中设置随机方式，就可以随机这几个点 Spawn player 了
	![这里写图片描述](http://img.blog.csdn.net/20160924205047142)

	两种随机方式的解释，传送门：[点我](https://docs.unity3d.com/Manual/UNetManager.html)
	- **Random** : 在几个 startPosition 中随机选择
	- **Round Robin**：在几个 startPosition 中循环选择

---
## 源代码
1. **Network Start Position**
	在代码里面是直接 在 **Awake** 时就往 **Network Manager** 中丢进去了 **Transform**  
	
	```csharp
	 public void Awake()
	        {
	            NetworkManager.RegisterStartPosition(transform);
	        }
	```

2. **Network Manager**
	```csharp
	        static public void RegisterStartPosition(Transform start)
	        {
	            if (LogFilter.logDebug) { Debug.Log("RegisterStartPosition:" + start); }
	            s_StartPositions.Add(start);
	        }
	```

	两种选择方式的代码
	```csharp
	        public Transform GetStartPosition()
	        {
	            // first remove any dead transforms
	            if (s_StartPositions.Count > 0)
	            {
	                for (int i = s_StartPositions.Count - 1; i >= 0; i--)
	                {
	                    if (s_StartPositions[i] == null)
	                        s_StartPositions.RemoveAt(i);
	                }
	            }
	
	            if (m_PlayerSpawnMethod == PlayerSpawnMethod.Random && s_StartPositions.Count > 0)
	            {
	                // try to spawn at a random start location
	                int index = Random.Range(0, s_StartPositions.Count);
	                return s_StartPositions[index];
	            }
	            if (m_PlayerSpawnMethod == PlayerSpawnMethod.RoundRobin && s_StartPositions.Count > 0)
	            {
	                if (s_StartPositionIndex >= s_StartPositions.Count)
	                {
	                    s_StartPositionIndex = 0;
	                }
	
	                Transform startPos = s_StartPositions[s_StartPositionIndex];
	                s_StartPositionIndex += 1;
	                return startPos;
	            }
	            return null;
	        }
	```
	如果想自定义 **start position** 的话，可以继承并重写这几个方法
