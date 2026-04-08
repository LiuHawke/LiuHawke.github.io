---
title: C#学习-C# 1.0
tags:
  - C#
  - 语法
categories:
  - C#
date: 2021-12-01 22:32:41
cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191917907.png
---

## C# 1.0

我们先罗列一下 C# 1.0 发布时所包含的语法特性，如下：

- 类（class）
- 结构（struct）
- 接口（interface）
- 事件（event）
- 属性（property）
- 委托（delegates）
- 表达式
- 语句
- 特性（有时候也叫属性）（Attribute）

C# 1.0 的特性只有以上这些。

基本上它涵盖了 C# 中最常用、最核心、最基础部分的内容。C# 使用进阶，一些非常基础的东西就不记录了。

 ![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191926400.png)

## 类

单纯从功能上来说，类可以：

- 包含变量（属性）
- 包含方法（行为）
- 继承
- 用来创建对象

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191926942.png)

### 面向对象与面向过程

有了类我们比较容易地进行面向对象编程，没有类当然也可以面向对象编程，只不过会不那么容易做到。

那么什么是面向对象编程呢？

我的理解只有两个字，建模。

建模就是对需要实现的功能或者需要解决的问题建立业务模型。

比如要实现购买商品功能，就需要我们创建商品对象，还要把购买商品的行为归属到另一个对象中，这个对象有可能是顾客对象，也有可能是平台对象。

这就是非常典型的面向对象建模案例。

那么如果是面向过程中的购买商品会是什么样的呢？

我们也许这样考虑，购买商品先创建一个购买商品函数，然后函数可能需要调用数据库，那就再写一个调用数据库的函数交媾购买商品函数调用，依次类推。

其实要区分面向对象编程与面向过程编程非常容易。

面向对象编程是以对象为基础进行思考的，而面向过程是以功能（函数）为基础进行思考的。

这就是两者的差别。

### 面向对象与面向过程是对立的吗？

不是对立的，面向对象更擅设计，而面向过程更擅长实现。

在使用 C# 的时候，两种编程思维都是会用到的。

### 没有类如何面向对象编程？

面向对象编程，当然要有对象了，如果没有类的话，其实也可以实现面向对象编程。

c 语言虽然不支持类，但是也可以通过宏或者一些模拟的手段支持类和对象的。

Lua 语言也没有提供类功能，但是通过元表也是能够模拟类和对象的。

OK，到此，面向对象这个话题可能与我们的专栏话题不太对题，但是一想到类，就不自觉地扯到面向对象这个话题了。

总是面向对象编程的核心就是两个字：建模。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927703.png)

### 引用类型和值类型

几乎在每一本编程语言书籍上都会谈到引用类型和值类型这两个概念。

具体的引用类型和值类型相关的细节，大家在网上找各种文章就可以了。

引用类型，是用类创建的类型就是引用类型。

值类型包含基础类型和结构体（struct）创建出来的类型。

如下：
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927309.png)

就是这么简单。

### 定义

值类型直接存储其值，而引用类型存储对其值的引用

值类型 和 引用类型 是非常重要且非常基础的话题，所以想深入研究，网上能够找到一大堆文章。

### 类是引用类型

记住这句话就行。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927535.png)

### 关键特性和功能

- 类的功能
- 类在编程生涯中的作用
- 面向对象和面向过程
- 值类型和引用类型

- 类的访问权限（封装性）
- 类的命名
- 抽象类 与 接口（继承性、多态性）
- 内部类
- partial 关键字
- 泛型类
- 等等

### 类的访问权限

访问权限有：internal、private、public。

internal class 一般在打 dll 的时候作用很大，可以控制有些类不让用户访问到。

private class 用得不多，一般作为内部类存在。

没了

如果不知道 internal 是什么作用，的可以在评论区提问。

### 类的命名

类的命名一般是名词，起名要合适，具体如何算合适？小班的文章中会介绍。

### 抽象类 与 接口

抽象类中有的时候需要些抽象方法，抽象方法需要在子类中覆写。

实现接口可以显式实现和隐式实现，显式实现可以控制方法的访问权限，这个在接口的部分会仔细讲。

### 内部类

