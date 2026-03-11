---
title: "Unity协程"
date: 2022-02-09T22:49:20+08:00

tags:
  - C#
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---

## yield

yield 实质是一个语法糖，它让程序员能够更方便的去使用迭代器，通过 yield 你可以直接使用迭代器操作而不需要去实现 IEnumerable 和 IEnumerator，也不需要一个临时的 Collection 来完成迭代。

yield 有两种格式声明

```
yield return <expression>;
yield break;
```

```
using System.Collections;
using UnityEngine;

namespace UniRxLesson
{
  public class YieldExample : MonoBehaviour
  {
    private void Start()
    {
      foreach (var empty in FiveTimes())
      {
        Debug.Log("A");
      }
    }

    private  IEnumerable FiveTimes()
    {
      for (var i = 0; i < 5; i++)
      {
        yield return string.Empty;
      }
    }
  }
}
```

输出结果为

```
A
A
A
A
A
```

每一次 foreach 的循环中都会调用迭代器方法，当 yield return 被执行到时，表达式的值会被返回，同时当前的函数的上下文信息被保存下来。下一次循环执行之时会重新从上一次停止的位置继续执行。你也可以使用 yield break 来终止迭代过程。

yield 关键字除了支持返回 IEnumerable 之外，还支持返回 IEnumerator。

```
using System.Collections;
using UnityEngine;

namespace UniRxLesson
{
  public class YieldExample : MonoBehaviour
  {
    private void Start()
    {
      foreach (var empty in FiveTimes())
      {
        Debug.Log("A");
      }

      var fourTimes = FourTimes();

      while (fourTimes.MoveNext())
      {
        Debug.Log("B");
      }
    }

    private IEnumerable FiveTimes()
    {
      for (var i = 0; i < 5; i++)
      {
        yield return string.Empty;
      }
    }

    private IEnumerator FourTimes()
    {
      for (var i = 0; i < 4; i++)
      {
        yield return string.Empty;
      }
    }
  }
}
```

输出结果为

```
A
A
A
A
A
B
B
B
B
```

### yield 解释说明

我们把包含 yield 语句的方法或属性称为迭代块。

```
public IEnumerator GetEnumerator()
{
  yield return 1;
  yield return 2;
  yield return "枚举器";
}
```

这个语句块在编译时将会编译成一个 yield 类型，其中包含一个状态机。yield 类型实现 IEnumerator 和 IDisposable 接口属性和方法。

```
class IEnumeratorTest
{
  public IEnumerator GetEnumerator()
  {
    yield return 1;
    yield return 2;
    yield return "枚举器";
  }
}
```

则会编译成如下类似的代码。

```
public class IEnuemratorTest
{
  public IEnumerator GetEnumerator()
  {
    return new Enumerator(0);
  }

  public class Enumerator:IEnumerator<object>,IEnumerator,IDisposable
  {
    private int state;
    private object current;
    public Enumerator(int state)
    {
      this.state = state;
    }

    public object Current
    {
      get 
      {    
        return current;
      }  
    }
    
    public void Dispose()
    {
    }
    
    public bool MoveNext()
    {
      switch(state)
      {
        case 0:
          state++; // 改变当前迭代诶值为下一个元素位置
          current = 1; // 当前迭代位置的对象
          return true;
        case 1:
          state++; // 改变当前迭代位置为下一个元素位置
          current = 2; // 当前迭代位置的对象
          return true;
        case 2:
          state++; // 改变当前迭代位置为下一个元素位置
          current = "枚举器"; // 当前迭代位置的对象
          return true;
      }
      return false;
    }
  
    public void Reset()
    {
    }
  }
}
```

枚举器其实就是通过每次调用 MoveNext() 方法，来改变集合中当前元素位置，来一个个遍历元素。类似通过更改下标来获取元素。yield 关键字其实是实现 IEnumerator 等接口如 MoveNext() 方法的编译器自动编译的关键字。

### **yield return种类**

