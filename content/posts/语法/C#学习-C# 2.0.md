---
title: C#学习-C# 2.0
tags:
  - C#
  - 学习
categories:
  - C#
date: 2021-12-05 20:27:45
cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191917907.png
---

## C# 2.0 版本

在 C# 2.0 版本提供的特性如下:

- 泛型
- 分部类型（partial）
- 匿名方法
- 可以为 null 的值类型
- 迭代器
- 协变和逆变
- getter/setter 单独可访问性
- 方法组转换（委托）
- 静态类
- 委托推断

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191928011.png)

## 泛型

最常用的 List<T> 就是泛型的一个应用。

C# 文档地址：[泛型](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/generics/index)

### 泛型 API 的设计

要设计一个泛型 的 API 非常简单，看以下代码中的 GetTypeName 方法。

```c#
using UnityEngine;

namespace QFramework.Master
{
	public class ReflectionExample : MonoBehaviour
	{
		string GetTypeName<T>()
		{
			var type = typeof(T);

			return type.Name;
		}
	
		private void Awake()
		{
			Debug.Log(GetTypeName<string>());
		}
	}
}
// 输出结果:
// String
```

泛型可以把一个类或者一个方法，当做一个模板，而这个模板所需要填充的内容则是定义的各种类型，所以泛型可以最大限度地实现代码的复用。

### C# 什么要实现泛型？

C# 实现泛型这个特性，肯定是为了解决开发者遇到的实际问题的，要想知道泛型具体解决了什么问题，那么就要回过头看看没实现泛型之前的 C# 语言有什么样的问题？

在 C# 实现泛型之前，我们只能使用 ArrayList 来充当不定长的数组。

而 ArrayList 所存储的元素都需要转换成 object 类型，其中也包括值类型，而值类型转换成 object 需要做装箱操作，这样会造成性能的消耗。

这是第一个问题，就是性能问题。

而 ArrayList 所存储的 object 类型，是类型不安全的，为什么说是类型不安全呢？

因为，ArrayList 可以存储任何类型，可以同时存储 int 和 string 甚至是自己创建的对象，也就是说你从 ArrayList 中拿到一个元素，那么这个元素的类型是不确定的，没有约束的。

那么有没有什么方式避免 ArrayList 存储值类型造成的性能消耗么，同时也能约束存储的类型。

答案就是泛型。

在 C# 添加了泛型这个特性之后，List<T> 就成为了 ArrayList 的替代，List<T> 不管是存储值类型还是引用类型，它的性能都是非常高的，而一旦确定了 T 是什么类型，那么 List 中只能添加类型为 T （或者继承于 T）的元素。

所以，泛型类型可以最大限度地重用代码之外，还可以保护类型安全性以及提高性能。

这就是泛型存在的意义。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191928908.png)

### 泛型约束

说到泛型，有一个重要的特性就是泛型约束。

先写一个泛型约束的示例，如下:

```c#
using UnityEngine;

namespace QFramework.Master
{
	public class GenericExample : MonoBehaviour
	{
		/// <summary>
		/// 只能获取值类型的名字
		/// </summary>
		/// <typeparam name="T"></typeparam>
		/// <returns></returns>
		string GetTypeName<T>() where T : struct
		{
			var type = typeof(T);

			return type.Name;
		}
	
		private void Awake()
		{
			Debug.Log(GetTypeName<int>());
		}
	}
}
// 输出结果:
// int
```

在定义了 GetTypeName<T> 方法后，还加上了一个 where T : struct。

意思是给 T 类型必须要继承 struct，而只要继承了 struct 那么说明 T 类型是值类型。

在 C# 中 int、float 等都是值类型，所以就可以将 int 类型传给 GetTypeName<T> 中的 T。

在刚开始接触泛型的时候，觉得泛型用得不多，但是当写了很多通用工具后，发现泛型真的是一个很方便、易用的语言特性。

一般情况下，我们通过方法、类、继承的方式来达到代码的复用，但是加上了泛型之后，可以更进一步地达到代码的复用。

关于泛型约束更全面且深入地使用，建议阅读微软的官方指南:[泛型约束](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters)

