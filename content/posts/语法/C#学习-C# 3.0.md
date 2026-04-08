---
title: C#学习-C# 3.0
tags:
  - C#
  - 学习
categories:
  - C#
date: 2021-12-05 21:01:21
cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191917907.png
---

## C# 3.0 版本

C# 3.0 新增的语法特性，如下:

-  自动实现的属性
-  匿名类型
-  查询表达式（LINQ）
-  表达式
-  表达式树
-  扩展方法
-  隐式类型本地变量
-  分部方法
-  对象和集合初始值设定项

## 自动实现属性

这个特性非常简单，代码如下:

```c#
public class PropertyExample
{
    // C# 3.0之前
    private string mNickName;

    public string NickName
    {
        get { return mNickName; }
        set { mNickName = value; }
    }

    // C# 3.0
    public string Name { get; set; }
}
```

非常简单，看代码就懂了。

## 匿名类型

代码也很简单，如下:

```c#
var person = new {Title = "Name"};
Debug.Log(person.Title);
```

## lambda 表达式

lambda 表达式非常简单，就是匿名方法的一种表现，如下:

```c#
using System;

public class LambdaExpressionExample
{
    void Func()
    {
    }

    void Main()
    {
        // C# 1.0
        Action action1 = new Action(Func);

        // C# 2.0 
        Action actionB = delegate {  };
  
        // C# 3.0
        Action acionC = () => { };
    }
}
```

## 表达式树

表达式树实际上是 Expression 这个 API 的使用，先看下代码，如下:

```c#
using System;
using System.Linq.Expressions;
using UnityEngine;

namespace Master
{
	public class ExpressionTreeExample : MonoBehaviour
	{
		void Start()
		{
			// 创建表达式树  
			Expression<Func<int, bool>> expr = num => num < 5;  
  
			// 将表达式树编译为一个委托  
			Func<int, bool> result = expr.Compile();  
  
			// 执行表达式树的委托
			Debug.Log(result(4));
		}
	}
}
// 输出结果
// True
```

通过表达式树可以构造一个委托出来，当执行委托的时候，就执行表达式树所表达的代码。

以上这段代码是通过 labmda 表达式来构建的表达式树，而最原始的表达式树的创建过程是通过 Expression 提供的 API，一个一个构建的，代码如下:

```c#
using System;
using System.Linq.Expressions;
using UnityEngine;

namespace Master
{
	public class ExpressionTreeExample : MonoBehaviour
	{
		void Start()
		{
			// 创建一个 num 参数
			var num = Expression.Parameter(typeof(int), "num");
			// 创建一个 常数 5
			var five = Expression.Constant(5, typeof(int));
			// 创建一个表达式 num < 5
			var numLessThan5 = Expression.LessThan(num, five);
			// 创建一个 labmda 表达式 (num)=>num < 5
			var labmda = Expression.Lambda<Func<int, bool>>(numLessThan5, new ParameterExpression[]
			{
				num
			});
		
			// 将表达式树编译为一个委托  
			Func<int, bool> result = labmda.Compile();  
  
			// 执行表达式树的委托
			Debug.Log(result(4));
		
		}
	}
}
// 输出结果
// True
```

以上的构建过程同 lambda 形式的构建过程。

表达式树非常适合做方法参数的验证等工作。

## 扩展方法

扩展方法也非常简单，代码如下:

```c#
using UnityEngine;

namespace Master
{
	public class StaticExtensionsExample : MonoBehaviour
	{
		void Start()
		{
			this.Show();
		}
	}

	/// <summary>
	/// 定义
	/// 需要是静态类
	/// </summary>
	public static class MonoExtensions
	{
		/// <summary>
		/// 
		/// </summary>
		/// <param name="self">需要有 this 关键字</param>
		public static void Show(this MonoBehaviour self)
		{
			self.gameObject.SetActive(true);
		}
	
	}
}
```

## 隐式类型本地变量

```c#
using UnityEngine;

namespace Master
{
	public class StaticExtensionsExample : MonoBehaviour
	{
		void Start()
		{
			// 隐式 （使用 var)
			var age = 10;
			// 显式
			int age2 = 10;
		}
	}
}
```