> yield return 0 or yield return null: 程序在下一帧中从当前位置继续执行；
>
> yield return 1,2,3,......: 程序等待1，2，3...帧之后从当前位置继续执行；
>
> yield return new WaitForSeconds(n): 程序等待n秒后从当前位置继续执行；
>
> yield new WaitForEndOfFrame(): 在所有的渲染以及GUI程序执行完成后从当前位置继续执行；
>
> yield new WaitForFixedUpdate(): 所有脚本中的FixedUpdate()函数都被执行后从当前位置继续执行；
>
> yield return WWW: 等待一个网络请求完成后从当前位置继续执行；
>
> yield return StartCoroutine(): 等待一个协程执行完成后从当前位置继续执行；
>
> yield break: 直接从当前位置跳出函数体，回到函数的根部；

## 协程（coroutine）

**官方解释**：在主程序运行的同时，开启另外一段逻辑处理，来协同当前程序的执行；

**个人理解：**函数中调用另一个函数，另一个执行完成回来继续往下执行该函数；

StartCoroutine方法属于MonoBehaviour中API，和Invoke一样必须继承MonoBehaviour才能使用；

### 通过 Unity 的 Update() 模拟协程。

修改上面代码，不使用 while 循环或者 foreach 关键字枚举元素。我们将在 Update() 实时刷新来枚举所有元素。

原理就是上面所说的每次 MoveNext() 方法后，改变当前的位置为下一个元素位置。

```
using System.Collections;
using UnityEngine;

namespace UniRxLesson
{
  public class CoroutineExample : MonoBehaviour
  {
    private Update2CoroutineTest mUpdate2CoroutineTest = new Update2CoroutineTest();

    IEnumerator mE;

    public CoroutineExample()
    {
      mE = mUpdate2CoroutineTest.GetEnumerator();
    }

    private void Update()
    {
      if (mE.MoveNext())
      {
      }
    }

    class Update2CoroutineTest
    {
      public IEnumerator GetEnumerator()
      {
        Debug.Log("协程:" + 1);
        yield return 0;
        Debug.Log("协程:" + 2);
        yield return 0;
        Debug.Log("协程:" + "枚举器");
        yield return 0;
      }
    }
  }
}
```

输出结果为:

```
协程:1
协程:2
协程:枚举器
```

### 协程

现在我们用 StartCoroutine（）方法启动协程。

代码如下：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CoroutineTest : MonoBehaviour
{
  void Start()
  {
    CoroutineJsTest coroutineJsTest = new CoroutineJsTest();
    StartCoroutine(coroutineJsTest.GetEnumerator());
  }
}

public class CorotuineJsTest
{
  public IEnumerator GetEnumerator()
  {
    Debug.Log("协程:" + 1);
    yield return 0;
    Debug.Log("协程:" + 2);
    yield return 0;
    Debug.Log("协程:" + "枚举器");
    yield return 0;
  }
}
```

输出结果如下:

```
协程:1
协程:2
协程:枚举器
```

从上面可以看出，在 update 方法模拟协程和使用 Unity 自带 StartCoroutine() 方法启动协程效果差不多。看来自 Unity 实现的 StartCoroutine() 启动协程和我们 Update() 模拟是一样的。但是也不确定到底是不是通过类似 Update() 方法实现的。反编译 UnityEngine.dll 程序集也没有找到具体实现方法。。。。。但是唯一确定的一点就是 Unity 也是通过枚举一步步运行程序块的。

类似 Update() 模拟协程，每次遇到 yield return，就执行 yield 类型的 MoveNext() 方法，改变当前迭代位置为下一个元素位置。等待下一次 MoveNext() 方法调用。

StartCoroutine() 方法会不停的调用 MoveNext() 方法（这样就类似于 foreach）。直到枚举结束。

但是注意的是，yield return 后面跟的值除了 Unity 自带的累（如：new WaitForSeconds（0.2f)。继承自 YieldInstruction） 和协程语句块（返回值为 IEnumerator 的方法），其他值没有意义（yield return 0 和 yield return null） 其实都是一样的，只是遇到 yield return 就做相同处理，不会去处理后面跟的值了）。

### **协程终止**

```c#
StopCoroutine (string methodName)		//终止指定的协程
StopAllCoroutine() 				//终止所有协程
```

### 总结

Coroutine 其实是一个 迭代器模式 + 定时器的一种实现。

与我们的 UniRx 的 Observable + Scheduler + LINQ 非常类似。

关于协程的原理我们理解到这里就够了。

![1.jpg](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022975.jpeg)

![2.jpg](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022065.jpeg)