有的时候需要在类内部创建一些只需要在类内部使用的对象，这时候可以用内部类。

### partial 关键字

paritial 可以实现类的逻辑拆分到不同的文件。

### 泛型类

类需要适配不同的类型的时候，可以用泛型类，比如单例的模板。

OK，到此就简单过了一些类相关的特性，只需要读一遍有个印象即可，如果遇到不懂的部分，自行百度。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927252.png)

## 结构体

首先要说到结构体，第一时间想到的就是：结构体是值类型。

那么值类型具体是什么样的呢？

不知道大家记不记得 Unity 中有 Vector3、Vector2 这样的向量类型，我们接触 Vector3 比较多的地方是 transform.position 和 transform.localPosition。

在刚开始接触 transform.position 和 transform.localPosition 的时候，会对一件事情会非常印象深刻

这件事情就是如果我想给一个 transform 的 x 坐标赋值，那么代码就要像如下的方式去写：

```
var position = transform.position;
position.x = 1.0f;
transform.position = position;
```

一个简单的赋值代码，需要些三行，觉得很麻烦。

为什么不像如下的方式写呢?

```
transform.position.x = 1.0;
```

这样写多方便？

这是因为，position 的类型 Vector3 是 struct 类型，struct 是值类型，同时 position 是 transform 的一个属性（而不是成员变量），所以就必须要给整个 position 变量赋值，而不是 position 变量中的某一个变量。

这就是 struct 和 class 最明显的区别。

作为一个属性时， struct 无法对单一的成员变量赋值，而 class 则是只要允许可以随便赋值。

其实关于结构体，其实有很多细节可以进行学习，但是我们不用学那么多。

根据自己的经验，结构体只需要知道如下关键点就行了：

- 是值类型
- 不能设置为 null
- 声明变量时，本身就有值了
- 赋值时是深拷贝
- 不能局部赋值（比如 transofrm.position.x 不能直接赋值）
  除了以上者五点比较重要之外，在其他情况下，很少用到结构体，最起码从以前到现在，接触到和结构体相关的编码实践也仅限于以上的五点特性。

而除了者五点之外，肯定还有很多其他的特性，其他的特性也许不那么常用，也可以简单地过一遍。

### 其它的细节

- 在结构声明中，除非将字段声明为 const 或 static，否则无法初始化。

- 结构不能声明无参数构造函数（没有参数的构造函数）或终结器。

- 结构在分配时进行复制。将结构分配给新变量时，将复制所有数据，并且对新副本所做的任何修改不会更改原始副本的数据。在处理值类型的集合（如`Dictionary<string, myStruct>`）时，请务必记住这一点。

- 结构是值类型，不同于类，类是引用类型。

- 与类不同，无需使用`new`运算符即可对结构进行实例化。

- 结构可以声明具有参数的构造函数。

- 一个结构无法继承自另一个结构或类，并且它不能为类的基类。所有结构都直接继承自[ValueType](https://docs.microsoft.com/zh-cn/dotnet/api/system.valuetype)，后者继承自[Object](https://docs.microsoft.com/zh-cn/dotnet/api/system.object)。

- 结构可以实现接口。

- 结构不能为`null`，并且不能向结构变量分配`null`，除非将变量声明为可为空的值类型。

  ![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927818.png)

## 接口（inteface）

是为了实现一个跑道生成器，使用 interface 来定义了一个跑道的实现标准，然后将跑道共同的逻辑都写到了跑道基类中（抽象类），最后每一种特殊的跑道则继承跑道基类，然后特殊的逻辑通过复写基类的方法即可。

大致的结构如下。
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927125.png)



interface 可以制定标准，可以解耦合，可以做设计，可以做限制等。

### 隐式实现

interface 的实现，大多是是隐式实现的，我们先看如下代码:

```c#
public interface IStage 
{
    void Generate();
}

public class Stage : IStage
{

    public void Generate()
    {
        Console.WriteLine("跑道生成");
    } 
}
```

### 显式实现

显式实现的代码其实非常简单如下：

```c#
public interface IStage 
{
    void Generate();
}

public class Stage : IStage
{

    void IStage.Generate()
    {
        Console.WriteLine("跑道生成");
    } 
}
```

