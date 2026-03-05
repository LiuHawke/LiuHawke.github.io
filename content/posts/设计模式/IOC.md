---
title: IOC
tags:
  - IOC
  - 设计模式
categories:
  - 设计模式
date: 2022-01-15 16:09:32
cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191859764.jpg
---

# 简介

## IOC简介

IOC全称为Invertuon Of Control，也就是控制反转。
控制反转是一种设计思想。而不是具体的技术。
IOC这种设计思想有很多方式实现，最常见的实现方式就是DI。

## DI简介

DI的全称是Dependency Injection，也就是依赖注入，是IOC思想的一种实现。

### 依赖

什么是依赖？看下面这段代码：

```
public class A
{
  public B b = new B();
}
```

上述代码中就是A依赖B。
我再这里理解为，A对象里面持有B对象，如果B对象不存在，那么A就无法成立，所以是A对象依赖B。

### 注入

什么是注入呢？看下面这段代码：

```
public class A
{
  public B b = null;
}

public class B {}

void Main()
{
  var a = new A();
  var b = new B();

  a.b = b;
}
```

从上面可以看出总共有两个对象，分别是a和b。可以看出现在的A对象是依赖B对象的。
**a.b=b;**这句代码就是将b对象注入到a对象的b成员中。
有时候可以把注入就理解成设置值。
依赖注入也就是吧某个对象依赖的对象进行赋值。

# DI

## QF中的IOC

QF是一种框架，是作者最近正在学习的一种框架，这个框架不要求会，我们就拿QF中的DI来进行详细的了解一下DI。
我们在使用DI方案的时候一般都离不开DI容器这个概念。育德时候DI容易也叫做IOC容器。就是：DIContainer 和 IOCContainer。
下面我们来看看QF中是如何使用IOC的。

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace QF.Master.Example
{    

    public class ServiceA
    {
        public void Say()
        {
            Debug.Log("I am ServiceA:" + this.GetHashCode());
        }
    }

    public class IOCExample : MonoBehaviour 
    {
        // 声明为需要注入的对象
        [Inject] 
        public ServiceA A {get;set;}

        void Start () 
        {
            // 创建实例容器
            var container = new QFrameworkContainer();

            // 注册类型
            container.Register<ServiceA>();

            // 注入对象（会自动查找 Inject Atrributet的对象)
            container.Inject(this);

            // 注入之后，就可以直接使用 A 对象了
            A.Say();        
        }
    }
}
```

上面是完整的案例，我们只要仔细看下面的代码：

```
// 创建实例容器
var container = new QFrameworkContainer();

// 注册类型
container.Register<ServiceA>();

// 注入对象（会自动查找 Inject Atrributet的对象)
container.Inject(this);

