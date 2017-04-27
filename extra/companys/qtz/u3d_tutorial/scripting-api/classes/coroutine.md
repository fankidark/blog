### Coroutine

#### 描述
MonoBehaviour.StartCoroutine 返回一个Coroutine。Coroutine实例只能引用Coroutines不持有任何暴露的变量和函数。

```
public class ExampleClass : MonoBehaviour
{
    IEnumerator WaitAndPrint()
    {
        // suspend execution for 5 seconds
        yield return new WaitForSeconds(5);
        print("WaitAndPrint " + Time.time);
    }
    IEnumerator Start()
    {
        print("Starting " + Time.time);

        // Start function WaitAndPrint as a coroutine
        yield return StartCoroutine("WaitAndPrint");
        print("Done " + Time.time);
    }
}

--Starting 0
--WaitAndPrint 5.016304
--Done 5.016304
```

注：CSharp中在使用了yield时函数返回值一定要是IEnumerator
