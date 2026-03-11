---
title: "Unity-网络开发（三）"
date: 2022-08-14T22:13:47+08:00

tags:
  - Unity
  - 网络

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192048985.png
---

# 大小端模式

## 什么是大小端模式

### 大端模式

是指数据的高字节保存在内存的低地址中

而数据的低字节保存在内存的高地址中

这样的存储模式有点儿类似于把数据当作字符串顺序处理

地址由小向大增加，数据从高位往低位放

符合人类的阅读习惯



### 小端模式

是指数据的高字节保存在内存的高地址中

而数据的低字节保存在内存的低地址中



### 举例说明

十六进制数据0x11223344



### 大端模式存储

11    22    33    44

0     1     2     3

低地址——>高地址



### 小端模式存储

44    33    22    11

0     1     2     3

低地址——>高地址

## 为什么有大小端模式

大小端模式其实是计算机硬件的两种存储数据的方式

我们也可以称大小端模式为 大小端字节序



对于我们来说，大端字节序阅读起来更加方便，为什么还要有小端字节序呢？

原因是，计算机电路先处理低位字节，效率比较高

计算机处理字节序的时候，不知道什么是高位字节，什么是低位字节

它只知道按顺序读取字节，先读第一个字节，再读第二个字节

如果是大端字节序，先读到的就是高位字节，后读到的就是低位字节

小端字节序正好相反



因为计算机都是从低位开始的

所以，计算机的内部处理都是小端字节序



但是，我们人类的读写习惯还是大端字节序

所以，除了计算机的内部处理

其它场合几乎都是大端字节序，比如网络传输和文件存储



一般情况下，操作系统都是小端模式，而通讯协议都是大端模式

但是具体的模式，还是要根据硬件平台，开发语言来决定

主机不同，开发语言不同 可能采用的大小端模式也会不一致

## 大小端模式对于我们的影响

只有读取的时候，才必须区分大小端字节序，其它情况都不用考虑



因此对于我们来说，在网络传输当中我们传输的是字节数组

那么我们在收到字节数组进行解析时，就需要考虑大小端的问题

虽然TCP/IP协议规定了在网络上必须采用网络字节顺序（大端模式）

但是具体传输时采用哪种模式，都是根据前后端语言、设备决定的



在进行网络通讯时，前后端语言不同时，可能会造成大小端不统一

一般情况下

C# 和 Java/Erlang/AS3 通讯需要进行大小端转换 因为C#是小端模式 Java/Erlang/AS3是大端模式

C# 与 C++通信不需要特殊处理 他们都是小端模式

## 大小端转换

### 判断是大小端哪种模式

 print("是否是小端模式:" + BitConverter.IsLittleEndian);

### 简单的转换API 只支持几种类型

```c
//转换为网络字节序 相当于就是转为大端模式
//1. 本机字节序转网络字节序
int i = 99;
byte[] bytes = BitConverter.GetBytes(IPAddress.HostToNetworkOrder(i));
//2. 网络字节序转本机字节序
int receI = BitConverter.ToInt32(bytes, 0);
receI = IPAddress.NetworkToHostOrder(receI);
```

### 通用的转换方式

```c
//数组中的倒序API
//如果后端需要用到大端模式 那么我们进行判断
//如果当前是小端模式 就进行一次 大小端转换
if(BitConverter.IsLittleEndian) Array.Reverse(bytes);
```

## 总结

大小端模式会根据主机硬件环境不同、语言不同而有所区别

当我们前后端是不同语言开发且运行在不同主机上时

前后端需要对大小端字节序定下统一的规则



一般让前端迎合后端，因为字节序的转换也是会带来些许性能损耗的

网络游戏中要尽量减轻后端的负担



一般情况下

C# 和 Java/Erlang/AS3 通讯需要进行大小端转换 前端C#从小变大

C# 与 C++通信不需要特殊处理



我们不用死记硬背和谁通讯要注意大小端模式

当开发时，发现后端收到的消息和前端发的不一样

在协议统一的情况下，往往就是因为大小端造成的

这时我们再转换模式即可



注意：

Protobuf已经帮助我们解决了大小端问题

即使前后端语言不统一

使用它也不用过多考虑字节序转换的问题

# 消息加密解密

## 什么是消息加密解密

我们在网路传输时，会把数据转换为字节数组以2进制的形式进行传输

理论上来说，如果有人截取篡改了消息，或者从前端发假消息给后端

就可能产生作弊行为

消息的加密解密 可以有效避免作弊行为的产生



### 加密

采用一些方式对数据进行处理后，使数据从表面上看，已经不能表达出原有的意思

别人就算获取到了你的信息，也无法知道你的内容的含义和规则

