> 连接服务器成功时，会在客户端生产一个player，这个player 的 **prefab** 可以在 **Network Manager** 中指定，如果不去重写一些方法的话，所有 player 生成的都是同一个 **prefab**  资源。

---
- 继承 **Network Manager** 重写 **OnServerAddPlayer** 方法，要自定义的**prefab** 一定要丢进注册列表中，也就是这个
	![这里写图片描述](http://img.blog.csdn.net/20160924211716145)

	代码中是丢在这个容器中
	`[SerializeField] List<GameObject> m_SpawnPrefabs = new List<GameObject>();`
	但使用时是用
	`public List<GameObject> spawnPrefabs { get { return m_SpawnPrefabs; }}`
	
	```csharp
	    /// <summary>
	    /// 重写实例化对象，使用自定义的prefab，这个prefab必须在spawnPrefabs列表中
	    /// </summary>
	    /// <param name="conn"></param>
	    /// <param name="playerControllerId"></param>
	    public override void OnServerAddPlayer(NetworkConnection conn, short playerControllerId)
	    {
	        GameObject go = null;
	        if (spawnPrefabs.Count > 0)
	            go = spawnPrefabs[spawnPrefabs.Count - 1];
	        else
	            go = playerPrefab;
	
	        var player = (GameObject)GameObject.Instantiate(go, GetStartPos().position, Quaternion.identity);
	        NetworkServer.AddPlayerForConnection(conn, player, playerControllerId);
	    }
	```

---
- 源码
	```csharp
	        void OnServerAddPlayerInternal(NetworkConnection conn, short playerControllerId)
	        {
	            if (m_PlayerPrefab == null)
	            {
	                if (LogFilter.logError) { Debug.LogError("The PlayerPrefab is empty on the NetworkManager. Please setup a PlayerPrefab object."); }
	                return;
	            }
	
	            if (m_PlayerPrefab.GetComponent<NetworkIdentity>() == null)
	            {
	                if (LogFilter.logError) { Debug.LogError("The PlayerPrefab does not have a NetworkIdentity. Please add a NetworkIdentity to the player prefab."); }
	                return;
	            }
	
	            if (playerControllerId < conn.playerControllers.Count  && conn.playerControllers[playerControllerId].IsValid && conn.playerControllers[playerControllerId].gameObject != null)
	            {
	                if (LogFilter.logError) { Debug.LogError("There is already a player at that playerControllerId for this connections."); }
	                return;
	            }
	
	            GameObject player;
	            Transform startPos = GetStartPosition();
	            if (startPos != null)
	            {
	                player = (GameObject)Instantiate(m_PlayerPrefab, startPos.position, startPos.rotation);
	            }
	            else
	            {
	                player = (GameObject)Instantiate(m_PlayerPrefab, Vector3.zero, Quaternion.identity);
	            }
	
	            NetworkServer.AddPlayerForConnection(conn, player, playerControllerId);
	        }
	```