### 泛型使用

使用泛型这个概念比较多的地方是 一些底层的、通用的代码。

比如用来创建单例的模板，泛型这个概念一开始接触的时候会觉得非常复杂，但是一旦用它设计过几个通用工具后，就会觉得泛型是一个非常好用的**设计工具**。

在一般的项目开发的过程中，写界面逻辑 或者 游戏的业务逻辑时，基本上不需要我们去设计泛型的 API，但是像 List、Dictionary 等 .Net 提供的数据结构中，要求我们最起码要掌握泛型 API 的基本使用。

而如果我们在项目开发中负责写一些通用工具的时候，那么就有很大的概率需要我们自己设计泛型 API 了。

需要我们设计的泛型 API 的大致类型如下:

- 泛型接口
- 泛型类
- 泛型方法
- 泛型委托
- 泛型的反射
- 等等

像泛型委托、泛型的反射、泛型的方法 这些设计起来难度不是那么高。

而泛型类 和 泛型接口 则涉及到了 继承 的问题，那么理解起来可能会有一点难度。

这里举个例子，如下:

```c#
using UnityEngine;

namespace QFramework.Master
{
		// T 要保证继承 Base<T>
		// T 是泛型
    public interface Base<T> where T : Base<T>
    {
        T Value { get; set; }
    }

		// 在 SameClass 里 T 就是 SomeClass
    public class SomeClass : Base<SomeClass>
    {
        public SomeClass Value { get; set; }
    }
}
```

以上这段代码，算是泛型 API 设计中比较绕的设计方式了，如果能看懂那是最好了，如果看不懂也没关系，因为这样的代码一般都是出现在一些框架或者库的底层。

如果你打算自己写一个框架或者库，那么泛型部分最起码要能够自己写出来以上这样的代码才行。

### 协变

先看如下代码:

```c#
using System.Collections.Generic;
using UnityEngine;

namespace QFramework.Master
{
	public class GenericExample : MonoBehaviour
	{
		public class BaseClass
		{

		}

		public class SubClass : BaseClass
		{

		}
	
		private void Awake()
		{
			// 这样写是合法的（但是性能很差）
			BaseClass[] baseClass = new SubClass[10];
		
			// 这样是会报编译错误的 
            // 因为 List<BaseClass> 不是 List<SubClass> 的父类
            // 它们是两个完全不同的类型
			List<BaseClass> baseClasses = new List<SubClass>();
		}
	}
}
```

首先 SubClass 继承了 BaseClass，在 Awake 方法中，用父类的数组变量接收子类的数组是没问题的。

但是用父类的 List 接收子类的数组就会报编译错误，这是泛型的一个设计问题。

不过我们换一种写法，缺没什么问题，代码如下:

```c#
using System;
using System.Collections.Generic;
using UnityEngine;

namespace QFramework.Master
{
	public class GenericExample : MonoBehaviour
	{
		public class BaseClass
		{

		}

		public class SubClass : BaseClass
		{

		}

		private void Awake()
		{
			// 合法的协变 C# 4.0 之前，支持的简单协变
			IEnumerable<BaseClass> baseClassesA = new SubClass[10];
		
			// 合法的协变 C# 4.0 之前，支持的简单协变
			IEnumerable<BaseClass> baseClassesB = new List<BaseClass>();

			// C# 4.0 后这样写是合法的,也是协变
			IEnumerable<BaseClass> baseClassesC = new List<SubClass>();
		
		}
	}
}
```

为什么以上的代码是合法的呢，其实原因非常简单，因为 IEnumerable 是只读的，也就是说我们不可以为 IEnumerable 添加数据。而次要的原因是 List 和 Array 都实现了 IEnuemrable 接口，而 SubClass 继承了 BaseClass，这些共同的条件才可以允许协变的发生。

那么在 C# 中，如何确保 IEnumerable 是只读的呢？非常简单，只需要在泛型定义上加上 out 关键字即可，代码如下：

```c#
using System.Runtime.CompilerServices;
// C# 4.0 之后的 iEnumerable 定义
namespace System.Collections.Generic
{
  public interface IEnumerable<[Nullable(2)] out T> : IEnumerable
  {
    [NullableContext(1)]
    IEnumerator<T> GetEnumerator();
  }
}
```