- 显式实现的方法在调用时，方法所在对象必须是所实现的接口类型。

比较绕，什么意思呢？

我们尝试调用 Stage 的 Generate 方法，调用代码如下:

```c#
var stage = new Stage();
      
stage.Generate();
```

写完以上代码，IDE 会报错，如下：
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927725.png)

这是因为 stage 目前是一个 Stage 类型，而不是 IStage 类型。

如果想调用 Generate 方法，必须像如下方式这样写：

```c#
IStage stage = new Stage();
      
stage.Generate();
```

使用 IStage 变量来接收。

显式实现是为了解决实现多个接口时有可能出现的重名问题。

### 显式实现的应用场景

```c#
public interface IFlyable
{
    void Fly();
}

public interface IBird
{
    void Fly();
}

// 老鹰
public class Eagle : IFlyable, IBird
{
    // Fly 实现的是哪个接口？
    public void Fly()
    {
  
    }
}
```

代码中 IFlyable 和 IBird 都有 Fly 这个方法，那么同时实现了两个接口的 Eagle，其中的 Fly 方法，到底是属于哪个接口的 Fly 呢？

答案是两个接口都实现了。

其实这样是没什么问题的。

但是如果 IFlyable 和 IBird 的 Fly 不一样，要分别实现这两个接口的 Fly 该怎么办呢？

这就需要使用接口的 显式实现 了。

分别实现后的代码如下:

```c#
public interface IFlyable
{
    void Fly();
}

public interface IBird
{
    void Fly();
}

// 老鹰
public class Eagle : IFlyable, IBird
{
    void IFlyable.Fly()
    {
        Console.WriteLine("flyable fly");
    }
  
    void IBird.Fly()
    {
        Console.WriteLine("bird fly");
    }
}

void Main() 
{
    var eagle = new Eagle();
    (eagle as IBird).Fly();
    (eagle as IFlyable).Fly();
}

// 结果:
// bird fly
// flyable fly
```

这样就解决了，接口方法重名的问题。

OK，这是 C# 官方的推荐用法，使用 接口的显式实现 来解决多个接口有重名方法的问题。

还有其他使用场景一般是为了降低接口方法的访问权限。

我们看如下代码:

```c#
public interface ICommand
{
    void Execute();

    void Dispose();
}

public abstract class Command : ICommand
{
    // 降低了 Execute 的访问权限
    // 这样 Command 对象无法直接调用 Execute 方法
    // 避免被用户误操作
    void ICommand.Execute()
    {
        OnExecute();
    }
  
    protected virtual void OnExecute() {}
  
    void ICommand.Dispose()
    {
        OnDispose();
    }
  
    protected virtual void OnDispose() {}
}

public class HelloWorldCommand : Command
{
    protected override OnExecute()
    {
       Debug.Log("Hello World");
    }
}

public static class CommandExecuter
{
    public void ExecuteCommand(ICommand command)
    {
        command.Execute();
        command.Dispose();
    }
}


void Start()
{
    var command = new HelloWorldCommand();
    // command.Execute(); 无法直接调用
    CommandExecuter.ExecuteCommand(command);// 可以交给专门的执行器执行
}
```

 降低接口方法权限的好处，就是用户创建出来的对象无法直接使用接口方法，而是把接口方法交给专门的调用器调用，这样可以避免用户误操作。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927429.png)

其实还有一些关于接口的一些细节描述没有介绍，

- 接口类似于只有抽象成员的抽象基类。 实现接口的任何类或结构都必须实现其所有成员。
- 接口无法直接进行实例化。 其成员由实现接口的任何类或结构来实现。
- 接口可以包含事件、索引器、方法和属性。
- 接口不包含方法的实现。
- 一个类或结构可以实现多个接口。 一个类可以继承一个基类，还可实现一个或多个接口。

## 事件

C# 语言的事件这个概念一般用得比较少，用得比较多的是委托。

