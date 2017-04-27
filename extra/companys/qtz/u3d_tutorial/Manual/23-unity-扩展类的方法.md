> 再不使用继承的方式的情况下，扩展引擎类的方法

---
也是看了 **DOTween** 插件，才知道原来可以这样扩展引擎类。
一个 **DOTween** api的使用
```csharp
transform.DOMoveX(10, 3f).OnComplete(()=> { Debug.Log("Move Complete"); });
```

---
1. 新建一个静态类 **MyExt** ，加上一个静态方法 **MyExtRot**（要扩展的方法），方法  的第一个形参 **target** 就是要扩展的类 **Transform** ，加上一个 namespace **MyExtension**  比较好管理

	```csharp
		namespace MyExtension { 
		public static class MyExt {
		    public static int MyExtRot(this Transform target, float angle)
		        {
		            target.localRotation = Quaternion.Euler(new Vector3(0, angle, 0));
		            return 123;
		        }
		    }
}
	```

2. 使用。加上一个 **namespace**，vs就会提示扩展的方法

	```csharp
	public class ExtCube : MonoBehaviour {
	
		// Use this for initialization
		void Start () {
	        int ret = transform.MyExtRot(30f);
	        Debug.LogFormat("--- ExtCube, ret:{0}", ret);
		}
		
		// Update is called once per frame
		void Update () {
		    
		}
	}
```

3. done