## 分部方法

```c#
using UnityEngine;

namespace Master
{
	public class StaticExtensionsExample : MonoBehaviour
	{
		void Start()
		{
			var obj = new TestPartialMethod();

			obj.Click();
		}
	}

	public partial class TestPartialMethod
	{
		public void Click(){ OnClick();}
	
		/// <summary>
		/// 不能有访问权限,实际上是 private 权限
		/// </summary>
		partial void OnClick();
	}

	public partial class TestPartialMethod
	{
		partial void OnClick()
		{
			Debug.Log("OnClick");
		}
	}
}
// 输出结果
// OnClick
```

## 对象和集合初始值设定项

```c#
using System.Collections.Generic;
using UnityEngine;

namespace Master
{
	public class InitialExample : MonoBehaviour
	{
		void Start()
		{
			var obj = new SomeClass() {Age = 10};

			var students = new List<string>() {"凉鞋", "匠人"};

			var dictionary = new Dictionary<string, string>()
			{
				{"A", "Apple"},
				{"B", "Banana"},
			};
		}
	}

	public class SomeClass
	{
		public int Age { get; set; }
	}
}
```

## 扩展方法

因为扩展方法是 C# 的特色概念，也就是说 C# 有，但是别的语言没有（最近用于 Flutter 的 Dart 支持了扩展方法）。

这样就导致了很多在 C# 容易做的事情，在别的语言中就变得非常困难。

扩展方法可以让我们对现有的对象增加方法，而不用去修改对象类的代码、编译等。

扩展方法本质上是一个静态方法。

在 C# 中，最常见的扩展方法就是 LINQ 。

对于 Unity 开发者来说，第一次接触扩展方法，应该是在使用 DOTween 的时候。

DOTween 的示例代码如下:

```c#
transform.DOMove(new Vector3(2,2,2), 2)
  .SetEase(Ease.OutQuint)
  .SetLoops(4)
  .OnComplete(myFunction);
```

当然不用扩展方法，也是可以做到以上这样的链式编程的，但是有了扩展方法，会更容易实现链式的 API 的，可以让每个类各司其职。

### 传统用法：扩展对象的方法

通过再封装的方式重命名 Unity 的 API，比如:

```c#
public static class BehaviourExtensions
{
        public static GameObject Show(this GameObject selfObj)
        {
            selfObj.SetActive(true);
            return selfObj;
        }

        public static T Show<T>(this T selfComponent) where T : Component
        {
            selfComponent.gameObject.Show();
            return selfComponent;
        }
}
// 使用方式:
// this.Show(); // this 是 MonoBehaivour
```

### 基础的链式 API

代码如下:

```c#
using UnityEngine;

namespace QFramework.Master
{

	public class ExtensionsExample : MonoBehaviour
	{

		// 不用扩展方法的方式定义
		public ExtensionsExample SayHello()
		{
			Debug.Log("Hello");
			return this;
		}

		public ExtensionsExample SayGoodBye()
		{
			Debug.Log("Goodbye");
			return this;
		}

		void Start()
		{
			this.SayHello()
				.DoSomething()
				.SayGoodBye();
		}
	}

	/// <summary>
	/// 扩展方法的方式定义
	/// </summary>
	public static class MonoExtensions
	{
		/// <summary>
		/// 使用泛型约束
		/// </summary>
		/// <param name="self"></param>
		/// <typeparam name="T"></typeparam>
		/// <returns></returns>
		public static T DoSomething<T>(this T self) where T : MonoBehaviour
		{
			self.name = "DoSomething";
			return self;
		}
	}

}
```

链式的 API 的核心就是返回自己（return this），可以把写死，也可以用泛型约束做成稍微通用一点的链式 API。

### Fluent API

Fluent API，流式的 API，或者说有阶段的 API。

其实以上的两种扩展方法使用方式都属于 Fluent API 范畴，只不过这些 API 还不够 Fluent。

Fluent API 是两个老外提出的。

有阶段的链式 API，举个例子。代码如下:

```c#
using UnityEngine;

namespace QFramework.Master
{
	public interface IStageStart : IStageA
	{
	}

	public interface IStageA : IStageB
	{
	}

	public interface IStageB : IStageBase
	{
	}

	public interface IStageBase
	{
		void ExecuteStage();
	}

	public static class StageExtensions
	{
		public static IStageA EnterStageA<T>(this T self) where T : IStageStart
		{
			return self;
		}

		public static IStageB EnterStageB<T>(this T self) where T : IStageA
		{
			return self;
		}
	}

	public class ExtensionsExample : MonoBehaviour, IStageStart
	{
		void Start()
		{
			this.EnterStageA()
				.EnterStageB()
				.ExecuteStage();
		
			this.EnterStageB()
				.ExecuteStage();
		
			this.ExecuteStage();

			// 这个会报编译错误 因为顺序不对
			// this.ExecuteStage()
				// .EnterStageB(); 
		}

		public void ExecuteStage()
		{

		}
	}
}
```

核心就是通过泛型约束和接口的继承，来强制让 API 按照一定顺序的调用。

## 查询表达式（LINQ)

查询表达式是用上了之后会离不开的一个特性，它主要做的一个事情就是对数据集合的查询。

我们看下示例代码：

```c#
using System.Collections.Generic;
using System.Linq;
using UnityEngine;

namespace QFramework.Master
{
	public class LINQExample : MonoBehaviour
	{
		public class Student
		{
			public string Name { get; set; }

			public int Age { get; set; }
		}

		void Start()
		{
			var students = new List<Student>()
			{
				new Student() {Name = "凉鞋", Age = 18},
				new Student() {Name = "hor", Age = 16},
				new Student() {Name = "天赐", Age = 17},
				new Student() {Name = "阿三", Age = 18}
			};

			// 1.基本的遍历
			students.ForEach(s => Debug.Log(s.Name));

			// 2.基本的条件过滤（Age > 5)
			students.Where(s => s.Age > 5)
				.ToList()
				.ForEach(s => Debug.Log(s.Name));


			// 与以上代码等价
			(from s in students
					where s.Age > 5
					select s)
				.ToList()
				.ForEach(s => Debug.Log(s.Name));

			// 3.基本的变换（student 转换成 name）
			students.Select(s => s.Name)
				.ToList()
				.ForEach(name => Debug.Log(name));

			// 与以上代码等价
			(from s in students
					select s.Name)
				.ToList()
				.ForEach(name => Debug.Log(name));

			// 4.基本的分组（使用学生的名字分组）
			students.GroupBy(s => s.Name)
				.ToList()
				.ForEach(group => Debug.Log(group.Count()));

			// 与以上代码等价
			(from s in students
					group s by s.Name)
				.ToList()
				.ForEach(group => Debug.Log(group.Count()));
		
			// 等等
		}
	}
}
```

LINQ 这个东西，很重要也很有用，它的理念是函数式编程，底层实现类似于上一篇文章的阶段性链式 API，不过比接口继承 + 泛型约束的方式更复杂一些。

理解 LINQ 是学习 UniRx 的基础，也是使用 .Net Core 的 Entity Framework Core 的基础。

LINQ 有各个语言的版本，如果做前端，那么 linq.js 是数据查询的必备的一个库。

### LINQ 扩展

- LINQ = IEnumerable + Operator + foreach/ForEach/Single/First/ToList/ToDictionary/ToHashSet等等

IEnumerable 实际上就是集合，C# 中的 Array、Dictionary、List、Stack、Queue 都是集合，都是可以用 LINQ 的。

Operator 是操作符：比如 Where、Select、GroupBy、Distinct 都是操作符。

而以上公式的第三个部分就是具体的操作了，一般情况下，经过 LINQ 操作符操作后得到的是一个集合，这个集合可以直接用 foreach 进行遍历，也可以转换成 List 或则 Dictionary 等数据结构。

如果想要从头到尾只用一行代码，那么就可以直接 ToList ，然后调用 List 的 ForEach，笔者在上一小节的示例代码就是这么做的。

主要记住这个核心，那么剩下的就只需要去一个个掌握 操作符（Operator）就可以了。