```c#
public class EventArgs
{
    public int Data { get; set; }
}

public event Action<EventArgs> SomeEvent;
            
void Start()
{
    SomeEvent += OnSomeEvent;

    SomeEvent(new EventArgs()
    {
        Data = 10
    });
          
    SomeEvent -= OnSomeEvent;
          
    if (SomeEvent != null)
    {
        SomeEvent.Invoke(new EventArgs()
        {
            Data = 20
        });
    }
}

void OnSomeEvent(EventArgs eventArgs)
{
    Debug.Log(eventArgs.Data);
}
// 输出结果为
// 10
```

代码很简单，event 的用法，其实和委托的语言特性差不多，都可以通过 += 和 -= 运算符进行注册和注销。

但是有一个非常重要的区别，就是 event 只能在所声明的类的内部调用。

也就是说，以上的代码中的 SomeEvent，在别的类中无法调用的，但是在别的类中可以进行 += 和 -= 操作。

这就是事件与委托最核心的区别。

这个区别不是约定，而是会报编译错误的一个限制。

除了这个其他的方面事件和委托差不多。

由于有了这个限制，事件只能从内向外发送事件。

只需要记住事件只能在类的内部触发即可。

除了这一点外，还有一种可能觉得比较新鲜的特性，就是事件的属性器不是 set 和 get，而是 add 和 remove，代码如下:

```c#
public event Action<EventArgs> SomeEvent
{
    add { mSomeEvent += value; }
    remove { mSomeEvent -= value; }
}

private Action<EventArgs> mSomeEvent;
```

## 属性器

属性器有一个好处，就是隔离类内部的变化，当类内部发生改变，而外部可以不受到任何影响。

```c#
public class Student
{
    public int Age;
}

void Start()
{
    var student = new Student() { Age = 15 };
  
    Debug.Log(student.Age);
}
```

代码没什么问题，接下来，由于需求变更，Student 的 Age 需要根据生日计算，而不是直接录入年龄，因为随着时间变换，学生的 Age 需要每年更新一次，如果根据生日自动计算，就会省去每年更新的操作。

实现需求后，如下:

```c#
public class Student
{
    public int Age 
    {
        get 
        {
            return DateTime.Now.Year - BirthYear;
        }
    }
  
    public int BirthYear;
}

void Start()
{
    var student = new Student() { BirthYear = 2004 };
  
    Debug.Log(student.Age);
}
```

这样，当用到 Age 部分的代码就不用进行变更了，只需要变更 Student 类的内部就可以了。

这是属性的一种用法，就是用来隔离类内部的变化，而隔离内部的变化的这个特性，其实就是 面向对象的 封装性。

C# 1.0 中的属性器，没有简写版本，只能是如下代码所示的方式定义属性器:

```c#
public string Name 
{
    get { return mName; }
    set { mName = value; }
}

private string mName;
```

## 委托

C# 中的委托真的是非常好用，C# 的委托相比 C++ 的函数指针，更加安全，相比 Java 的匿名类，更加简洁。

### 使用经验

C# 内置了 Action<T>、Func<T> 等委托定义，基本上用到委托的部分用 Action 和 Func 就可以满足大多数需求了。

Action<T>、Func<T> 的区别是 ：

Action<T>只有参数类型，不能传返回类型。所以Action<T>的委托函数都是没有返回值的。

Func<T>有返回类型

### 注意事项

- 注册委托 和 注销委托 最好成对出现
- 委托有可能为 null，所以最好在声明委托变量时，设置一个初始值，可以减少空指针异常的风险，如下:

```c#
public Action<int> OnAgeChanged = (age)=>{};
```

## 表达式

什么是表达式呢？

官方的定义：

- 表达式是由一个或多个操作数以及零个或多个组成的序列，其计算结果为一个值、对象、方法或命名空间 。

这就是表达式的相对严谨一些的定义了。

虽然定义理解起来比较晦涩，但是我们在日常开发中，会写大量的表达式。

比如如下代码:

```c#
if (x > 10)
{
    // do something
}

for (int i = 0; i < 10; i++) 
{ 
    // ...  
}
```

代码中的 x > 10，i < 10，这种代码就是一种表达式。

那么表达式有哪些分类呢？

我们来逐个过一遍表达式的类型。

### 数值和字符串

最简单的两种表达式是数值和字符串，代码如下:

```
int i = 5;
string text = "Hello World";
```

