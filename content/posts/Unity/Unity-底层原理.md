---
title: "Unity-底层原理"
date: 2022-03-12T16:46:56+08:00

tags:
  - Unity
  - Mono

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192048985.png
---

## Unity是如何实现跨平台的

首先，我们要知道Unity,Mono,.Net 三者的关系。需要简单说一下.Net。

.Net拥有跨语言，跨平台性。

跨语言：就是只要是面向.Net平台的编程语言，用其中一种语言编写的类型就可以无缝的在另外一种语言编写的应用程序中互操作。

跨平台：一次编译，不需要任何代码修改，应用程序就可以运行在任意在.Net实现的平台上跑，即代码不依赖于操作系统，也不依赖硬件环境。一个.Net程序运行的核心在于.Net

CLR(公共语言运行时，或者称为.Net 虚拟机，类似java虚拟机的概念)，为了让.Net程序在其他平台（目前只能在.Net 平台,windows系统）上跑,微软官方还推出了在其他平台上(MacOs,Linux)跑的

.Net的实现，就推出了.Net Core。

然而，Unity引擎需求也是需要跨平台，支持多语言（C#，Js，Boo）。就参考微软开发.Net Core的概念，于是，推出了Mono.

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049788.png)

到这里，基本说明了.Net 与Mono和Unity的联系关系，其实没啥关系。做游戏都知道，肯定需要跨平台，不能只支持一种平台，不然每个对应的平台做出一种对应的编译器，那真的会累死。所以对于跨平台的需求，对于游戏开发而言，很重要。Unity的架构需求设计当然也需要这个特性。参考.Net依托CLR来实现设计思路，于是Mono就出来了。

## Mono介绍

**Mono**是一个由[Xamarin](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/Xamarin)公司所主持的自由开放源码项目。
Mono的目标是在尽可能多的平台上使.net标准的东西能正常运行的一套工具，核心在于“跨平台的让.net代码能运行起来“。
Mono组成组件：C# 编译器，CLI虚拟机，以及核心类别程序库。
Mono的编译器**负责生成符合公共语言规范的映射代码**，即公共中间语言（Common Intermediate Language，**CIL**），我的理解就是工厂方法实现不同解析。
IL的全称是 Intermediate Language，很多时候还会看到**CIL**（特指在.Net平台下的IL标准）。翻译过来就是中间语言。
它是一种属于通用语言架构和.NET框架的低阶的人类可读的编程语言。
CIL类似一个面向对象的汇编语言，并且它是完全基于堆栈的，它运行在虚拟机上（.Net Framework, Mono VM）的语言。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049879.png)

### **工作流程：**

1. 通过C#编译器mcs，将C#编译为IL（中间语言，byte code）
2. 通过Mono运行时中的编译器将IL编译成对应平台的原生码

### **编译器**

> C#编译器mcs：将C#编译为**IL**
> Mono Runtime编译器：将IL转移为**原生码**。

### **三种转译方式：**

**即时编译（Just in time,JIT）**：程序运行过程中，将CIL的byte code转译为目标平台的原生码。
**提前编译（Ahead of time,AOT）**：程序运行之前，将.exe或.dll文件中的CIL的byte code部分转译为目标平台的原生码并且存储，程序运行中仍有部分CIL的byte code需要JIT编译。
**完全静态编译（Full ahead of time,Full-AOT）**：程序运行前，将所有源码编译成目标平台的原生码。

### Unity跨平台的原理

> Mono运行时编译器支持将IL代码转为对应平台原生码
> IL可以在任何支持CLI,通用语言环境结构)中运行，IL的运行是依托于Mono运行时。

### IOS不支持jit编译原因：

> 机器码被禁止映射到内存，即封存了内存的可执行权限，变相的封锁了jit编译方式。

### **JIT编译**

> 将IL代码转为对应平台原生码并且将原生码映射到虚拟内存中执行。JIT编译的时候IL是在依托Mono运行时，转为对应的原生码后在依托本地运行。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049905.png)

### 优点：

1. 构建应用非常快
2. 由于Mono的JIT(Just In Time compilation ) 机制, 所以支持更多托管类库
3. 支持运行时代码执行
4. 必须将代码发布成托管程序集(.dll 文件 , 由mono或者.net 生成 )
5. Mono VM在各个平台移植异常麻烦，有几个平台就得移植几个VM（WebGL和UWP这两个平台只支持 IL2CPP）
6. Mono版本授权受限，C#很多新特性无法使用
7. iOS仍然支持Mono , 但是不再允许Mono(32位)应用提交到Apple Store

## IL2CPP介绍

IL2CPP 是 Unity一种新的脚本后处理(Scripting Backend)方式，针对.Net平台编译输出的IL（中间语言）进行处理。

### IL2CPP主要由两部分组成

AOT静态编译编译器（il2cpp.exe），把IL中间语言转换成CPP文件

运行时库（libil2cpp）
其中AOT将 IL转换为C++源码，再交给各平台的C++编译器进行编译，达到平台兼容的目的；运行时库则会提供诸如 垃圾回收、线程/文件获取、内部调用直接修改托管数据结构的原生代码 的服务与抽象。

#### AOT编译器

> IL2CPP AOT编译器名为il2cpp.exe。
> 在Windows上，您可以在`Editor \ Data \ il2cpp`目录中找到它。
> 在OSX上，它位于Unity安装的`Contents / Frameworks / il2cpp / build`目录中
> il2cpp.exe 是由C#编写的受托管的可执行程序，它接受我们在Unity中通过Mono编译器生成的托管程序集，并生成指定平台下的C++代码。