具体的每个操作符是什么意思可以看下微软的官方文档，如下:

- [LINQ 教程](https://docs.microsoft.com/zh-cn/dotnet/csharp/linq/)
- [Enumerable 方法(操作符大全)](https://docs.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.aggregate?view=netframework-3.5)

进入操作符大全之后具体看这里即可:
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191929871.png)
一个个点进去，就能看到每个 API 的简介和示例了。

### 底层实现

LINQ 的底层实现主要用了以下特性:

- 扩展方法
- 泛型约束
- 表达式树（源码中，没有看到表达式树相关的 API，但是微软的表达式树文档上说 表达式树是 LINQ 的基础）

而 LINQ 的两个核心接口是，IEnumerable 和 IEnumerator。

所有的集合都实现了 IEnumerable，比如 List、Dictinoary、Stack 等等。

所有的操作符都实现了 IEnumerator，比如 Where、Select、GroupBy 等等。

大部分操作符同时实现了 IEnumerable 和 IEnumurator，这样才可以对集合再进行一次操作。

### 总结

- 核心接口时 IEnumable 和 IEnumerator
- 使用的核心特性为 泛型约束 和 扩展方法
- ![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191929281.png)

## 总结

C# 1.0 主要是让 C# 变成了一个合格的面向对象语言，在此之上还提供了委托 Attribute 特性，这两个特性一个是使 C# 朝着函数式编程的方向上发展，另一个则使 C# 可以对程序添加声明性信息，这时候已经有了 Attribute 和 反射 这一黄金搭档。

完全掌握 C# 1.0，算是对 C# 这门语言达到了入门程度了，这时候用 C# 写一些逻辑是没问题的，只不过实现业务会有点不方便，但是都可以写的，基本上 C# 1.0 所支持的特性是我们平时编程所使用到的大部分特性。

C# 2.0，主要提供了泛型和迭代器还有匿名方法，提供了泛型之后就需要支持协变和逆变，匿名方法的支持可以让 C# 的回调更加简洁(delegte {})。

完全掌握 C# 2.0 ，算是对 C# 这门语言达到了基础程度了，在写业务上不会遇到太大的语法特性问题。而 C# 2.0 中的的匿名方法和泛型的 API（比如 List<T>）在业务的开发上会用的比较多，而需要设计泛型 API 的地方一般都是比较偏底层的部分。

C# 3.0，主要提供了 LINQ 和 扩展方法，这两个算是革命性的特性，有了 LINQ 处理数据的代码就变得更加简洁精炼了，而有了扩展方法使得对系统的扩展能力大幅增强。

完全掌握 C# 3.0 ，已经算是进阶的使用者了，LINQ 虽然在 Unity 上使用会有点效率问题，但是整体上它的“函数式编程的思想“会根深蒂固地影响我们写的每一行代码，LINQ 大幅简化了我们对数据操作的代码，而扩展方法可以让我们可以写出更合理的代码。

大概就是这样的一个过程。

而之后 C# 4.0 到 C# 8.0（截止到 2020 年 1 月），只有一个比较重要的特性，就是 Task 以及 async 和 await 的关键字支持。

构建 C# 的知识体系，简单的版本如下:

C# 1.0:

-  类（class）
-  结构（struct）
-  接口（interface）
-  事件（event）
-  属性（property）
-  委托（delegates）
-  表达式
-  语句
-  特性（有时候也叫属性）（Attribute）

C# 2.0:

-  泛型
-  分部类型（partial）
-  匿名方法
-  可以为 null 的值类型
-  迭代器
-  协变和逆变
-  getter/setter 单独可访问性
-  方法组转换（委托）
-  静态类
-  委托推断

C# 3.0 :

-  自动实现的属性
-  匿名类型
-  查询表达式（LINQ）
-  Lambda 表达式
-  表达式树
-  扩展方法
-  隐式类型本地变量
-  分部方法
-  对象和集合初始值设定项

以上没有反射这个特性，因为反射不是特性而是 .Net 的 API，当然要支持反射，语言层面上也是要做一些支持的。