代码中的 5 和 Hello World 就是表达式值，而 i 和 text 则是声明的变量。

以上的两行代码一般叫做声明两个变量，但是我们也可以叫做两个表达式的声明。

在表达式中使用 i 和 text 这些变量的时候，变量名称计算为当前在改变量的内存位置所存储的值：

```c#
Debug.Log(i); // Output: 5
Debug.Log(text); // Output: Hello World
```

### 调用表达式

调用表达式非常简单，就是方法调用。

```c#
void DoWork() {}
DoWork(); // 调用表达式

Action SomeAction = ()=>{};
SomeAction(); // 调用表达式
```

### 查询表达式

虽然 C# 1.0 没有支持，但是 LINQ 则是查询表达式

### Lambda 表达式

```c#
c(paramA)=>{  return someValue; }

()=>{}

(paramA)=>someValue;
```

除了以上这些表达式，还剩下一些概念：

- [表达式树 (C#)](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/expression-trees/index)
- 表达式主体定义:[Expression-bodied members](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/statements-expressions-operators/expression-bodied-members)

这些平时用的不多，但是对于理解 C# 语言还是蛮有好处的。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927324.png)

## 语句

语句的介绍非常简单，任何可以用分号结尾的代码就是一个语句。

比如:

```c#
Debug.Log("Hello World");

int age = 10;

int result = 1 + 1;
```

虽然语句的概念比较好理解，但是在 C# 语言中，语句的类型有非常多。

它们分别如下:

- 声明语句
- 表达式语句
- 选择语句
  - if、else、switch、case
- 迭代语句
  - do、for、foreach、in、while
- 跳转语句
  - break、continue、default、goto、return、yield
- 异常处理语句
  - throw、try-catch、try-finally、try-catch-finally
- await 语句
- yield return 语句
- fixed 语句
- lock 语句
- 空语句
- 等等

而具体的细节这里就不介绍了，大家只要知道语句是什么样的一个东西 并且 C# 1.0 中包含语句这个特性就可以了。

如果想详细了解可以查阅如下地址：https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/statements-expressions-operators/statements

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927802.png)

## 反射

### 反射简介

反射，英文为：Reflection。

核心就是使用各种类型（Type）相关的 API。

对于反射来说，其 Hello World 如下:

```c#
using UnityEngine;

namespace QFramework.Example.CSharp
{
	public class ReflectionExample : MonoBehaviour
	{
		public class SomeClass
		{
		}

		void Start()
		{
			var type = typeof(SomeClass);
			Debug.Log(type);
		}
	}
}

// 输出
// QFramework.Example.CSharp.ReflectionExample+SomeClass
```

代码很简单，typeof 可以获取到一个类的 Type。

而获取一个类或对象的类型信息，就算是一种反射操作了。

### 反射的官方定义

下面给出反射的官方定义，如下:

- 提供封装程序集、模块和类型的对象

- 反射提供以下对象
  - 封装了程序集的对象
  - 封装了模块的对象
  - 封装了类型的对象

在 C# 中，我们可以通过 Type 获取到该类所在的程序集、模块、以及本身的类型，代码如下:

```c#
using UnityEngine;

namespace QFramework.Example.CSharp
{
	public class ReflectionExample : MonoBehaviour
	{
		public class SomeClass
		{
		}

		void Start()
		{
			var type = typeof(SomeClass);
			// 程序集
			Debug.Log(type.Assembly);
			// 模块
			Debug.Log(type.Module);
			// 类型名字
			Debug.Log(type.Name);
		}
	}
}
// 输出结果:
// Assembly-CSharp, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null
// Assembly-CSharp.dll
// SomeClass
```



通过一个对象的 GetType() 获取到 Type 对象。

type 还可以访问大量的类相关的信息，比如是否是 Class？是否是抽象的？是否是值类型等等。

- 类信息查询 API：获取各种名字。
- 检测 API：判断 type 是否是某一个事物（比如是否是抽象的，等等）
- 类结构查询 API：获取父类类型、获取方法、获取构造、获取成员变量等等。

```c#
using UnityEngine;
 
namespace QFramework.Example.CSharp
{
    public class ReflectionExample : MonoBehaviour
    {
        public class SomeClass
        {
        }
 
        void Start()
        {
            // 通过对象的 GetType 获取对象 
            var type = new SomeClass().GetType();

            Debug.LogFormat("FullName:{0}", type.FullName);
            Debug.LogFormat("IsClass:{0}", type.IsClass);
            Debug.LogFormat("Namespace:{0}", type.Namespace);
            // 是否是抽象的（抽象类、接口）
            Debug.LogFormat("IsAbstract:{0}", type.IsAbstract);
            // 是否是值类型
            Debug.LogFormat("IsValueType:{0}", type.IsValueType);
        }
    }
}
// 输出结果:
// FullName:QFramework.Example.CSharp.ReflectionExample+SomeClass
// IsClass:True
// Namespace:QFramework.Example.CSharp
// IsAbstract:False
// IsValueType:False
```

### 反射知识体系:

- 基本的反射对象的 API 类型
  - 信息查询
  - 检测
  - 结构查询
- Type 对象提供的 结构查询 API
  - GetPropertys()、GetProeprty(name)：返回 PropertyInfo 对象，可以 SetValue 和 GetValue
  - GetMethods()、GetMethod(name)：返回 MethodInfo 对象，可以 Invoke
  - GetMembers()、GetMember(name)：返回 MemberInfo 对象
  - GetFields()、GetField(name)：返回 FieldInfo 对象，可以 SetValue 和 GetValue
- Assembly 提供的 API
  - Assembly.LoadFrom(fullPath)
  - Assembly.GetExecutingAssembly()
  - assembly.CreateInstance(typeName)

## Attribute

Attribute 有时候翻译成特性，有时候翻译成属性。

使用 Attribute，可以在类或者方法、属性、程序集中声明一些标记。使用 Attribute 标记程序后，可以在运行时使用反射来查询特性。

一般情况下，最常接触的 Attribute 就是 SerializableAttribute，示例代码如下:

```c#
[Serializable]
public class PlayerInfo
{
    public int Age = 10;
}
```

使用 Serializable 标记的类，可以进行序列化和反序列化，比如用 JsonUtility 或者一些 XML 库。

而第二个常见的 Attribute 也许是 Unity 提供的 SerializeField，它可以让私有的成员变量在 Inspector 上进行显示。

代码如下:
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927730.png)