#### **运行时库**

> IL2CPP技术的另一部分是运行时库（libil2cpp），用于支持IL2CPP虚拟机的运行。
> 这个简单且可移植的运行时库是IL2CPP技术的主要优势之一！
> 通过查看我们随Unity一起提供的libil2cpp的头文件，您可以找到有关libil2cpp代码组织方式的一些线索
> 您可以在Windows的`Editor \ Data \ PlaybackEngines \ webglsupport \ BuildTools \ Libraries \ libil2cpp \ include`目录中找到它们
> 或OSX上的`Contents / Frameworks / il2cpp / libil2cpp`目录。

### 为何要转成CPP

- 运行效率快

> 根据官方的实验数据，换成IL2CPP以后，程序的运行效率有了1.5-2.0倍的提升。

- Mono VM在各个平台移植，维护非常耗时，有时甚至不可能完成

> Mono的跨平台是通过Mono VM实现的，有几个平台，就要实现几个VM，像Unity这样支持多平台的引擎，Mono官方的VM肯定是不能满足需求的。所以针对不同的新平台，Unity的项目组就要把VM给移植一遍，同时解决VM里面发现的bug。这非常耗时耗力。这些能移植的平台还好说，还有比如WebGL这样基于浏览器的平台。要让WebGL支持Mono的VM几乎是不可能的。

- 可以利用**现成的在各个平台的C++编译器**对代码执行**编译期优化**，这样可以进一步**减小最终游戏的尺寸并提高游戏运行速度**。

- 由于动态语言的特性，他们多半无需程序员太多关心内存管理，所有的内存分配和回收都由一个叫做GC（Garbage Collector）的组件完成。

虽然通过IL2CPP以后代码变成了静态的C++，但是内存管理这块还是遵循C#的方式，这也是为什么最后还要有一个 **IL2CPP VM**的原因：**它负责提供诸如GC管理，线程创建这类的服务性工作。**

但是由于去除了**IL加载和动态解析**的工作，**使得IL2CPP VM可以做的很小**，**并且使得游戏载入时间缩短**。

### 编译过程

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192049027.png)

在得到中间语言IL后，使用IL2CPP将他们重新变回C++代码，然后**再由各个平台的C++编译器直接编译成能执行的原生汇编代码。**

### 优点：

1. 相比Mono, 代码生成有很大的提高
2. 可以调试生成的C++代码
3. 可以启用引擎代码剥离(Engine code stripping)来减少代码的大小
4. 程序的运行效率比Mono高，运行速度快
5. 多平台移植非常方便
6. 相比Mono构建应用慢
7. 只支持AOT(Ahead of Time)编译

## ILRuntime

ILRuntime项目为基于C#的平台（例如Unity）提供了一个`纯C#实现`，`快速`、`方便`且`可靠`的IL运行时，使得能够在不支持JIT的硬件环境（如iOS）能够实现代码的热更新

### ILRuntime的优势

同市面上的其他热更方案相比，ILRuntime主要有以下优点：

- 无缝访问C#工程的现成代码，无需额外抽象脚本API
- 直接使用VS2015进行开发，ILRuntime的解译引擎支持.Net 4.6编译的DLL
- 执行效率是L#的10-20倍
- 选择性的CLR绑定使跨域调用更快速，绑定后跨域调用的性能能达到slua的2倍左右（从脚本调用GameObject之类的接口）
- 支持跨域继承
- 完整的泛型支持
- 拥有Visual Studio的调试插件，可以实现真机源码级调试。支持Visual Studio 2015 Update3 以及Visual Studio 2017和Visual Studio 2019
- 最新的2.0版引入的寄存器模式将数学运算性能进行了大幅优化

### C# vs Lua

目前市面上主流的热更方案，主要分为Lua的实现和用C#的实现，两种实现方式各有各的优缺点。

Lua是一个已经非常成熟的解决方案，但是对于Unity项目而言，也有非常明显的缺点。就是如果使用Lua来进行逻辑开发，就势必要求团队当中的人员需要同时对Lua和C#都特别熟悉，或者将团队中的人员分成C#小组和Lua小组。不管哪一种方案，对于中小型团队都是非常痛苦的一件事情。

用C#来作为热更语言最大的优势就是项目可以用同一个语言来进行开发，对Unity项目而言，这种方式肯定是开发效率最高的。

Lua的优势在于解决方案足够成熟，之前的C++团队可能比起C#，更加习惯使用Lua来进行逻辑开发。此外借助luajit，在某些情况下的执行效率会非常不错，但是luajit现在维护情况也不容乐观，官方还是推荐使用公版Lua来开发。

> 如果需要测试ILRuntime对比Lua的性能Benchmark，需要确认以下几点：

- ILRuntime加载的dll文件是`Release`模式编译的
- dll中对外部API的调用都进行了`CLR绑定`
- 确保`没有勾选Development Build`的情况下发布成正式真机运行包，而`不是在Editor中直接运行`
- 可以直接使用Demo工程中提供的性能测试进行对比

> ILRuntime设计上为了在开发时提供更多的调试支持，在Unity Editor中运行会有很多额外的性能开销，
> 因此在Unity Editor中直接测试并不能代表ILRuntime的实际运行性能。