// 注入之后，就可以直接使用 A 对象了
A.Say();
```

首先我们创建了一个容器，并且在容器里注册了一个ServiceA对象。这些其实都是可以自己写一个脚本进行封装的。
接下来我们来看注入：**container.Inject(this);**这句就是注入对象，注入的就是刚才注册的对象。
接下来我们就可以使用this对象里的ServiceA对象了，为啥注入到A对象了，这里是框架给设计好了，只要在对应的对象上面添加[Inject]就代表是需要注入的对象。注入的时候就会对其进行赋值。
我们可以看下面的图进行理解：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191902943.png)


这是QF框架中的使用方法，我们不用会这种方法，因为只是针对QF框架的，我们学习的是IOC的这种思想。



## DI容器的强大之处

### 职责

一般情况下DI容器会提供如下的API：

```
注册类型：Register<TSource,TTarget>
注入:Inject(object obj)
解析:Resolve<T>()
123
```

注册和注入在刚才代码中已经有体现了。
在 DI Container 中，Resolve 实际上是根据类型返回实例。
DI容器的职责很简单，就是管理依赖和注入依赖。
DI容器中的容器代表的就是存储依赖的容器，容器里面装的就是依赖。
一般的DI容器利用的其实就是Dictonary<Type,object>来作为核心数据结构。
根据Type就可以得到依赖的对象，然后通过Inject注入到对象中。

### 强大之处

有了单例这个工具，当我们需要写一个独立模块的时候，很容易用单例就实现了。而且通过单例（或静态类）实现的独立模块，与其他模块之间的交互会变得非常容易。
这种结构好于直接撸的结构，毕竟它有一定的扩展性（只需实现单例），维护性上也还可以（模块独立），但是它很容易达到瓶颈，尤其是当单例的数量逐渐随着项目变多的时候。
因为单例的获取是没有限制的，任何层级的代码都可以获取到任何层级的单例，如下图示：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191902488.png)


虽然我们在预定上分了很多层，但是当我们的模块数量超过一定数量时，约定就很难遵循，因为我们访问模块的时候就需要去看一遍层级的示意图：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191902548.png)


这就造成项目达到一定规模，约定就变成了限制，让我们很难遵循。
从约定上，来说，我们怎么约定都可以，而约定的图示，就是上边的图。那些分层都是一些抽象的概念，比如游戏流程、主逻辑层、业务模块、框架层、底层，这些都是约定的结构。一般都是存在于文档上，并不会通过技术手段表达。
而从技术限制这个角度来说，单例的访问是没有限制的，就算是用最佳实践，也是没有限制的。而且单例难以表达约定上的分层的那些概念，因为只要一个模块变成单例了，那么这个模块就在技术上失去了层次关系。
如下图：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191902306.png)


如图所示，在使用单例的时候蓝牙和商店模块就在同一层级了，就失去了层级的约定。
使用单例时，项目结构的设计非常依赖于约定（文档等等）。
在项目规模比较少的时候，是没问题的，因为模块数量也比较少，但是一旦项目规模变大，模块数量变多，那么约定就会越来越难以遵循，就会非常容易导致项目结构的崩溃。
这个时候，有一种很简单的解决方式，就是最顶层模块都用单例，然后一些底层的模块，作为顶层的成员变量，从而达到逻辑层无法直接访问底层模块，而是必须通过顶层模块间接地使用底层模块服务。
如下图：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191902290.png)


这样就解决了单例结构无法表达层级的问题，但是同时也失去了单例带来的种种好处，比如易扩展，维护性（模块独立）。
现在不容易扩展模块了，因为要扩展模块需要考虑依赖关系，也不容易维护了，因为模块不独立了。
而这里不容易维护指的是各个模块不容易单独维护了，但是整体项目会更容易维护。
接下来就体现DI Container 的作用了，它的出现可以让各个模块容易维护，也容易扩展，同时让整个项目也同样容易维护，而且可以通过技术限制来分担一部分约定。



## 依赖管理

从上面解决单例问题的图我们可以看到，有大量的依赖关系，这个依赖关系我们可以理解成对象的持有或者变量。
就是因为这些依赖关系，造成了此结构的维护性和扩展性降低了。因为模块本身也是对象，对象就有声明周期，那么对象和是去new就很重要。
我们来看看下面的代码：

```
public  class ModuleA
{
  public ModuleB ModuleB;
}

public class ModuleB
{

}
```

从代码中我们可以知道ModuleA是依赖ModuleB，这样我们在使用ModuleA的时候要考虑A中ModuleB是不是有值。
创建ModuleB我们可以考虑在ModuleA内部创建和外部创建两种情况：

### 在ModuleA内部创建

看下列代码：

```
public  class ModuleA
{
  public ModuleB ModuleB = new ModuleB();
}
```

这种方式也很简单，创建ModuleA的时候ModuleB一定被创建了，但是此时的ModuleB是比较难共用的。

### 在ModuleA外部创建

看下列代码：

```
void main()
{
  var moduleA = new ModuleA();
  moduleA.ModuleB = new ModuleB();
}
```

这样看起来没问题，因为在外部创建过程中，我们非常清晰地知道，moduleA.ModuleB 是有实例的。 但是我们看下在 ModuleA 内部使用 ModuleB 时的情况，代码如下:

```
public class ModuleA
{
  public ModuleB ModuleB;

  ...

  void XXX()
  {
    ModuleB. // 在这里会想 ModuleB 到底有没有值？在哪里设置的值？
  }
}
```

接下来我们看看单例的解决方案：

```
public class ModuleA
{
  void XXX()
  {
    ModuleB.Instance.DoSomething();
  }
}
```

这样就不用考虑ModuleB的创建过程。
现在这种结构就是有大量的依赖，不易维护不易扩展也都出现在这个依赖上，因为依赖要考虑依赖对象的创建过程。

### 使用 DI Container 管理依赖

利用DI代码就变成下面的方式了：

```
public class ModuleA
{
  [Inject]
  public ModuleB ModuleB {get;set;}

  void XXX()
  {
    ModuleB.DoSomeThing(); // 放心用，因为不用考虑依赖的创建过程。
  }
}
```

我们只需要在启动程序的时候进行注册：

```
public static QFrameworkContainer Container {get;private set;}