这样可以让我们的数据更加的安全，降低被篡改的可能性



### 解密

通过对加密过的数据采用某些方法，去还原原有数据，从而获取目标数据



其实就是在

发消息时，对我们的消息2进制数据进行加密（一般只对消息体加密）

收到消息时，对2进制数据进行解密（一般只对消息体解密）

## 加密是否是100%安全？

一定记住加密只是提高破解门槛，没有100%保密的数据

通过各种尝试始终是可以破解加密规则的，只是时间问题

加密只能提升一定的安全性



对于大多数情况下已经够用了，除非专门有人针对你们的产品进行破解

但是遇到这种情况 也证明你的产品已经足够成功了

## 加密解密的相关名词解释

明文：待加密的报文（内容）

密文：加密后的报文（内容）

密钥：加密过程中或解密过程中输入的数据

算法：将明文和密钥相结合进行处理，生成密文的方法，叫加密算法。将密文和密钥相结合进行处理，生成明文的方法，叫解密算法

## 了解加密算法分类

### 1.单向加密

​    将数据进行计算变成另一种固定长度的值，这种加密是不可逆的



​    常用算法

​    MD5、SHA1、SHA256等



​    用途：这种加密在网络传输中不会使用，主要用到其它功能当中，比如密码的单向加密



### 2.对称加密技术

​    使用同一个密钥，对数据镜像加密和解密（用密钥对明文加密，用密钥对密文解密）



​    常用算法

​    DES、3DES、IDEA、AES等



​    优点：计算量小，加密速度快、效率高

​    缺点：如果知道了密钥和算法，就可以进行解密



​    用途：网路通讯中可以使用对称加密技术，这个密钥可以是由后端下发的，每次建立通讯后都会变化的



### 3.非对称加密技术

​    在加密过程中，需要一对密钥，不公开的密钥称为私钥，公开的那一个密钥称为公钥

​    也可以称为公开密钥加密

​    从一对密钥中的任何一个密钥都不能计算出另一个密钥

​    使用一对密钥中的任何一个加密，只有另一个密钥才能解密。如果截获公钥加密数据，没有私钥也无法解密



​    常用算法

​    RSA、DSA等



​    优点：安全性高，即使获取到了公钥，没有私钥也无法进行解密

​    缺点：算法复杂，加密速度较慢



​    用途：对安全性要求较高的场景，并且可以接受较慢的加密速度的需求可以使用非对称加密技术

​        以后在对接一些支付SDK时经常会看到平台提供的就是非对称加密技术



关于这些加密算法

有很多的别人写好的第三发加密算法库

可以直接获取用于在程序中对数据进行加密

也可以自己基于加密算法原理来设计自己的规则

## 用简单的异或加密感受加密的作用

```c
//异或加密特点
//密钥为一个整数
//明文 异或 密钥 得到 密文
//密文 异或 密钥 得到 明文

TestMsg msg = new TestMsg();
msg.ListInt.Add(1);
msg.TestBool = false;
msg.TestD = 5.5;
msg.TestInt32 = 99;
msg.TestMap.Add(1, "刘英博");
msg.TestMsg2 = new TestMsg2();
msg.TestMsg2.TestInt32 = 88;
msg.TestMsg3 = new TestMsg.Types.TestMsg3();
msg.TestMsg3.TestInt32 = 66;

msg.TestHeart = new GameSystemTest.HeartMsg();
msg.TestHeart.Time = 7777;

byte[] bytes = NetTool.GetProtoBytes(msg);
//异或加密算法
//密钥声明
byte s = 55;
//异或加密
for (int i = 0; i < bytes.Length; i++)
    bytes[i] ^= s;

//异或解密
for (int i = 0; i < bytes.Length; i++)
    bytes[i] ^= s;

TestMsg msg2 = NetTool.GetProtoMsg<TestMsg>(bytes);
print(msg2.TestMsg3.TestInt32);
```

# Protobuf

## 什么是Protobuf

Protobuf全称是 protocol-buffers（协议缓冲区）

是谷歌提供给开发者的一个开源的协议生成工具

它的主要工作原理和我们之前做的自定义协议工具类似

只不过它更加的完善，可以基于协议配置文件生成

C++、Java、C#、Objective-C、PHP、Python、Ruby、Go

等等语言的代码文件



它是商业游戏开发中常常会选择的协议生成工具

有很多游戏公司选择它作为协议工具来进行网络游戏开发

因为它通用性强，稳定性高，可以节约出开发自定义协议工具的时间



protocol-buffers官网

https://developers.google.com/protocol-buffers

## Protobuf的使用流程

在官网中前往下载地址

 protocol-buffers官网

 https://developers.google.com/protocol-buffers