而在 C# 4.0 之前的定义如下:

```c#
namespace System.Collections.Generic
{
  public interface IEnumerable<T> : IEnumerable
  {
    IEnumerator<T> GetEnumerator();
  }
}
```

由于 C# 4.0 之前，没有加上 out 关键字，那么协变就无法在 IEnumerable 和 List 之间发生。
这就是泛型的协变的作用。

想自己实现一个也非常简单，代码如下:

```c#
using UnityEngine;

namespace QFramework.Master
{
	public class GenericExample : MonoBehaviour
	{
		public interface Base<out T>
		{
		
		}

		public class Sub<T> : Base<T>
		{
		
		}

		private void Awake()
		{
			// 完全合法 发生了协变
			Base<object> baseObject = new Sub<string>();
		}
	}
}
```

OK，关于协变就介绍到这里，接下来介绍逆变。

### 逆变

理解了协变，逆变就更容易被理解了。

逆变只需要用 in 关键字，来标记父接口的类型是只写的。

具体的示例代码如下：

```c#
using UnityEngine;

namespace QFramework.Master
{
	public class GenericExample : MonoBehaviour
	{
		// 用 in 表示只写
		public interface Base<in T>
		{
			T Value { set; } // 不能声明 get
		}

		public class Sub<T> : Base<T>
		{
			public T Value { get; set; }
		}

		private void Awake()
		{
			// 完全合法
			Base<string> baseObject = new Sub<object>();

			baseObject.Value = "hello";

		}
	}
}
```

代码看懂了，就算理解了逆变了。

这里总结一下：

- 协变就是父接口的泛型是只读的，用 out 关键字修饰即可
- 逆变就是父接口的泛型是只写的，用 in 关键字修饰即可
- 如果无法确定父接口的泛型是只读和只写的，只能确保同泛型类型之间发生协变。比如:`IEnumerable<string> a = new List<string>();

## 分部类型 partial

分部类型可以把一个类的定义拆分到不同的文件中。

这样非常容易实现对类进行扩展的操作。

基本的使用方式如下:

```c#
// 需要在同一个程序集中的同一个命名空间
namespace QFramework.Partial
{
	// 类名 和 访问权限 要完全一致
	public partial class PartialClass
	{
		public string A { get; set; }
	}

}

namespace QFramework.Partial
{
	// 加上 abstract 关键字，那么 PartialClass 则变成抽象类型
	public abstract partial class PartialClass
	{
		public string B { get; set; }
	}
}
```

微软的官方文档说，partial 关键字设计的初衷则是为了把一个类的职责拆分给不同的开发者维护。

而现实情况则是，partial 关键字一般是用来给界面类拆分 View 和 Controller 的职责。

比如 Xamarin 的中一个界面的代码一般会分别写在两个文件中，即 SomeUI.cs 和 SomeUI.Designer.cs。

SomeUI.cs 中主要是提供给用户自己写逻辑用的，充当的是 Controller 的职责，而 SomeUI.Designer.cs 主要是提供一个 UI 元素的引用提供，充当的是 View 的职责，为什么拆分成两个文件呢？这是因为 SomeUI.Designer.cs 中的代码，完全是自动生成的。

大概代码如下:
SomeUI.cs

```c#
public partial class SomeUI : ViewController
{
    protected override void Init()
    {
        // 逻辑
    }
}
```

SomeUI.Designer.cs

```c#
public partial class SomeUI
{
    [Bind]
    public UIText Text;
  
    [Bind]
    public UIButton Button;
}
```

这就是 partial 关键字比较常见的用法。

当然还有一些用法，像 UniRx 的 API 统一都是由 Observable 静态类提供的，而 UniRx 有大量的操作符和事件源的 API，那么都写在一个 Observable 静态类中那是不现实的，所以作者用 partial 关键字把 Observable 静态类拆分到了几个文件中，这样情况就好多了。

大概用法如下:
Observable.XXX.cs

```c#
public static partial Observable
{
    public static IObserver<TSource> Create() {}
    ...
}
```

Observable.YYY.cs

```c#
public static partial Observable
{
    public static IObserver<TSource> Timer() {}
    ...
}
```

所以 partial 关键字的另一个用法就是拆分 API 的定义。

## 匿名方法

C# 2.0 中的匿名方法写法如下:

```c#
using System;
using UnityEngine;