void Main()
{
  Container = new Contaner();

  Container.Register<ModuleB>();
}
```

但是我们在创建ModuleA的时候需要注入对象：

```
void MethodA()
{
  App.Container.Inject(new ModuleA());
}
```

使用 DIContainer之后我们的结构就变成了下图所示：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191902598.png)


我们只需要，在模块内部声明模块作为变量，而不需要考虑创建过程，这样就导致每个模块都是可独立测试的（易维护），而扩展一个模块，不需要用单例实现，而是注册到类型容器即可，扩展性比单例还容易。这就是 DI Container 的最核心的优势。



# 域

我们来了解DIContainer 的一个基础知识：域

## 简介

域这个概念我们在学习C#的时候接触过，域的俗称就是代码块：

```
public class A
// 类范围内的域
{
  public void Main()
  // 方法范围内的域
  {

  }
}
```

从上面代码的注释就可以看出，代码块其实就是域
C# 的域的特性就是内部的域中的代码可以调用外部域中的变量。

## DIContainer中的域

首先我们要考虑一个问题，每次DIContainer在注入实例的时候，注入的是新的还是返回的同一个实例呢？
而这个问题所描述的行为指的是 DIContainer 的域。
我们来看看QF中是如何注入：

```
当注册依赖时，使用 Register<Type>() 方法时，每次注入都会创建新的实例。
当注册依赖时，使用 RegisterInstance(object obj) 方法时，每次注入都会使用 obj 实例。
```

我们来看看演示的代码：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace QF.Master.Example
{
    public class SomeService 
    {
        public void Description()
        {
            Debug.Log("SomeService:" + this.GetHashCode());
        }
    }

    public class SomeObject
    {
        public void Description()
        {
            Debug.Log("SomeObject:" + this.GetHashCode());
        }
    }

    public class IOCScopeExample : MonoBehaviour {

        [Inject]
        public SomeObject ObjA {get;set;}

        [Inject]
        public SomeObject ObjB {get;set;}

        [Inject]
        public SomeObject ObjC {get;set;}


        [Inject]
        public SomeService ServiceA {get;set;}

        [Inject]
        public SomeService ServiceB {get;set;}

        void Start () 
        {
            var container = new QFrameworkContainer();

            // 每次注入都创建新的实例
            container.Register<SomeObject>();

            // 每次注入都使用同一个实例
            container.RegisterInstance(new SomeService());

            // 注入
            container.Inject(this);

            ObjA.Description();
            ObjB.Description();
            ObjC.Description();
            ServiceA.Description();
            ServiceB.Description();
        }
    }
}
```

接下来我们看看运行的结果：

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191903164.png)


我们不难看到过 Register

() 注册的依赖，每次注入时创建新的实例，而通过 RegisterInstance(object obj) 注册的依赖，每次注入都返回同一个 obj 实例。
总结如下：



```
Register<T>，每次注入时创建新的实例。
RegisterInstance(object obj)，每次注入时，返回同一个 obj 对象。
```

# 依赖倒置原则

依赖倒置原则，即：Dependency Inversion Principle，很多时候缩写成 DIP。
这个原则包含了两个主题：

```
高层模块不应该依赖于低层模块，两者都应该依赖于抽象概念。
抽象接口不应该依赖于实现，而实现依赖于抽象接口
12
```

## 高层模块不应该依赖于低层模块，两者都应该依赖于抽象概念

我们来拿一个司机开车的例子，代码如下：

```
public class Driver
{
  // 司机的职责是开车
  public void Drive(Benz benz)
  {
    benz.Run();
  }
}

public class Benz
{
  public void Run()
  {
    Debug.Log("奔驰汽车开始运行")
  }
}

void Main()
{
  var lucas = new Driver();
  var benz = new Benz();

  lucas.Drive(benz);
}
```

目前A同学负责开发Driver类，B同学负责开发Benz类。这时候突然多了一个需求，那就是有一个同学的车换成了宝马，那我们需要重新创建一个BMW类。
然而因为A同学和B同学开发的类耦合性太强了，所以都需要重新改一下代码，这时候看看这句话：高层模块不应该依赖于低层模块，两者都应该依赖于抽象概念。这样我们把车和司机都依赖于抽象，更改后的代码是：

```
public class Driver
{
  // 司机的职责是开车
  public void Drive(ICar car)
  {
    car.Run();
  }
}

public interface ICar
{
  void Run();
}

//public class Benz : ICar
//{
//  public void Run()
//  {
//    Debug.Log("奔驰汽车开始运行")
//  }
//}

public class BMW : ICar
{
  public void Run()
  {
    Debug.Log("宝马汽车开始运行")
  }
}

void Main()
{
  var lucas = new Driver();
  var bmw = new BMW();

  lucas.Drive(bmw);
}
```

