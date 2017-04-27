> - 插件 **Behavior Designer 1.5.7**
> - 和ue4的行为树极其相似。[ue4中的AI和行为树(BluePrint版)](http://blog.csdn.net/yangxuan0261/article/details/50272365)

---
效果图  
![这里写图片描述](http://img.blog.csdn.net/20161012170229405)

---
- 导入插件 **Behavior Designer 1.5.7**，打开编辑面板
**Tools** -> **Behavior** -> **Designer** -> **Editor**

	![这里写图片描述](http://img.blog.csdn.net/20161013003426201)
	
	-  Behavior：这个行为树的设置
	- Tasks：行为树的所有节点Tasks（少用，一般直接在编辑器右键即可）
	- Variables：行为树的 **通用变量**，用过这个行为树所有节点间变量的 **通信**（比如A节点的是寻敌任务，寻找到敌人Target后把这个Target设置到通用变量中，B节点的任务从这个通用变量中拿到Target执行攻击任务，在ue4中就是 **BlackBoard**）
	- Inspector：点击某个节点后，显示节点的详细信息，可以设置 **public** 变量

- 具体各个节点的描述看这个篇文章，写的很全，几乎都是官网翻译过来，[http://www.jianshu.com/p/64b5fe01fb1c](http://www.jianshu.com/p/64b5fe01fb1c)
- 官网文档：[http://www.opsive.com/assets/BehaviorDesigner/documentation.php](http://www.opsive.com/assets/BehaviorDesigner/documentation.php)

---
- 效果图的设计是：（markdown的流程图语法有点不熟就不用了）
没有敌人 -> 半径范围内寻找敌人 -> 没找到就发呆几秒 -> 范围内随机移动

## 1. 给 **gameobject** 穿件 behavior tree
1. 选中场景中的一个对象 **Capsule**，然后在行为树编辑器右键  **Add Behavior Tree**

	![这里写图片描述](http://img.blog.csdn.net/20161013005255133)
	
	- Instant；勾选这个选项意思同一帧执行这个节点，不勾选则下一帧执行

---
## 2. 创建一个 **SharedTransform**类型 的通用变量 **target**
 ![这里写图片描述](http://img.blog.csdn.net/20161013010341632)

---
## 3. 创建各个节点如下
![这里写图片描述](http://img.blog.csdn.net/20161013005810000)

1. **Conditional Evaluator** 评估节点：判定是否有 Target，里面的 **Condictional** 脚本是  

	**HasTarget.cs**
	```csharp
	using UnityEngine;
	namespace BehaviorDesigner.Runtime.Tasks
	{
	    [TaskDescription("Returns success as soon as the event specified by eventName has been received.")]
	    [HelpURL("http://www.opsive.com/assets/BehaviorDesigner/documentation.php?id=123")]
	    [TaskIcon("{SkinColor}HasReceivedEventIcon.png")]
	    [TaskCategory("My")]
	    public class HasTarget : Conditional
	    {
	        public override void OnStart()
	        {
	
	        }
	
	        public override TaskStatus OnUpdate()
	        {
	            SharedTransform target = (SharedTransform)Owner.GetVariable("target");
	            if (target.Value == null)
	            {
	                Debug.Log("--- HasTarget, false");
	                return TaskStatus.Failure;
	            }
	            else{
	                Debug.Log("--- HasTarget, true");
	                return TaskStatus.Success;
	            }
	        }
	    }
	```

2. **Search Target** 条件节点：用来寻找敌人，脚本是 
	**SearchTarget.cs**

	```csharp
	using System.Collections;
	using UnityEngine;
	namespace BehaviorDesigner.Runtime.Tasks.Basic.UnityTransform
	{
	    [TaskCategory("My")]
	    public class SearchTarget : Conditional
	    {
	        public Transform mTarget = null;
	        public float mRadius = 2f;
	
	        public override void OnStart()
	        {
	            Collider[] hits = Physics.OverlapSphere(transform.position, mRadius);
	            for(int i = 0; i < hits.Length; ++i)
	            {
	                if (hits[i].transform.CompareTag("Player"))
	                {
	                    mTarget = hits[i].transform;
	                    Owner.SetVariableValue("target", mTarget);
	                    break;
	                }
	            }
	        }
	
	        public override TaskStatus OnUpdate()
	        {
	            return mTarget ? TaskStatus.Success : TaskStatus.Failure;
	        }
	    }
	}
	```

3. **Wait** 动作节点：默认自带的 **Action** 节点
右键 **Add Task** -> **Actions** -> **Wait**

4. **Range Move** 动作节点：范围内随机移动，脚本是
	**RangeMove.cs**

	```csharp
	using UnityEngine;
	using System.Collections;
	using DG.Tweening;
	namespace BehaviorDesigner.Runtime.Tasks
	{
	    [TaskDescription("Returns success as soon as the event specified by eventName has been received.")]
	
	    [TaskCategory("My")]
	    [TaskIcon("Assets/bdIcon/aaa.jpg")]
	    public class RangeMove : Action
	    {
	        public bool mIsOver = false;
	        public float mRange = 2f;
	        public float mTotalTime = 2f;
	
	        public override void OnStart()
	        {
	            float x = Random.Range(-mRange, mRange);
	            float z = Random.Range(-mRange, mRange);
	            Vector3 dir = transform.position + new Vector3(x, 0, z); //dest world position
	            transform.LookAt(dir);
	            transform.DOLocalMoveX(x, mTotalTime).OnComplete(()=> { mIsOver = true; });
	            transform.DOLocalMoveZ(z, mTotalTime);
	        }
	
	        public override TaskStatus OnUpdate()
	        {
	            return mIsOver ? TaskStatus.Success : TaskStatus.Running;
	        }
	    }
	}
	```

---
## 备注
- 运行时各个节点只有public变量才会被 reset 成默认的值
	OnReset方法只是在编辑器中的inspector中，点击reset时调用，并不是运行时被调用