namespace QFramework.Master
{
	public class DelegateExample : MonoBehaviour
	{
		public Action<int> OnValueChanged;

		private void Awake()
		{
			// C# 1.0 中 委托只能注册方法
			OnValueChanged = OnSomeValueChanged;

			// C# 2.0 中 委托支持了匿名方法（）
			OnValueChanged = delegate(int value) { Debug.Log(value); };
		}

		void OnSomeValueChanged(int value)
		{

		}
	}
}
```

非常简单，匿名方法就不多说了。

## 可以为 null 的值类型

代码如下：

```c#
using UnityEngine;

namespace QFramework.Master
{
	public class NullableValueTypeExample : MonoBehaviour
	{

		private void Awake()
		{
			int? number = null;
		}
	}
}
```

实际上，可以为 null 的值类型，我们在 Unity 开发中用得非常少。

值类型和引用类型相比，有一个不太方便的地方就是值类型的变量，比较难以判断是否进行过初始化。而引用类型则比较容易，如果值为 null，说明未初始化。

而有了可空值类型这个特性，就比容易判断一个 number 是否是初始化过了。

## 迭代器

迭代器是非常重要的一个 C# 特性。

Coroutine、LINQ、甚至是 UniRx 的实现都是以迭代器为基础的。

那么什么是 C# 中的迭代器呢？

我们先看一个示例，代码如下:

```c#
using System.Collections;
using UnityEngine;

namespace QFramework.Master
{
	public class IEnumerableExample : MonoBehaviour
	{
		private void Awake()
		{
			foreach (var value in GetSomeValue())
			{
				Debug.Log(value);
			}
		}

		IEnumerable GetSomeValue()
		{
			yield return 1;
			yield return 2;
			yield return 3;
		}
	}
}
// 输出结果:
// 1
// 2
// 3 
```

其中 GetSomeValue 叫做迭代器方法，GetSomeValue 中写了 yield return 这样的语句。

从以上代码中的输出结果，就可以猜到 GetSomeValue 具体做了什么事情，在 foreach 遍历时，每次遍历获取到的 value 值，就是 GetSomevalue 中返回的值。

换一个角度，我们可以把 GetSomeValue 的返回 IEnumerable 当做一个 int 数组，数组中的值分别是 1，2，3，这样的话就比较容易理解了。

我们来介绍下 GetSomeValue 还可以返回哪些类型的参数，如下:

- IEnumerable
- IEnumerator
- IEnumerable<T>
- IEnumerator<T>

示例代码如下:

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace QFramework.Master
{
	public class IEnumerableExample : MonoBehaviour
	{
		private void Awake()
		{
			foreach (var value in GetSomeValue())
			{
				Debug.Log(value);
			}

			foreach (var value in new GetSomeValue2())
			{
				Debug.Log(value);
			}

			foreach (var value in GetSomeValue3())
			{
				Debug.Log(value);
			}

			foreach (var value in new GetSomeValue4())
			{
				Debug.Log(value);
			}
		}

		IEnumerable GetSomeValue()
		{
			yield return 1;
			yield return 2;
			yield return 3;
		}

		public class GetSomeValue2
		{
			// 只要有 GetEnumerator 方法就可以用 foreach 迭代
			public IEnumerator GetEnumerator()
			{
				yield return 4;
				yield return 5;
				yield return 6;
			}
		}


		IEnumerable<int> GetSomeValue3()
		{
			yield return 7;
			yield return 8;
			yield return 9;
		}

		public class GetSomeValue4
		{
			// 只要有 GetEnumerator 方法就可以用 foreach 迭代
			public IEnumerator<int> GetEnumerator()
			{
				yield return 10;
				yield return 11;
				yield return 12;
			}
		}
	}
}
// 输出结果:
// 1
// 2
// 3 
// 4 
// 5 
// 6 
// 7 
// 8 
// 9 
// 10
// 11
// 12
```

