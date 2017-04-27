> - unity5 中新加入了个 **unet** 模块，使用的命名空间是 **using UnityEngine.Networking;**
> - 做多人在线的游戏最头疼的问题就是同步问题，而这个unet模块就是其中一个解决方案，可以不提太关心网络，就可以制作出一个多人在线的demo。

---
- unet里的同步概念就是有n个客户端，其中一个同时也作为服务端。身兼服务端的客户端已挂，其他客户单也就掉线。
- 就像cs一样，建房间的人一掉线，其他客户端就全都动不了了；不是建房间的客户端掉线，并不会影响到其他人。

---
***未完待续……***








---
照着教程做的demo，传送门：[Github](https://github.com/yangxuan0261/TestUNet)

---
## 参考文章
[http://blog.csdn.net/column/details/unity-unet.html](http://blog.csdn.net/column/details/unity-unet.html)

- 视频教程（需要科学上网）
[https://www.youtube.com/watch?v=pNUsVNwAdZE&list=PLwyZdDTyvucz5D5NqhaUu0VxCTB9678_O](https://www.youtube.com/watch?v=pNUsVNwAdZE&list=PLwyZdDTyvucz5D5NqhaUu0VxCTB9678_O)

	[https://www.youtube.com/watch?v=sKZJRXMJL30&list=PLwyZdDTyvucyAeJ_rbu_fbiUtGOVY55BG](https://www.youtube.com/watch?v=sKZJRXMJL30&list=PLwyZdDTyvucyAeJ_rbu_fbiUtGOVY55BG)