从上面代码可以看出，当更换车的时候，不需要修改高层的代码，只需要把车的类更换掉就行了。
实际上，要完全实现”高层模块不应该依赖于低层模块，两者都应该依赖于抽象概念“这一个主题的话，Driver 类也应该抽象出来一个 IDriver，不过对于目前的结构来说没有必要。

## 抽象接口不应该依赖于实现，而实现依赖于抽象接口

我们直接上代码：

```
var lucas = new Driver();
ICar car = new BMW();

lucas.Drive(car);
```

其中的 ICar car = new BMW() 就实现了“实现依赖于抽象接口”这一主题。
依赖这个概念需要再次更新了，上文中说到司机需要车才能当好司机，所以司机依赖车，那么现在 BMW 需要实现 ICar 接口，才能正常使用，那么 BMW 类依赖 ICar 接口，（实现依赖抽象接口)。
实际上依赖倒置原则的两个主题都在说一件事情，两个类之间的交互通过抽象（接口、抽象类）来完成。
而通过一个抽象，可以将两个类的变化隔离。即 A 类修改或扩展甚至是替换都不影响 B，反过来也是如此。
通过依赖倒置原则可以提高整个系统的灵活性和扩展性还有维护性。

# 通过DIContainer实现DIP

我们这时候来想想，其实依赖也就等于需求了。
而 QF 中的 DIContainer 支持前两种依赖管理，而市面上有个 IOC 容器，叫做 Zenject，支持以上全部的依赖关系。

## DIContainer 中的接口依赖管理（QF）

在上文中，我们说过QF中的依赖注入的时候DIContainer提供了两个API，分别是Register 与 RegisterInstance，分别是注入的时候是新的还是说同一个。
实际上这两者都有一些重载，即支持接口依赖的注册。
多说无用，放出来代码大家来体会一下：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace QF.Master.Example
{
    public interface ILoginService
    {
        void Login();
    }

    public class LoginService : ILoginService
    {
        public void Login()
        {
            Debug.Log("登录成功");
        }
    }

    public class IOCInterfaceExample : MonoBehaviour 
    {
        [Inject]
        public ILoginService LoginService {get;set;}

        void Start () 
        {
            var container = new QFrameworkContainer();

            container.RegisterInstance<ILoginService>(new LoginService());

            // Register 也支持注册接口依赖
            //container.Register<ILoginService,LoginService>();

            container.Inject(this);

            LoginService.Login();
        }
    }
}
```

代码很简单，代码中可以根据接口注入具体的实例，使脚本可以依赖 ILoginService 接口而不依赖于 LoginService 类。

# 控制反转

在上文我们已经了解了依赖倒置原则和依赖注入的方案。了解了上面两个概念。这样会让我们更容易了解控制反转。

## 控制正转

有反转就有正转，我们先来看看正转的代码：

```
public class ModuleA
{
  public ModuleB B = new ModuleB();
}

public class ModuleB
{

}
```

这个就是正转，就是依赖的对象在内部进行创建，其实这不叫正转，这是正常的控制过程。
控制的意思其实就是依赖的对象创建的过程。也就是上述代码中的B的创建控制过程。
正转的过程就是被依赖对象B的创建是由依赖对象A控制，谁依赖谁创建。
控制反转就好理解了，就是被依赖对象B的创建是由外部控制了，就不需要依赖对象A进行控制创建了。
控制反转代码如下：

```
public class ModuleA
{
  public ModuleB B;
}

public class ModuleB
{

}

void Main()
{
  var moduleA = new ModuleA();
  // 对象创建过程由外部控制
  moduleA.B = new ModuleB();
}
```

**控制反转意思是类依赖对象创建的过程由外部控制，而不是在类内部控制。**

# 依赖注入与控制反转

```
控制反转：依赖对象的创建过程交给外部控制。 
依赖注入：依赖对象在外部进行设置。
```

控制反转是设计原则，或者说是设计模式，而依赖注入是控制反转的具体实现。
控制反转的具体实现除了有常用的依赖注入还有一种方案叫依赖查找，这里我们不介绍。
控制反转和依赖注入有一些细微的差别，控制反转的重点在于依赖对象创建过程的控制，而依赖注入的重点在于依赖对象如何设置，实际上这两个概念都在说，依赖对象的创建和设置都在外边搞，不要在类内部搞。