### 什么是迭代器模式

> 在不知道集合内部细节的情况下，提供一个按序方法存取的一个对象集合体的每一个单元。—GoF
> 提供一种方法顺序访问一个集合对象中的各个元素，又不暴露该对象的内部表示

迭代器模式由于经常使用到，已经被 .Net 收录到 API 中。

在 C# 中，经常使用泛型存储对象，当想按序存取这些泛型容器时，都会使用 C# 的 foreach 语句。

foreach 语句就是一个能顺序访问一个集合的方法。
它就是 C# 语言内置的迭代器模式。

### 如何使用 IEnumerable

在使用之前，我们先理解一下 IEnumerable。这里老师直接给一个比较容易记住的定义，IEnumerable 中文意思是 可枚举的，教练直接把他理解为 IForeachable（可遍历的），虽然定义不是很准确，但是在区分 IEnumerable 和 IEnumerator 时候很容易搞清楚。

我们直接看下 IEnumerable 的定义:

```c#
//这个接口告知调方对象的子项可以枚举
public interface IEnumerable
{
	IEnumerator GetEnumerator();
}
```

示例代码如下:

```c#
using System.Collections;
using UnityEngine;

namespace UniRxLesson
{
	public class IEnumerableExample : MonoBehaviour
	{
		class ForEachable : IEnumerable
		{
			object[] mObjArray = new object[4]
			{
				"1","2","3","4",
			};

		
			public IEnumerator GetEnumerator()
			{
				return mObjArray.GetEnumerator();
			}
		}

		private void Start()
		{
			var foreachAble = new ForEachable();

			foreach (var number in foreachAble)
			{
				Debug.Log(number);
			}
		}

	}
}

//输出结果为
// 1
// 2
// 3
// 4
```

只要实现一个 GetEnumerator 就可以使用 foreach 了。

### foreach 同等实现

```c#
using System.Collections;
using UnityEngine;

namespace UniRxLesson
{
	public class ForEachExample : MonoBehaviour
	{
		class ForEachable : IEnumerable
		{
			object[] mObjArray = new object[4]
			{
				"1","2","3","4",
			};

			public IEnumerator GetEnumerator()
			{
				return mObjArray.GetEnumerator();
			}
		}

		private void Start()
		{
			var foreachAble = new ForEachable();
		
			// 手动与 IEnumerator 协作
			var i = foreachAble.GetEnumerator();
		
			while (i.MoveNext())
			{ 
				var number = i.Current;
				Debug.Log(number);
			}
		}
	}
}

//输出结果为
// 1
// 2
// 3
// 4
```

代码中出现的 MoveNext、Current 等 API 简单看下 IEnumerator 定义就清楚了。

### IEnumerator 定义

```c#
public interface IEnumerator
{
	bool MoveNext();             //将游标的内部位置向前移动
	object Current{get;}         //获取当前的项（只读属性）
	void Reset();                //将游标重置到第一个成员前面
}
```

### IEnuemrator 代码示例

```c#
using System.Collections;
using UnityEngine;

namespace UniRxLesson
{
	public class IEnumeratorExample : MonoBehaviour
	{
		private class ForEachable : IEnumerable
		{
			public IEnumerator GetEnumerator()
			{
				return new FiveTimes();
			}
		}
	
		private class FiveTimes : IEnumerator
		{
			private int mCount = 5;
		
			public bool MoveNext()
			{
				mCount--;
				return mCount >= 0;
			}

			public void Reset()
			{
				mCount = 5;
			}

			public object Current
			{
				get { return string.Empty; }
			}
		}

		private void Start()
		{
			var foreachAble = new ForEachable();

			foreach (var empty in foreachAble)
			{
				Debug.Log("A");
			}
		}
	}
}

//输出结果为
// A
// A
// A
// A
```

### yield

yield 实质是一个语法糖，它让程序员能够更方便的去使用迭代器，通过 yield 你可以直接使用迭代器操作而不需要去实现 IEnumerable 和 IEnumerator，也不需要一个临时的 Collection 来完成迭代。

