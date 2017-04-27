> - 类似于AOI，在超过范围的物体不绘制，也不同步。
> - 减少客户端不必要的绘制和计算

---
效果图  
![这里写图片描述](http://img.blog.csdn.net/20160922221849403)

---
## 1. 简单使用
在UNET中使用很简单，只需要在不销毁的实例中挂上一个 **NetworkProximityChecker** 组件，调整可视范围 **Vis Range** 即可  
![这里写图片描述](http://img.blog.csdn.net/20160922220510932)

里面的实现是 通过物理碰撞框来找到可视对象
`var hits = Physics.OverlapSphere(transform.position, visRange);`
所以需要 **被可视** 的对象要加上一个 **Collider** 组件  
![这里写图片描述](http://img.blog.csdn.net/20160922220903609)

---
## 2. 自定义使用
新建一个类继承 **NetworkProximityChecker**，重写 **OnRebuildObservers** 方法
有些时候会把 **Collider** 组件丢在某个 **节点A**  的父节点 **节点B**上，而需要 **被可视** 的节点不是A而是B，就要下面这样干了
```
    public override bool OnRebuildObservers(HashSet<NetworkConnection> observers, bool initial)
    {
        if (forceHidden)
        {
            // ensure player can still see themself
            var uv = GetComponent<NetworkIdentity>();
            if (uv.connectionToClient != null)
            {
                observers.Add(uv.connectionToClient);
            }
            return true;
        }

        // find players within range
        switch (checkMethod)
        {
            case CheckMethod.Physics3D:
                {
                    var hits = Physics.OverlapSphere(transform.position, visRange);
                    foreach (var hit in hits)
                    {
                        // (if an object has a connectionToClient, it is a player)
                        //var uv = hit.GetComponent<NetworkIdentity>();           //  < -----DEFAULT
                        var uv = hit.GetComponentInParent<NetworkIdentity>(); //    <----- MODIFIED
                        if (uv != null && uv.connectionToClient != null)
                        {
                            observers.Add(uv.connectionToClient);
                        }
                    }
                    return true;
                }
                
            case CheckMethod.Physics2D:
                {
                    var hits = Physics2D.OverlapCircleAll(transform.position, visRange);
                    foreach (var hit in hits)
                    {
                        // (if an object has a connectionToClient, it is a player)
                        //var uv = hit.GetComponent<NetworkIdentity>();          //   < -----DEFAULT
                        var uv = hit.GetComponentInParent<NetworkIdentity>(); //    <----- MODIFIED
                        if (uv != null && uv.connectionToClient != null)
                        {
                            observers.Add(uv.connectionToClient);
                        }
                    }
                    return true;
                }
        }
        return false;
    }
```

相对于源码只改了一行代码就是
`var uv = hit.GetComponent<NetworkIdentity>();`
改为
`var uv = hit.GetComponentInParent<NetworkIdentity>();`