下载protobuf-csharp然后有两种方式选择

### 在unity中使用源码

解压后打开csharp\src将Google.Protobuf这个文件夹直接放入到unity中

### 在unity中使用Google.Protobuf.dll

解压后打开csharp\src中的Google.Protobuf.sln

选择Google.Protobuf右键生成 dll文件

在csharp\src\Google.Protobuf\bin\Debug路径下找到对应.net版本的Dll文件（我们使用4.5即可）

将net45中的dll文件导入到Unity工程中的Plugins插件文件夹中

## .proto文件

```c
// 指定版本
syntax = "proto3";
// C#中的namespace
package ProtoTest
 
option optimize_for = SPEED;
 
// java文件路径
option java_package = "com.montior.proto";
 
// java文件名称
option java_outer_classname = "MonitorData";
 
// 消息结果。
message MsgResult {
    // 结果码。
    int32 code = 1;
    // 错误消息。
    string err_msg = 2;
 
}
 
// 接收包
message TaskProtocol {
    // 数据类型
    int32 packType = 1;
 
    // 具体数据
    bytes content = 3;
 
}
 
// 包的类型
enum PackType {
    LOGIN = 0;
    CREATE_TASK = 2;
    DELETE_TASK = 3;
}
 
message LoginPack{
    string username = 1;
}
 
message LoginPack2{
    string username = 1;
}
 
message CreateTaskPack{
    string taskId = 1;
    string taskName = 2;
}
```

1.message：消息类型，类似于一个类

2.package：包名，CSharp中的命名空间，用来防止不同消息类型的冲突

3.enum：枚举，这个需要我说吗？

4.option：选项，说明下我这边用到的
option java_package = “com.example.foo”;// java文件路径
option java_outer_classname = “Ponycopter”;// java文件名称
option optimize_for = SPEED;//可以被设置为 SPEED, CODE_SIZE,or LITE_RUNTIME。这些值将通过如下的方式影响C++及java代码的生成：

注：以上选项，CSharp都用不着的，就是写着玩儿....

5.数据类型

| protobuf 数据类型 | 描述                             |
| ----------------- | -------------------------------- |
| bool              | 布尔类型                         |
| double            | 64位浮点数                       |
| float             | 32为浮点数                       |
| int32             | 32位整数                         |
| uin32             | 无符号32位整数                   |
| int64             | 64位整数                         |
| uint64            | 64为无符号整                     |
| sint32            | 32位整数，处理负数效率更高       |
| sing64            | 64位整数 处理负数效率更高        |
| fixed32           | 32位无符号整数                   |
| fixed64           | 64位无符号整数                   |
| sfixed32          | 32位整数、能以更高的效率处理负数 |
| sfixed64          | 64为整数                         |
| string            | 只能处理 ASCII字符               |
| bytes             | 用于处理多字节的语言字符、如中文 |

6.关键字

| 指定字段 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| required | 表示是一个必须字段，必须相对于发送方，在发送消息之前必须设置该字段的值，对于接收方，必须能够识别该字段的意思。发送之前没有设置required字段或者无法识别required字段都会引发编解码异常，导致消息被丢弃。 |
| optional | 表示是一个可选字段，可选对于发送方，在发送消息时，可以有选择性的设置或者不设置该字段的值。对于接收方，如果能够识别可选字段就进行相应的处理，如果无法识别，则忽略该字段，消息中的其它字段正常处理。---因为optional字段的特性，很多接口在升级版本中都把后来添加的字段都统一的设置为optional字段，这样老的版本无需升级程序也可以正常的与新的软件进行通信，只不过新的字段无法识别而已，因为并不是每个节点都需要新的功能，因此可以做到按需升级和平滑过渡。 |
| repeated | 表示该字段可以包含0~N个元素。其特性和optional一样，但是每一次可以包含多个值。可以看作是在传递一个数组的值。 |

## Protoc

在官网中前往下载地址

 protocol-buffers官网

 https://developers.google.com/protocol-buffers

写好 proto 文件之后用 protoc 编译器将 .proto文件编译成目标语言。

### Unity中protoc使用

下载Protobuf-Unity插件放入Unity工程
`Protobuf-Unity下载链接`：https://github.com/5argon/protobuf-unity

Unity工程打开`Editor/Preferences/Protobuf`并将下载的Protoc路径填入Path to protoc中
![在这里插入图片描述](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242132910.png)这个插件可以遍历Unity工程目录下的所有proto文件，并生成对应的c#文件。

# 网络管理框架

一个小Demo主要是为了理解网络框架的大概流程，都是基础的C#

https://github.com/liuyingbor/NetDemo

如果想深入了解网络框架Unity商城的BestHTTP强烈推荐。