yield 有两种格式声明

```c#
yield return <expression>;
yield break;
```

 yield 工作流程

```c#
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

//输出结果为
// A
// A
// A
// A
// A
```

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191928017.png)

## getter/setter 单独可访问性

getter/setter 单独可访问性，英文原文是 getter/setter seperate accessbility，翻译成人话应该是，gette/setter 的分别的访问权限。

代码如下:

```c#
public class PropertyExample
{
    int mAge = 0;
  
    public int Age 
    {
        get 
        {
            return mAge;    
        }
      
        // 可以单独设置 setter 的访问权限
        private set 
        {
            mAge = value;
        }
    }
}
```


![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191928855.png)

get 和 set 的访问器，可以有不同的访问权限。

## 方法组转换（委托）

这个特性，直接看如下代码就懂了。

```c#
public class CSharpExample
{
    public delegate void SomeDelegate();

    void SomeFunc()
    {
    }

    void Example()
    {
        // C# 1.0 只能这样写，要使用 new 关键字
        SomeDelegate delegateA = new SomeDelegate(SomeFunc);

        // C# 2.0 可以直接这样写 
        SomeDelegate delegateB = SomeFunc;
    }
}
```

## 静态类

静态类 与 非静态类唯一的区别就是 静态类不能实例化。

除了这个静态类是实现静态扩展方法的基础之一，不过静态扩展在 C# 3.0 才开始支持。

还有一个比较重要的一点就是静态构造方法。

代码如下:

```c#
class SimpleClass
{
    static readonly long baseline;

    static SimpleClass()
    {
        baseline = DateTime.Now.Ticks;
    }
}
```

- 静态构造函数在类的静态成员第一次访问或第一个类实例创建之前由系统调用

在 Unity 中，静态构造函数调用时机一般是前者，也就是静态类中的成员被第一次访问的时候调用。

想了解更多静态构造跟多地特性，可以看 C# 官方文档：[ 静态构造函数（C# 编程指南）](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)

## 委托推断

代码如下：

```c#
public class CSharpExample
{
    public delegate void SomeDelegate();

    void SomeFunc()
    {
    }

    void Example()
    {
        // C# 1.0 只能这样写，要使用 new 关键字
        SomeDelegate delegateA = new SomeDelegate(SomeFunc);

        // C# 2.0 可以直接这样写 
        SomeDelegate delegateB = SomeFunc;
    }
}
```

可以看到，其实和方法组转换的例子一模一样。

方法组转换的重点是方法组，方法组实际上就是方法名，可以把一个方法组直接设置给一个同类型同返回值的委托变量上，在这个直接设置值的过程中，方法组本身发生了方法组转换。

而委托推断意思就是，通过委托的返回值和参数去匹配方法组。

方法组转换和 委托推断共同可以实现以上的示例。

## 总结

- 泛型：
  - 泛型约束：where
  - 协变：父接口只读用 out T，父接口<父类型> = 子类<\子类型>
  - 逆变：父接口只写用 in T。父接口<子类型> = 子类<父类型>
- 分部类型：
  - 使用 partial 关键字将一个类的定义拆分到多个文件中（或者多个代码位置中）
  - 用途：
    - ViewController 中的 VIew 和 Controller 分别声明（代码生成部分和编程部分）
    - 同一个类的 API 分别在不同的文件中声明
    - 等等
- 匿名方法：
  - delegate(int a )=>{}
- 可空值类型:
  - int? a = null
  - 笔者自己的理解就是为了比较方便地判断一个值类型是否进行过初始化
- 迭代器:
  - IEnumerable: 可以直接 foreach
  - IEnumerator：需要作为 GetEnumerator 的返回值时，可以对所在的对象 foreach
  - yield return：语法糖，通过生成代码来生成迭代器对象
- 协变和逆变：在泛型中有介绍
- getter/setter 可单独访问性：
  - 可以单独为 setter 或 getter 设置访问权限
- 方法组转换、委托推断：
  - del a = funcB
  - 方法组就是方法名
- 静态类
  - 不能创建实例的类
  - 静态构造，在第一次访问时调用