效果如下:
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927554.png)

### 自定义 Attribute

要想实现自定义的 Attribute 非常简单，只需要继承 Attribute 类即可，代码如下:

```c#
public class AutoSetHelloWorldAttribute : Attribute
{

}

// 使用代码如下
public class SomeClass
{
	[AutoSetHelloWorld]
	public string Text;
}
```

定义好 AutoSetHelloWorld Attribute 后，我们可以将它标记到一个成员变量上。

AutoSetHelloWorld 意思是自动设置 HelloWorld 给所标记的成员变量。

但是光是把 SomeClass 中的 Text 变量标记为 AutoSetHelloWorld 是不够的，因为 Attribute 往往需要和反射技术一起使用。

OK，我们看下如何对 SomeClass 的 Text 变量赋值的代码，如下:

```c#
public class AttributeExample : MonoBehaviour {

	void Start ()
	{
		// 创建 SomeClass 对象 
		var someObject = new SomeClass();

		// 获取 SomeClass 的类型
		var type = someObject.GetType(); // typeof(SomeClass) 亦可

		// 获取所有成员变量
		var members = type.GetMembers();

		// 获取所有标记 AutoSetHelloWorld 的成员变量
		var markedMembers =
			members.Where(m => m.GetCustomAttributes(typeof(AutoSetHelloWorldAttribute), false).Length != 0);

		// 设置 Hello World
		foreach (var markedMember in markedMembers)
		{
			var fieldInfo = markedMember as FieldInfo;

			fieldInfo.SetValue(someObject, "Hello World");
		}
	
		// 输出
		Debug.Log(someObject.Text);
	}
}
```

代码中包含了很多 反射相关的代码

我们看下 AttributeExample 的输出结果，如下:
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191927150.png)

- 自定义 Attribute 只需要继承 System.Attribute
- 自定义 Attribute 通常需要与反射配合
