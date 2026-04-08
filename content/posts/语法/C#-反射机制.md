---
title: C#反射机制
tags:
  - C#
  - 语法
categories:
  - C#
date: 2021-12-05 21:26:10
cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191917907.png
---

## C#反射机制

资料转载自知乎:https://zhuanlan.zhihu.com/p/41282759

### **何为反射?**

首先我们通过两个实例来说明反射的大体概念。

B超:大家体检的时候大概都做过B超,B超可以透过肚皮探测到你内脏的生理情况。这是如何做到的呢?B超是B型超声波,它可以透过肚皮通过向你体内发射B型超声波,当超声波遇到内脏壁的时候就会产生一定的“回音”反射,然后把“回音”进行处理就可以显示出内脏的情况了。

地球内部结构:地球的内部结构大体可以分为三层:地壳、地幔和地核。如何在地球表面不用深入地球内部就知道其内部的构造呢?我们可以向地球发射“地震波”,“地震波”分两种一种是“横波”,另一种是“纵波”。“横波”只能穿透固体,而“纵波”既可穿透固体又可以穿透液体。通过在地面对纵波和横波的反回情况,我们就可以大体断定地球内部的构造了。

大家注意到这两个例子的共同特点,就是从一个对象的外部去了解对象内部的构造,而且都是利用了波的反射功能。在.NET中的反射也可以实现从对象的外部来了解对象(或程序集)内部结构的功能,哪怕你不知道这个对象(或程序集)是个什么东西,另外.NET中的反射还可以运态创建出对象并执行它其中的方法。

反射是.NET中的重要机制,通过反射,可以在运行时获得程序或程序集中每一个类型(包括类、结构、委托、接口和枚举等)的成员和成员的信息。有了反射,即可对每一个类型了如指掌。另外我还可以直接创建对象,即使这个对象的类型在编译时还不知道。

为什么使用反射,而不直接引用它的dll或者类型呢?

例如你有个main.exe,需要使用say.dll,draw.dll,突然客户说我们要添加一个跑的功能,那么只需要按照我们约定的规则做一个run的dll,之前的main.exe不需要做任何修改(就是不需要再去导入run.dll,其中需要其他的设计来规范),在main.exe中就能直接使用run.dll了。

其实,我们已经在不自觉地使用它了,举个最简单的例子,当你在VS的设计器里拖入一个控件后,设计器会通过反射获取这个控件的属性,并提供你进行设置。那么,问题来了,为什么要用反射呢?因为设计器在做的时候,根本不可能预知将来有什么控件会被你拖入进去。

### **反射的用途简要介绍**

反射的用途大体总结如下,我们会在下面详细的进行介绍。
(1)使用Assembly定义和加载程序集,加载在程序集清单中列出模块,以及从此程序集中查找类型并创建该类型的实例。
(2)使用Module了解包含模块的程序集以及模块中的类等,还可以获取在模块上定义的所有全局方法或其他特定的非全局方法。
(3)使用ConstructorInfo了解构造函数的名称、参数、访问修饰符(如pulic 或private)和实现详细信息(如abstract或virtual)等。
(4)使用MethodInfo了解方法的名称、返回类型、参数、访问修饰符(如pulic 或private)和实现详细信息(如abstract或virtual)等。
(5)使用FiedInfo了解字段的名称、访问修饰符(如public或private)和实现详细信息(如static)等,并获取或设置字段值。
(6)使用EventInfo了解事件的名称、事件处理程序数据类型、自定义属性、声明类型和反射类型等,添加或移除事件处理程序
(7)使用PropertyInfo了解属性的名称、数据类型、声明类型、反射类型和只读或可写状态等,获取或设置属性值。
(8)使用ParameterInfo了解参数的名称、数据类型、是输入参数还是输出参数,以及参数在方法签名中的位置等。

反射用到的主要类:

System.Type 类--通过这个类可以访问任何给定数据类型的信息。
System.Reflection.Assembly类--它可以用于访问给定程序集的信息,或者把这个程序集加载到程序中。
System.Type类:System.Type 类对于反射起着核心的作用。但它是一个抽象的基类,Type有与每种数据类型对应的派生类,我们使用这个派生类的对象的方法、字段、属性来查找有关该类型的所有信息。获取给定类型的Type引用有3种常用方式:

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924905.jpg)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924797.jpg)

### **Type类的属性:**

Name 数据类型名

FullName 数据类型的完全限定名(包括命名空间名)
Namespace 定义数据类型的命名空间名
IsAbstract 指示该类型是否是抽象类型

IsArray 指示该类型是否是数组

IsClass 指示该类型是否是类
IsEnum 指示该类型是否是枚举
IsInterface 指示该类型是否是接口
IsPublic 指示该类型是否是公有的
IsSealed 指示该类型是否是密封类
IsValueType 指示该类型是否是值类型

### **Type类的方法:**

GetConstructor(), GetConstructors():返回
ConstructorInfo类型,用于取得该类的构造函数的信息
GetEvent(), GetEvents():返回EventInfo类型,用于取得该类的事件的信息
GetField(), GetFields():返回FieldInfo类型,用于取得该类的字段(成员变量)的信息
GetInterface(), GetInterfaces():返回InterfaceInfo类型,用于取得该类实现的接口的信息
GetMember(), GetMembers():返回MemberInfo类型,用于取得该类的所有成员的信息
GetMethod(), GetMethods():返回MethodInfo类型,用于取得该类的方法的信息
GetProperty(), GetProperties():返回PropertyInfo类型,用于取得该类的属性的信息可以调用这些成员,其方式是调用Type的InvokeMember()方法,或者调用MethodInfo, PropertyInfo和其他类的Invoke()方法。

### **反射的具体使用**

(1)我们首先定义一个类用来测试反射

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924938.jpg)

该类就是一个普通的类,我们下面就使用它测试反射的各种常见操作。

(2)查看类中的构造方法
我们可以根据对象进而查看内部的构造函数以及参数。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924955.jpg)

(3)用构造函数动态生成对象
我们使用构造函数动态创建一个新的对象,如下代码所示:

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924817.jpg)

(4) 用Activator生成对象

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925381.jpg)

上面代码中,Activator.CreateInstance第一个参数为需要创建对象的类型,后面的为调用构造函数的参数,上面的三种格式都可以,只是调用的构造函数不同而已。

(5)查看类中的属性

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925621.jpg)

(6)查看类中的public方法

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925591.jpg)

(7)查看类中的public字段

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925222.jpg)

(8)用反射生成对象,并调用属性、方法和字段进行操作

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925479.jpg)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925822.jpg)

(9)System.Reflection.Assembly介绍
Assembly类可以获得程序集的信息,也可以动态的加载程序集,

以及在程序集中查找类型信息,并创建该类型的实例。使用Assembly类可以降低程序集之间的耦合,有利于软件结构的合理化。通过程序集名称返回Assembly对象

Assembly ass = Assembly.Load("ClassLibrary831");

通过DLL文件名称返回Assembly对象

Assembly ass =
Assembly.LoadFrom("ClassLibrary831.dll");

通过Assembly获取程序集中类

Type t = ass.GetType("ClassLibrary831.NewClass");

//参数必须是类的全名
通过Assembly获取程序集中所有的类

Type[] t = ass.GetTypes();

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925413.jpg)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925330.jpg)

(10)System.Reflection.Assembly使用

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925424.jpg)

(11)反射当前项目中的类

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925461.jpg)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925748.jpg)

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191925631.jpg)
