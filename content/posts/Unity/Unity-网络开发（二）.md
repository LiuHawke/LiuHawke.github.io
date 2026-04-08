---
title: "Unity-网络开发（二）"
date: 2022-05-16T15:06:06+08:00

tags:
  - Unity
  - 网络

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192048985.png
---

# 网络通信

## 网络游戏通信方案概述

### 弱联网和强联网游戏

网络游戏是以C/S模型为基础进行开发的由客户端和服务端组成

弱联网游戏：

这种游戏不会频繁的进行数据通信，客户端和服务端之间每次连接只处理一次请求，服务端处理完客户端的请求后返回数据后就断开连接了

强联网游戏：

这种游戏会频繁的和服务端进行通信，会一直和服务端保持连接状态，不停的和服务器之间交换数据通过之前的知识我们知道，网络游戏是以C/S模型为基础进行开发的由客户端和服务端组成

弱联网游戏代表：

一般的三消类休闲游戏、卡牌游戏等都会是弱联网游戏，这些游戏的核心玩法都由客户端完成，客户端处理完成后只是告诉服务端一个结果，服务端验证结果即可，不需要随时通信

比如：开心消消乐、刀塔传奇、我叫MT等等

强联网游戏代表：

一般的MMORPG（角色扮演）、MOBA（多人在线竞技游戏）、ACT（动作游戏）等等都会是强联网游戏，这些游戏的部分核心逻辑是由服务端进行处理，客户端和服务端之间不停的在同步信息

比如：王者荣耀、守望先锋、和平精英等等

### 长连接和短连接游戏

长连接和短连接游戏是按照网络游戏通信特点来划分的

弱联网游戏——>短连接游戏

强联网游戏——>长连接游戏



短连接游戏：

需要传输数据时，建立连接，传输数据，获得响应，断开连接

通信特点：需要通信时再连接，通信完毕断开连接

通信方式：HTTP超文本传输协议、HTTPS安全的超文本传输协议（他们本质上是TCP协议）



长连接游戏：不管是否需要传输数据，客户端与服务器一直处于连接状态，除非一端主动断开，或

者出现意外情况（客户端关闭或服务端崩溃等）

通信特点：连接一直建立，可以实时的传输数据

通信方式：TCP传输控制协议 或 UDP用户数据报协议

### Socket、HTTP、FTP

Socket：网络套接字，是对网络中不同主机上的应用进程之间进行双向通信的端点的抽象，一个套接字就是网络上进程通信的一端，提供了应用层进程利用网络协议交换数据的机制

主要用于制作长连接游戏（强联网游戏）

Http/Https：(安全的)超文本传输协议，是一个简单的请求-响应协议，它通常运行在TCP协议之上，它指定了客户端可能发送给服务端什么样的信息以及得到什么样的响应。

主要用于制作短连接游戏（弱联网游戏），也可以用来进行资源下载

FTP：文件传输协议，是用于在网络上进行文件传输的一套标准协议，可以利用它来进行网络上资源的下载和上传。它也是基于TCP的传输，是面向连接的，为文件传输提供了可靠的保证

## 网络通信基础

### IP地址和端口类

```c++
        #region IPAddress类
        //命名空间：System.Net;
        //类名：IPAddress

        //初始化IP信息的方式

        //1.用byte数组进行初始化
        byte[] ipAddress = new byte[] { 118, 102, 111, 11 };
        IPAddress ip1 = new IPAddress(ipAddress);

        //2.用long长整型进行初始化
        //4字节对应的长整型 一般不建议大家使用
        IPAddress ip2 = new IPAddress(0x79666F0B);

        //3.推荐使用的方式 使用字符串转换
        IPAddress ip3 = IPAddress.Parse("118.102.111.11");

        //特殊IP地址
        //127.0.0.1代表本机地址

        //一些静态成员
        //获取可用的IPv6地址
        //IPAddress.IPv6Any

        #endregion

        #region IPEndPoint类
        //命名空间：System.Net;
        //类名：IPEndPoint
        //IPEndPoint类将网络端点表示为IP地址和端口号，表现为IP地址和端口号的组合

        //初始化方式
        IPEndPoint ipPoint = new IPEndPoint(0x79666F0B, 8080);

        IPEndPoint ipPoint2 = new IPEndPoint(IPAddress.Parse("118.102.111.11"), 8080);
        #endregion

        #region 总结
        //程序表示IP信息
        IPAddress ip = IPAddress.Parse("IPv4地址");
        //程序表示通信目标
        IPEndPoint point = new IPEndPoint(ip, 8080);
        #endregion
```

### 域名解析

域名解析也叫域名指向、服务器设置、域名配置以及反向IP登记等等，说得简单点就是将好记的域名解析成IP，IP地址是网络上标识站点的数字地址，但是IP地址相对来说记忆困难，所以为了方便记忆，采用域名来代替IP地址标识站点地址。

比如 我们要登录一个网页 www.baidu.com 这个就是域名 我们可以通过记忆域名来记忆一个远端服务器的地址，而不是记录一个复杂的IP地址

域名解析就是域名到IP地址的转换过程。域名的解析工作由DNS服务器完成，我们在进行通信时有时会有需求通过域名获取IP

域名系统（英文：Domain Name System，缩写：DNS）是互联网的一项服务，它作为将域名和IP地址相互映射的一个分布式数据库，能够使人更方便地访问互联网，是因特网上解决网上机器命名的一种系统，因为IP地址记忆不方便，就采用了域名系统来管理名字和IP的对应关系

```c++
        #region IPHostEntry类
        //命名空间：System.Net
        //类名：IPHostEntry
        //主要作用：域名解析后的返回值 可以通过该对象获取IP地址、主机名等等信息
        //该类不会自己声明，都是作为某些方法的返回值返回信息，我们主要通过该类对象获取返回的信息

        //获取关联IP       成员变量:AddressList
        //获取主机别名列表  成员变量:Aliases
        //获取DNS名称      成员变量:HostName
        #endregion

        #region Dns类
        //命名空间：System.Net
        //类名：Dns
        //主要作用：Dns是一个静态类，提供了很多静态方法，可以使用它来根据域名获取IP地址

        //常用方法
        //1.获取本地系统的主机名
        print(Dns.GetHostName());

        //2.获取指定域名的IP信息
        //根据域名获取
        //同步获取
        //注意：由于获取远程主机信息是需要进行网路通信，所以可能会阻塞主线程
        IPHostEntry entry = Dns.GetHostEntry("www.baidu.com");
        for (int i = 0; i < entry.AddressList.Length; i++)
        {
           print("IP地址：" + entry.AddressList[i]);
        }
        for (int i = 0; i < entry.Aliases.Length; i++)
        {
           print("主机别名" + entry.Aliases[i]);
        }
        print("DNS服务器名称" + entry.HostName);

        //异步获取
        GetHostEntry();
        #endregion
```

如果不知道对方的IP地址，想通过域名和对方进行通信，可以通过Dns类通过域名得到IP地址后再和对方建立连接并通信

### 序列化和反序列化2进制数据

#### 网络通信中传输的数据

在网络通信中

把想要传递的类对象信息序列化为2进制数据（一般为byte字节数组）

再将该2进制数据通过网络传输给远端设备

远端设备获取到该2进制数据后再将其反序列化为对应的类对象

![image-20220518003846852](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242122585.png)

序列化：

将类对象信息转换为可保存或传输的格式的过程

反序列化：

与序列化相对，将保存或传输过来的格式转换为类对象的过程

比如

将C#类对象序列化为xml、json、2进制三种格式的数据保存在本地，达到持久化的目的，再将保存在本地的持久化数据文件反序列化为C#类对象

#### 字符编码

字符编码（英语：Character encoding）也称字集码

是把字符集中的字符，编码为指定集合中某一对象，以便文本在计算机中存储或通过网络进行传递。

**说人话：计算机里只能存数字（2机制），所以如果文字字符想要进行存储的话，就需要把对应的文字字符转换为数字才能进行处理，而字符编码就是文字字符在计算机中和数值的对应关系，是人为定义的一种映射规则。**比如

ASCII码（一种字符编码规则）中 数值65 用来映射 字符A

我们存储 A 这个字符，本质上存在内存中的是数值65对应的2进制是0100 0001

 

常见的一些字符编码规则有

**每个国家针对自己国家语言制定的编码规则（因为语言文字的数量各不相同）**

**ASCII码（美国）、GB2312编码（中国）、Shift_JIS编码（日本）、Euc-kr（韩国）等等**

世界通用的编码规则（把所有语言统一到一套编码里）

**Unicode编码 以及 基于Unicode实现的编码规则**

**UTF-8、UTF-16、UTF-32**

#### 乱码

假设我们的一个文件是采用中国制定的GB2312编码进行编辑存储的，而此时我们并不使用GB2312这个编码规则去读取文件，而是采用其它的规则，比如日本制定的Shift_JIS编码读取该文件，那么**由于编码格式的存读不统一就会造成乱码的出现。因为不同的编码规则，字符和数值的映射关系是不同的。**

比如：130这个数值在法语编码中代表了`é`，在希伯来语编码中却代表了字母`Gimel` (`ג`)，在中文编码中又会代表另一个符号。

#### **ASCII码**

在计算机内部，所有的信息最终都是一个二进制值。

一个二进制数就是一位（bit），有0和1两种状态。一个字节（byte）是8个二进制数组成的，所以1 byte = 8 bit。

也就是说一个字节一共可以用来表示256种不同的状态，从00000000到11111111。如果每一个状态都代表一个符号的话，那么一个字节可以用来表示256个符号。

上个世界60年代，美国制定了一套算是最早的字符编码，制定了一套基于英文字符与二进制位之间的对应关系。这套标准被称为ASCII码，一直使用到今天。

ASCII码一共规定了128个字符的编码。比如：

字符 A = 65（十进制数）= 0100 0001（二进制数）

字符 1 = 49（十进制数）= 0011 0001（二进制数）等等

下图为ASCII码的对照表

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123917.png)

这128个字符的编码规则，只占用了一个字节的后面7位，最前面的一位统一规定为0。  

英文国家使用128个字符编码就足够了，但是如果用来表示其它国家的语言，128个符号是远远不够的，于是乎就出现了非ASCII码们。

#### **非ASCII码**

非ASCII码基本都是基于ASCII码进行的扩充，他们都保留了ASCII码0~127这段编码的规范。也就是说非ASCII码的前面部分往往是和ASCII码的规则是相同的。

对于一些欧洲国家，他们使用一个字节便可以表示完自己所有的文字，他们利用了字节中闲置的最高位编入新的符号，因为我们知道ASCII码的编码规则是：一个字节中的8位，只占用了一个字节的后面7位，最前面的一位统一规定为0。所以这些语言系统中文字较少的国家让最前面的一位可以为1，他们就可以为自己的文字在128~255这一段加入新的对应规则。

比如：130这个数值在法语编码中代表了`é`，在希伯来语编码中却代表了字母`Gimel` (`ג`)

这种一个字节就把字符表示完的做法只适用于语言系统中文字较少的国家，因为他们的语言的字母是有限的。

对于使用象形文字的国家来说，一个字节完全不够用！比如中国，我们的汉字多达10万左右，一个字节最多也只能表示256种符号，是远远不够的。所以必须使用多个字节来表示一个符号。比如我们前面提到的中国的简体中文GB2312编码，是使用两个字节表示一个汉字，所以理论上来说可以表示256x256=65536个符号。

所以所谓的非ASCII码，就是指的除了ASCII码以外的编码格式，每个国家都至少有1种针对自己语言文字的编码格式，每一个编码格式中 数值和字符的对应关系都可能不相同。这也就造成了前面说到的乱码问题。在全世界范围内进行网络通信时，如果每个国家都使用不统一的编码格式，那么出现乱码的情况将随处可见。

因此随着互联网的发展，人们决定要制定一套全世界统一的的编码规则，将世界上所有的符号都纳入其中，为每一个符号赋予独一无二的编码（2进制数值）。那么这样就不会出现乱码问题，影响信息的传递了。

#### **Unicode**

Unicode可以理解为是 Unique Code 的简写，翻译过来是“唯一的编码”。

它出现的主要原因就是用来解决乱码问题的，它将世界上所有的符号都纳入其中，每一个符号都为其分配一个独一无二的二进制数表示它，那么乱码问题就会消失。

Unicode是一个很大的集合，现在的规模可以容纳100多万个符号，每个符号对应的二进制数都不一样。这样就确保了不同语言的字符不会再有冲突。

那么这样可能就存在一个问题，就是有的符号用1个字节8位就可以表示了，有的符号可能需要使用2个字节16位甚至3个字节24位才能表示。就比如说ASCII码，它的存储规则就是一个字节存储一个字符，那么当我们使用Unicode编码时，到底用几个字节来存储字符呢？

因此我们需要注意：**Unicode编码只是一个符号集，它只规定符号和二进制的对应关系，并没有规定这个二进制数值应该如何存储。**

**而UTF-8、UTF-16、UTF-32三种编码格式才是基于Unicode实现的具体编码方案**

UTF-8编码：可变字节编码方案，可以根据实际情况使用1个、2个、3个、4个字节来存储字符

UTF-16编码：可变字节编码方案，可以根据实际情况使用2个、4个字节来存储字符

UTF-32编码：固定字节编码方案，用4个字节来存储字符

#### **UTF-8**

UTF-8是Unicode的实现方式之一，它的最大特点是：它是一种变长的编码方式，可以使用1~4个字节表示一个字符，根据不同的符号而变化字节的长度。

![img](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123273.png)

**Unicode是世界上所有符号对应二进制数据的关系集合**

**UTF-8是Unicode的实现方式之一：UTF-8 = Unicode符号集 + 变长的编码规则**

#### 序列化

```c++
        #region 非字符串类型转字节数组
        //关键类：BitConverter
        //所在命名空间：System
        //主要作用：除字符串的其它常用类型和字节数组相互转换

        byte[] bytes = BitConverter.GetBytes(1);
        #endregion

        #region 字符串类型转字节数组
        //关键类：Encoding
        //所在命名空间：System.Text
        //主要作用：将字符串类型和字节数组相互转换，并且决定转换时使用的字符编码类型，网络通信时建议大家使用UTF-8类型
        byte[] byte2 = Encoding.UTF8.GetBytes("的卡萨福利卡决胜巅峰卡视角的副驾驶的");
        #endregion

        #region 如何将一个类对象转换为二进制
        //注意：网络通信中我们不能直接使用数据持久化2进制知识点中的
        //BinaryFormatter 2进制格式化类
        //因为客户端和服务器使用的语言可能不一样，BinaryFormatter是C#的序列化规则，和其它语言之间的兼容性不好
        //如果使用它，那么其它语言开发的服务器无法对其进行反序列化
        //我们需要自己来处理将类对象数据序列化为字节数组

        //单纯的转换一个变量为字节数组非常的简单
        //但是我们如何将一个类对象携带的所有信息放入到一个字节数组中呢
        //我们需要做以下几步
        //1.明确字节数组的容量（注意：在确定字符串字节长度时要考虑解析时如何处理）
        PlayerInfo info = new PlayerInfo();
        info.lev = 10;
        info.name = "刘英博";
        info.atk = 88;
        info.sex = false;
        //得到的 这个Info数据 如果转换成 字节数组 那么字节数组容器需要的容量
        int indexNum = sizeof(int) + //lev int类型  4
                       sizeof(int) + //代表 name字符串转换成字节数组后 数组的长度 4
                       Encoding.UTF8.GetBytes(info.name).Length + //字符串具体字节数组的长度
                       sizeof(short) + //atk short类型 2
                       sizeof(bool); //sex bool类型 1

        //2.申明一个装载信息的字节数组容器
        byte[] playerBytes = new byte[indexNum];

        //3.将对象中的所有信息转为字节数组并放入该容器当中（可以利用数组中的CopeTo方法转存字节数组）
        //CopyTo方法的第二个参数代表 从容器的第几个位置开始存储
        int index = 0;//从 playerBytes数组中的第几个位置去存储数据

        //等级
        BitConverter.GetBytes(info.lev).CopyTo(playerBytes, index);
        index += sizeof(int);

        //姓名
        byte[] strBytes = Encoding.UTF8.GetBytes(info.name);
        int num = strBytes.Length;
        //存储的是姓名转换成字节数组后 字节数组的长度
        BitConverter.GetBytes(num).CopyTo(playerBytes, index);
        index += sizeof(int);
        //存储字符串的字节数组
        strBytes.CopyTo(playerBytes, index);
        index += num;

        //攻击力
        BitConverter.GetBytes(info.atk).CopyTo(playerBytes, index);
        index += sizeof(short);
        //性别
        BitConverter.GetBytes(info.sex).CopyTo(playerBytes, index);
        index += sizeof(bool);
        #endregion
```

BitConverter转换非字符串的类型的变量为字节数组

Encoding.UTF8转换字符串类型的变量为字节数组（注意：为了考虑反序列化，我们在转存2进制，序列化字符串之前，先序列化字符串字节数组的长度）

#### 反序列化

```c++
        #region 字节数组转非字符串类型
        //关键类：BitConverter
        //所在命名空间：System
        //主要作用：除字符串的其它常用类型和字节数组相互转换
        byte[] bytes = BitConverter.GetBytes(99);
        int i = BitConverter.ToInt32(bytes, 0);
        print(i);
        #endregion

        #region 字节数组转字符串类型
        //关键类：Encoding
        //所在命名空间：System.Text
        //主要作用：将字符串类型和字节数组相互转换，并且决定转换时使用的字符编码类型，网络通信时建议大家使用UTF-8类型
        byte[] bytes2 = Encoding.UTF8.GetBytes("123123空间大撒了房间阿斯利康放大镜");
        string str = Encoding.UTF8.GetString(bytes2, 0, bytes2.Length);
        print(str);
        #endregion

        #region 如何将二进制数据转为一个类对象
        //1.获取到对应的字节数组
        PlayerInfo info = new PlayerInfo();
        info.lev = 10;
        info.name = "刘英博";
        info.atk = 88;
        info.sex = false;

        byte[] playerBytes = info.GetBytes();

        //2.将字节数组按照序列化时的顺序进行反序列化(将对应字节分组转换为对应类型变量)
        PlayerInfo info2 = new PlayerInfo();
        //等级
        int index = 0;
        info2.lev = BitConverter.ToInt32(playerBytes, index);
        index += 4;
        print(info2.lev);
        //姓名的长度
        int length = BitConverter.ToInt32(playerBytes, index);
        index += 4;
        //姓名字符串
        info2.name = Encoding.UTF8.GetString(playerBytes, index, length);
        index += length;
        print(info2.name);
        //攻击力
        info2.atk = BitConverter.ToInt16(playerBytes, index);
        index += 2;
        print(info2.atk);
        //性别
        info2.sex = BitConverter.ToBoolean(playerBytes, index);
        index += 1;
        print(info2.sex);
        #endregion
```

BitConverter转换字节数组为非字符串的类型的变量

Encoding.UTF8转换字节数组为字符串类型的变量（注意：先读长度，再读字符串）



## 套接字Socket

### Socket概述

```c++
#region Socket套接字的作用
//它是C#提供给我们用于网络通信的一个类（在其它语言当中也有对应的Socket类）
//类名：Socket
//命名空间：System.Net.Sockets

//Socket套接字是支持TCP/IP网络通信的基本操作单位
//一个套接字对象包含以下关键信息
//1.本机的IP地址和端口
//2.对方主机的IP地址和端口
//3.双方通信的协议信息

//一个Sccket对象表示一个本地或者远程套接字信息
//它可以被视为一个数据通道
//这个通道连接与客户端和服务端之间
//数据的发送和接受均通过这个通道进行

//一般在制作长连接游戏时，我们会使用Socket套接字作为我们的通信方案
//我们通过它连接客户端和服务端，通过它来收发消息
//你可以把它抽象的想象成一根管子，插在客户端和服务端应用程序上，通过这个管子来传递交换信息
#endregion

#region Socket的类型
//Socket套接字有3种不同的类型
//1.流套接字
//  主要用于实现TCP通信，提供了面向连接、可靠的、有序的、数据无差错且无重复的数据传输服务
//2.数据报套接字
//  主要用于实现UDP通信，提供了无连接的通信服务，数据包的长度不能大于32KB，不提供正确性检查，不保证顺序，可能出现重发、丢失等情况
//3.原始套接字（不常用，不深入讲解）
//  主要用于实现IP数据包通信，用于直接访问协议的较低层，常用于侦听和分析数据包

//通过Socket的构造函数 我们可以申明不同类型的套接字
//Socket s = new Socket()
//参数一：AddressFamily 网络寻址 枚举类型，决定寻址方案
//  常用：
//  1.InterNetwork  IPv4寻址
//  2.InterNetwork6 IPv6寻址
//  做了解：
//  1.UNIX          UNIX本地到主机地址 
//  2.ImpLink       ARPANETIMP地址
//  3.Ipx           IPX或SPX地址
//  4.Iso           ISO协议的地址
//  5.Osi           OSI协议的地址
//  7.NetBios       NetBios地址
//  9.Atm           本机ATM服务地址

//参数二：SocketType 套接字枚举类型，决定使用的套接字类型
//  常用：
//  1.Dgram         支持数据报，最大长度固定的无连接、不可靠的消息(主要用于UDP通信)
//  2.Stream        支持可靠、双向、基于连接的字节流（主要用于TCP通信）
//  做了解：
//  1.Raw           支持对基础传输协议的访问
//  2.Rdm           支持无连接、面向消息、以可靠方式发送的消息
//  3.Seqpacket     提供排序字节流的面向连接且可靠的双向传输

//参数三：ProtocolType 协议类型枚举类型，决定套接字使用的通信协议
//  常用：
//  1.TCP           TCP传输控制协议
//  2.UDP           UDP用户数据报协议
//  做了解：
//  1.IP            IP网际协议
//  2.Icmp          Icmp网际消息控制协议
//  3.Igmp          Igmp网际组管理协议
//  4.Ggp           网关到网关协议
//  5.IPv4          Internet协议版本4
//  6.Pup           PARC通用数据包协议
//  7.Idp           Internet数据报协议
//  8.Raw           原始IP数据包协议
//  9.Ipx           Internet数据包交换协议
//  10.Spx          顺序包交换协议
//  11.IcmpV6       用于IPv6的Internet控制消息协议

//2、3参数的常用搭配：
//       SocketType.Dgram  +  ProtocolType.Udp  = UDP协议通信（常用，主要学习）
//       SocketType.Stream  +  ProtocolType.Tcp  = TCP协议通信（常用，主要学习）
//       SocketType.Raw  +  ProtocolType.Icmp  = Internet控制报文协议（了解）
//       SocketType.Raw  +  ProtocolType.Raw  = 简单的IP包通信（了解）

//我们必须掌握的
//TCP流套接字
Socket socketTcp = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

//UDP数据报套接字
Socket socketUdp = new Socket(AddressFamily.InterNetwork, SocketType.Dgram, ProtocolType.Udp);
#endregion

#region Socket的常用属性
//1.套接字的连接状态
if(socketTcp.Connected)
{

}
//2.获取套接字的类型
print(socketTcp.SocketType);
//3.获取套接字的协议类型
print(socketTcp.ProtocolType);
//4.获取套接字的寻址方案
print(socketTcp.AddressFamily);

//5.从网络中获取准备读取的数据数据量
print(socketTcp.Available);

//6.获取本机EndPoint对象(注意 ：IPEndPoint继承EndPoint)
//socketTcp.LocalEndPoint as IPEndPoint

//7.获取远程EndPoint对象
//socketTcp.RemoteEndPoint as IPEndPoint
#endregion

#region Socket的常用方法
//1.主要用于服务端
//  1-1:绑定IP和端口
IPEndPoint ipPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);
socketTcp.Bind(ipPoint);
//  1-2:设置客户端连接的最大数量
socketTcp.Listen(10);
//  1-3:等待客户端连入
socketTcp.Accept();

//2.主要用于客户端
//  1-1:连接远程服务端
socketTcp.Connect(IPAddress.Parse("118.12.123.11"), 8080);

//3.客户端服务端都会用的
//  1-1:同步发送和接收数据
//  1-2:异步发送和接收数据
//  1-3:释放连接并关闭Socket，先与Close调用
socketTcp.Shutdown(SocketShutdown.Both);
//  1-4:关闭连接，释放所有Socket关联资源
socketTcp.Close();
#endregion
```



### TCP通信

#### 服务端和客户端需要做什么

![image-20220521151533548](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123242.png)

#### TCP协议三次握手的体现

![image-20220521151638515](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123581.png)

#### TCP协议四次挥手的体现

![image-20220521151714969](/Users/liuyingbo/Library/Application Support/typora-user-images/image-20220521151714969.png)

#### TCP基本API

```c++
#region 实现服务端基本逻辑
//1.创建套接字Socket（TCP）
Socket socketTcp = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
//2.用Bind方法将套接字与本地地址绑定
try
{
    IPEndPoint ipPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);
    socketTcp.Bind(ipPoint);
}
catch (Exception e)
{
    Console.WriteLine("绑定报错" + e.Message);
    return;
}
//3.用Listen方法监听
socketTcp.Listen(1024);
Console.WriteLine("服务端绑定监听结束，等待客户端连入");
//4.用Accept方法等待客户端连接
//5.建立连接，Accept返回新套接字
Socket socketClient = socketTcp.Accept();
Console.WriteLine("有客户端连入了");
//6.用Send和Receive相关方法收发数据
//发送
socketClient.Send(Encoding.UTF8.GetBytes("欢迎连入服务端"));
//接受
byte[] result = new byte[1024];
//返回值为接受到的字节数
int receiveNum = socketClient.Receive(result);
Console.WriteLine("接受到了{0}发来的消息：{1}",
    socketClient.RemoteEndPoint.ToString(),
    Encoding.UTF8.GetString(result, 0, receiveNum));

//7.用Shutdown方法释放连接
socketClient.Shutdown(SocketShutdown.Both);
//8.关闭套接字
socketClient.Close();
#endregion

#region 实现客户端基本逻辑
//1.创建套接字Socket
Socket socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
//2.用Connect方法与服务端相连
//确定服务端的IP和端口
IPEndPoint ipPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);
try
{
    socket.Connect(ipPoint);
}
catch (SocketException e)
{
    if (e.ErrorCode == 10061)
        print("服务器拒绝连接");
    else
        print("连接服务器失败" + e.ErrorCode);
    return;
}
//3.用Send和Receive相关方法收发数据

//接收数据
byte[] receiveBytes = new byte[1024];
int receiveNum = socket.Receive(receiveBytes);
print("收到服务端发来的消息：" + Encoding.UTF8.GetString(receiveBytes, 0, receiveNum));

//发送数据
socket.Send(Encoding.UTF8.GetBytes("你好，我是客户端"));

//4.用Shutdown方法释放连接
socket.Shutdown(SocketShutdown.Both);
//5.关闭套接字
socket.Close();
#endregion
```

#### 如何区分消息

为发送的信息添加标识，比如添加消息ID

在所有发送的消息的头部加上消息ID(int、short、byte、long都可以，根据实际情况选择)

举例说明：

如果选用int类型作为消息ID的类型

前4个字节为消息ID

后面的字节为数据类的内容

\####***************************

这样每次收到消息时，先把前4个字节取出来解析为消息ID

再根据ID进行消息反序列化即可

#### 分包、黏包

##### 什么是分包、黏包？

分包、黏包指在网络通信中由于各种因素（网络环境、API规则等）造成的消息与消息之间出现的两种状态

分包：一个消息分成了多个消息进行发送

黏包：一个消息和另一个消息黏在了一起

注意：分包和黏包可能同时发生

##### 如何解决？

为消息添加头部，头部记录消息的长度

当我们接收到消息时，通过消息长度来判断是否分包、黏包

对消息进行拆分处理、合并处理

![分包黏包解决方案图](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123236.png)

#### 心跳消息

客户端主动断开连接会调用socket的 ShutDown和Close方法，但是通过调用这两个方法后 服务器端无法得知客户端已经主动断开

客户端尝试使用Disconnect方法主动断开连接，Socket当中有一个专门在客户端使用的方法，客户端调用该方法和服务器端断开连接

服务器端可以通过Conected属性判断连接状态决定是否释放Socket

但是由于服务器端Conected变量表示的是上一次收发消息是否成功，所以服务器端无法准确判断客户端的连接状态，因此 我们需要自定义一条退出消息 用于准确断开和客户端之间的连接

##### 什么是心跳消息？

所谓心跳消息，就是在长连接中，客户端和服务端之间定期发送的一种特殊的数据包，用于通知对方自己还在线，以确保长连接的有效性

由于其发送的时间间隔往往是固定的持续的，就像是心跳一样一直存在，所以我们称之为心跳消息

##### 为什么需要心跳消息？

1.避免非正常关闭客户端时，服务器无法正常收到关闭连接消息，通过心跳消息我们可以自定义超时判断，如果超时没有收到客户端消息，证明客户端已经断开连接

2.避免客户端长期不发送消息，防火墙或者路由器会断开连接，我们可以通过心跳消息一直保持活跃状态

##### 实现心跳消息

客户端：定时发送消息

服务器：不停检测上次收到某客户端消息的时间，如果超时则认为连接已经断开

心跳消息是长连接项目中必备的一套逻辑规则，通过它可以帮助我们在服务器端及时的释放掉失效的socket，可以有效避免当客户端非正常关闭时，服务器端不能及时判断连接已断开

### UDP通信

#### 服务端和客户端需要做什么

![image-20220521160007101](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123022.png)

#### UDP相对TCP的区别

![image-20220521160026008](/Users/liuyingbo/Library/Application Support/typora-user-images/image-20220521160026008.png)

#### UDP的分包、黏包问题

##### 分包

UDP本身作为无连接的不可靠的传输协议（适合频繁发送较小的数据包），不会对数据包进行合并发送，一端发送什么数据，直接就发出去了，他不会对数据合并，因此在UDP当中不会出现黏包问题（除非你手动进行黏包）

##### 分包问题

由于UDP是不可靠的连接，消息传递过程中可能出现无序、丢包等情况，所以如果允许UDP进行分包，那后果将会是灾难性的，比如分包的后半段丢包或者比上半段先发来，我们在处理消息时将会非常困难，因此为了避免其分包，我们建议在发送UDP消息时控制消息的大小在MTU（最大传输单元）范围内

MTU（Maximum Transmission Unit）最大传输单元，用来通知对方所能接受数据服务单元的最大尺寸

不同操作系统会提供用户一个默认值

以太网和802.3对数据帧的长度限制，其最大值分别是1500字节和1492字节

由于UDP包本身带有一些信息，因此建议：

1.局域网环境下：1472字节以内（1500减去UDP头部28为1472）

2.互联网环境下：548字节以内（老的ISP拨号网络的标准值为576减去UDP头部28为548）

只要遵守这个规则，就不会出现自动分包的情况

如果想要发送的消息确实比较大，要大于548字节或1472字节这个限制呢？比如我们要发一个5000字节的数据，他是一条完整消息，我们可以进行手动分包，将5000拆分成多个消息，每个消息不超过限制，但是手动分包的前提是要解决UDP的丢包和无序问题，我们可以将不可靠的UDP通信实现为可靠的UDP通信

比如：在消息中加入序号、消息总包数、自己的包ID、长度等等信息，并且实现消息确认、消息重发等功能

#### UDP基本API

```c++
#region 实现UDP服务端通信 收发字符串
//1.创建套接字
Socket socket = new Socket(AddressFamily.InterNetwork, SocketType.Dgram, ProtocolType.Udp);
//2.绑定本机地址
IPEndPoint ipPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8081);
socket.Bind(ipPoint);
Console.WriteLine("服务器开启");
//3.接受消息
byte[] bytes = new byte[512];
//这个变量主要是用来记录 谁发的信息给你 传入函数后 在内部 它会帮助我们进行赋值
EndPoint remoteIpPoint2 = new IPEndPoint(IPAddress.Any, 0);
int length = socket.ReceiveFrom(bytes, ref remoteIpPoint2);
Console.WriteLine("IP:" + (remoteIpPoint2 as IPEndPoint).Address.ToString() +
    "port:" + (remoteIpPoint2 as IPEndPoint).Port +
    "发来了" +
    Encoding.UTF8.GetString(bytes, 0, length));

//4.发送到指定目标
//由于我们先收 所以 我们已经知道谁发了消息给我 我直接发给它就行了
socket.SendTo(Encoding.UTF8.GetBytes("欢迎发送消息给服务器"), remoteIpPoint2);

//5.释放关闭
socket.Shutdown(SocketShutdown.Both);
socket.Close();
#endregion


#region 实现UDP客户端通信 收发字符串
//1.创建套接字
Socket socket = new Socket(AddressFamily.InterNetwork, SocketType.Dgram, ProtocolType.Udp);

//2.绑定本机地址
IPEndPoint ipPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8080);
socket.Bind(ipPoint);

//3.发送到指定目标
IPEndPoint remoteIpPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 8081);
//指定要发送的字节数 和 远程计算机的 IP和端口
socket.SendTo(Encoding.UTF8.GetBytes("我来了"), remoteIpPoint);

//4.接受消息
byte[] bytes = new byte[512];
//这个变量主要是用来记录 谁发的信息给你 传入函数后 在内部 它会帮助我们进行赋值
EndPoint remoteIpPoint2 = new IPEndPoint(IPAddress.Any, 0);
int length = socket.ReceiveFrom(bytes, ref remoteIpPoint2);
print("IP:" + (remoteIpPoint2 as IPEndPoint).Address.ToString() +
    "port:" + (remoteIpPoint2 as IPEndPoint).Port +
    "发来了" +
    Encoding.UTF8.GetString(bytes, 0, length));

//5.释放关闭
socket.Shutdown(SocketShutdown.Both);
socket.Close();
#endregion
```



## 文件传输FTP

### FTP工作原理

#### FTP是什么

FTP（File Transfer Protocol）

文件传输协议，是支持Internet文件传输的各种规则所组成的集合，这些规则使Internet用户可以把文件从一台主机拷贝到另一台主机上，

除此之外，FTP还提供登录、目录查询以及其他会话控制等功能

说人话：FTP文件传输协议就是一个在网络中上传下载文件的一套规则

#### FTP的工作原理

划重点：FTP的本质是TCP通信

通过FTP传输文件，双发至少需要建立两个TCP连接

一个称为控制连接，用于传输FTP命令

一个称为数据连接，用于传输文件数据

![image-20220521174749538](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242123585.png)

FTP的数据连接和控制连接方向一般是相反的

举例说明：

用户使用FTP客户端连接FTP服务区请求下载文件

控制连接方向：客户端主动连接服务器告知其下载命令

数据连接方向：服务端主动连接客户端下发数据



当客户端和FTP服务器建立控制连接后

需要告诉服务器采用那种传输模式

1.主动模式(Port模式)：服务器主动连接客户端，然后传输文件

2.被动模式(Passive模式)：客户端主动连接服务器，即控制连接和数据连接都由客户端发起

一般情况下主动模式会受到客户端防火墙影响，所以被动模式使用较多



在使用FTP进行数据传输时，有两种数据传输方式

1.ASCII传输方式

以ASCII编码方式传输数据，适用于传输，仅包含英文的命令和参数或者英文文本文件

2.二进制传输方式（建议使用该方式）

可以指定采用哪种编码传输命令和文件数据，如果传输的文件不是英文文件则应该采用该方式



一般情况下，使用FTP传输文件时，客户端必须先登录服务器，获得相应权限后才能上传或下载文件

服务器也可以允许用户匿名登录FTP，不需要都拥有一个合法账号



在实际学习过程中，我们并不需要利用FTP原理来实现FTP通信，FTP工作原理相关知识点，主要做了解

C#中实现了FTP通信需要用到的相关类

FtpWebRequest、FtpWebResponse、NetworkCredential

###  搭建FTP服务器

在实际商业项目开发当中，如果需要用FTP来进行文件传输，那么FTP服务器的解决方案都是由后端程序员来完成的，不管它使用哪种方式来搭建FTP服务器，只要能正常上传下载内容并且保证安全性即可

可以使用Serv-U，具体方式自行百度

### FTP相关API

```c++
#region NetworkCredential类
//命名空间：System.Net
//NetworkCredential通信凭证类
//用于在Ftp文件传输时，设置账号密码
NetworkCredential n = new NetworkCredential("liuyingbo", "liuyingbo123");
#endregion

#region FtpWebRequest类
//命名空间：System.Net
//Ftp文件传输协议客户端操作类
//主要用于：上传、下载、删除服务器上的文件

//重要方法
//1.Create 创建新的WebRequest，用于进行Ftp相关操作
FtpWebRequest req = FtpWebRequest.Create(new Uri("ftp://127.0.0.1/Test.txt")) as FtpWebRequest;
//2.Abort  如果正在进行文件传输，用此方法可以终止传输
req.Abort();
//3.GetRequestStream  获取用于上传的流
Stream s = req.GetRequestStream();
//4.GetResponse  返回FTP服务器响应
//FtpWebResponse res = req.GetResponse() as FtpWebResponse;

//重要成员
//1.Credentials 通信凭证，设置为NetworkCredential对象
req.Credentials = n;
//2.KeepAlive bool值，当完成请求时是否关闭到FTP服务器的控制连接（默认为true，不关闭）
req.KeepAlive = false;
//3.Method  操作命令设置
//  WebRequestMethods.Ftp类中的操作命令属性
//  DeleteFile  删除文件
//  DownloadFile    下载文件    
//  ListDirectory   获取文件简短列表
//  ListDirectoryDetails    获取文件详细列表
//  MakeDirectory   创建目录
//  RemoveDirectory 删除目录
//  UploadFile  上传文件
req.Method = WebRequestMethods.Ftp.DownloadFile;
//4.UseBinary 是否使用2进制传输
req.UseBinary = true;
//5.RenameTo    重命名
//req.RenameTo = "myTest.txt";
#endregion

#region FtpWebResponse类
//命名空间：System.Net
//它是用于封装FTP服务器对请求的响应
//它提供操作状态以及从服务器下载数据
//我们可以通过FtpWebRequest对象中的GetResponse()方法获取
//当使用完毕时，要使用Close释放

//通过它来真正的从服务器获取内容
FtpWebResponse res = req.GetResponse() as FtpWebResponse;

//重要方法：
//1.Close:释放所有资源
res.Close();
//2.GetResponseStream：返回从FTP服务器下载数据的流
Stream stream = res.GetResponseStream();

//重要成员：
//1.ContentLength:接受到数据的长度
print(res.ContentLength);
//2.ContentType：接受数据的类型
print(res.ContentType);
//3.StatusCode:FTP服务器下发的最新状态码
print(res.StatusCode);
//4.StatusDescription:FTP服务器下发的状态代码的文本
print(res.StatusDescription);
//5.BannerMessage:登录前建立连接时FTP服务器发送的消息
print(res.BannerMessage);
//6.ExitMessage:FTP会话结束时服务器发送的消息
//7.LastModified:FTP服务器上的文件的上次修改日期和时间
#endregion

#region 总结
//通过C#提供的这3个类
//我们便可以完成客户端向FTP服务器
//操作文件的需求，比如
//上传、下载、删除文件
#endregion
```



## 超文本传输协议HTTP

### HTTP工作原理

#### HTTP是什么

HTTP（HyperText Transfer Protocol）

超文本传输协议，是因特网上应用最为广泛的一种网络传输协议。最初设计HTTP的，目的是为了提供一种发布和接收由文本文件组成的HTML页面的方法，后来发展到除了文本数据外，还可以传输图片、音频、视频、压缩文件以及各种程序文件等。

HTTP主要用于超文本传输，因此相对FTP显得更简单一些，目前常见的HTTP标准是HTTP/1.1.

说人话：HTTP超文本传输协议就是一个在网络中上传下载文件的一套规则

#### HTTP的工作原理

划重点：HTTP的本质也是TCP通信

HTTP定义了Web客户端（一般指浏览器）如何从Web服务器请求Web页面，以及服务器如何把Web页面传送给客户端。

HTTP客户端首先与服务器建立TCP连接，然后客户端通过套接字发送HTTP请求，并通过套接字接收HTTP响应，由于HTTP采用TCP传输数据，因此不会丢包、不会乱序。

HTTP的工作原理主要有以下三个特点

##### HTTP是以TCP方式工作

在HTTP/1.0中，客户端和服务器建立TCP连接后，发送一个请求到服务器，服务器发送一个应答给客户端，然后立即断开TCP连接，他们的主要步骤为：

1.客户端与服务端建立TCP连接

2.客户端向服务端发出请求

3.若服务端接受请求，则回送响应码和所需的信息

4.客户端与服务端断开TCP连接

需要注意，HTTP/1.1 支持持久连接，即客户端和服务端建立连接后，可以发送请求和接收应答，然后迅速地发送另一个请求和接收另一个应答。

持久连接也使得在得到上一个请求的应答之前能够发送多个请求，这就是HTTP/1.1与HTTP/1.0的明显不同之处，除此之外，HTTP/1.1可以发送的请求类型也比HTTP/1.0多。

目前市面上的Web服务器软件和浏览器软件基本都是支持HTTP/1.1版本的，目前使用的基本上都是HTTP/1.1版本



##### HTTP是无状态的

无状态指：客户端发送一次请求后，服务端并没有存储关于该客户端的任何状态信息，即使客户端再次请求同一个对象，服务端仍会重新发送这个对象，不会在意之前是否已经向客户端发送过这个对象

说人话：HTTP通信就是客户端要什么来什么，想要多少来多少，服务端不会因为你要过了而不给你，不会记录你要过的状态



##### HTTP使用元信息作为标头

HTTP通过添加标头（header）的方式向服务端提供本次HTTP请求的相关信息，即在主要数据前添加一部分额外信息，称为元信息（metainformation）元信息里主要包含：传送的对象属于哪种类型，采用的是哪种编码等等

说人话：HTTP的元信息标头，类似我们讲解Socket通信时用于区分消息类型、处理分包黏包时，在消息体前方加的自定义信息。在HTTP协议中，它也定义了类似的规则，在头部包含了一些额外信息

#### HTTP协议的请求类型和响应状态码

请求类型：

HTTP/1.0中：

GET、POST、HEAD

HTTP/1.1中：

GET、POST、HEAD、PUT、DELETE、OPTIONS、TRACE、CONNECT

响应状态码：

1xx、2xx、3xx、4xx、5xx

| 请求方法名说明 | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| GET            | 请求获取特定的资源，比如请求一个Web页面或请求获取一个资源    |
| POST           | 请求提交数据进行处理，比如请求上传一个文件                   |
| HEAD           | 请求获取和GET一致的内容，但是不会返回具体内容，只会返回消息头 |
| PUT            | 向指定位置上传最新内容                                       |
| DELETE         | 删除指定资源                                                 |
| OPTIONS        | 返回服务器针对特定资源支持的HTTP请求方法                     |
| TRACE          | 回显服务端收到的请求                                         |
| CONNECT        | 预留给能够将连接改为管道方式的代理服务器                     |



每一种请求方法，其实就是在HTTP请求的头部信息包含的内容不同而已内容发送的格式为：

![image-20221124213110318](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242131363.png)

![image-20221124213118941](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242131974.png)

![image-20221124213123578](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242131611.png)

客户端向服务端发送请求后，服务端会返回HTTP响应

HTTP响应的一般格式为：

![image-20221124213152161](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242131203.png)

状态行中主要内容有：

\1. HTTP版本号

\2. 3位数字组成的状态码

1xx消息：请求已被服务端接收，继续处理

2xx成功：请求已成功被服务端理解并接收

3xx重定向：需要后续操作才能完成这一请求

4xx请求错误：请求含有语法错误或者无法被执行

5xx服务器错误：服务端在处理某个正确请求时发生错误

| 编号 | 状态码             | 说明                                           |
| ---- | ------------------ | ---------------------------------------------- |
| 200  | OK                 | 找到资源，一切正常                             |
| 304  | NOT MODIFIED       | 资源在上次请求后没有任何修改（常用语缓存机制） |
| 401  | UNAUTHORIZED       | 客户端无权访问该资源，通常需要输入用户名和密码 |
| 403  | FORBIDDEN          | 客户端未授权，通常是401后输入了错误用户名密码  |
| 404  | NOT FOUND          | 指定位置不存在申请的资源                       |
| 405  | Method Not Allowed | 不支持请求的方法                               |
| 501  | Not Implemented    | 服务器不能识别请求或者没有实现指定的请求       |



### 搭建HTTP服务器

www.baidu.com



### C#中HTTP类

```c++
#region HttpWebRequest类
//命名空间：System.Net
//HttpWebRequest是主要用于发送客户端请求的类
//主要用于：发送HTTP客户端请求给服务器，可以进行消息通信、上传、下载等等操作

//重要方法
//1.Create 创建新的WebRequest，用于进行HTTP相关操作
HttpWebRequest req = HttpWebRequest.Create(new Uri("http://192.168.50.109:8000/Http_Server/")) as HttpWebRequest;
//2.Abort  如果正在进行文件传输，用此方法可以终止传输 
req.Abort();
//3.GetRequestStream  获取用于上传的流
Stream s = req.GetRequestStream();
//4.GetResponse  返回HTTP服务器响应
HttpWebResponse res = req.GetResponse() as HttpWebResponse;
//5.Begin/EndGetRequestStream 异步获取用于上传的流
//req.BeginGetRequestStream()
//6.Begin/EndGetResponse 异步获取返回的HTTP服务器响应
//req.BeginGetResponse()

//重要成员
//1.Credentials 通信凭证，设置为NetworkCredential对象
req.Credentials = new NetworkCredential("", "");
//2.PreAuthenticate 是否随请求发送一个身份验证标头,一般需要进行身份验证时需要将其设置为true
req.PreAuthenticate = true;

//3.Headers 构成标头的名称/值对的集合
//req.Headers
//4.ContentLength 发送信息的字节数 上传信息时需要先设置该内容长度
req.ContentLength = 100;
//5.ContentType 在进行POST请求时，需要对发送的内容进行内容类型的设置
//6.Method  操作命令设置
//  WebRequestMethods.Http类中的操作命令属性
//  Get     获取请求，一般用于获取数据
//  Post    提交请求，一般用于上传数据，同时可以获取
//  Head    获取和Get一致的内容，只是只会返回消息头，不会返回具体内容
//  Put     向指定位置上传最新内容
//  Connect 表示与代理一起使用的 HTTP CONNECT 协议方法，该代理可以动态切换到隧道
//  MkCol   请求在请求 URI（统一资源标识符）指定的位置新建集合

//了解该类的更多信息
//https://docs.microsoft.com/zh-cn/dotnet/api/system.net.httpwebrequest?view=net-6.0
#endregion

#region HttpWebResponse类
//命名空间：System.Net
//它主要用于获取服务器反馈信息的类
//我们可以通过HttpWebRequest对象中的GetResponse()方法获取
//当使用完毕时，要使用Close释放

//重要方法：
//1.Close:释放所有资源
//2.GetResponseStream：返回从FTP服务器下载数据的流

//重要成员：
//1.ContentLength:接受到数据的长度
//2.ContentType：接受数据的类型
//3.StatusCode:HTTP服务器下发的最新状态码
//4.StatusDescription:HTTP服务器下发的状态代码的文本
//5.BannerMessage:登录前建立连接时HTTP服务器发送的消息
//6.ExitMessage:HTTP会话结束时服务器发送的消息
//7.LastModified:HTTP服务器上的文件的上次修改日期和时间

//了解该类的更多信息
//https://docs.microsoft.com/zh-cn/dotnet/api/system.net.httpwebresponse?view=net-6.0
#endregion

#region NetworkCredential、Uri、Stream、FileStream类
//这些类我们在学习Ftp时已经使用过了
//在HTTP通讯时使用方式不变
#endregion

#region 总结
//Http相关通讯类的使用和Ftp非常类似
//只有一些细节上的区别
#endregion
```

#### Get和Post的区别

Get — 一般从指定的资源请求数据,主要用于获取数据

Post — 一般向指定的资源提交想要被处理的数据，主要用于上传数据

相同点:

Get和Post都可以传递一些额外的参数数据给服务端

不同点:

1.在传递参数时，Post相对Get更加的安全，因为Post看不到参数
    Get传递的参数都包含在连接中（URL资源定位地址），是暴露式的 ?参数名=参数值&参数名=参数值
    Post传递的参数放在请求数据中，不会出现在URL中，是隐藏式的

2.Get在传递数据时有大小的限制，因为它主要是在连接中拼接参数，而URL的长度是有限制的（最大长度一般为2048个字符）
    Post在传递数据时没有限制

3.在浏览器中Get请求能被缓存，Post不能缓存

4.传输次数可能不同
    Get:  建立连接——>请求行、请求头、请求数据一次传输——>获取响应——>断开连接
    Post: 建立连接——>传输可能分两次——>请求行，请求头第一次传输——>请求数据第二次传输——>获取响应——>断开



但是由于他们的这些特点
我们在实际使用时建议Get用于获取，Post用于上传
如果想要传递一些不想暴露在外部的参数信息，建议使用Post，它更加的安全

#### Post如何携带额外参数

```c++
#region Post如何携带额外参数
//关键点：将Content-Type设置为 application/x-www-form-urlencoded 键值对类型
HttpWebRequest req = HttpWebRequest.Create("http://192.168.50.109:8000/Http_Server/") as HttpWebRequest;
req.Method = WebRequestMethods.Http.Post;
req.Timeout = 2000;
//设置上传的内容的类型
req.ContentType = "application/x-www-form-urlencoded";

//我们要上传的数据
string str = "Name=liuyingbo&ID=2";
byte[] bytes = Encoding.UTF8.GetBytes(str);
//我们在上传之前一定要设置内容的长度
req.ContentLength = bytes.Length;
//上传数据
Stream stream = req.GetRequestStream();
stream.Write(bytes, 0, bytes.Length);
stream.Close();
//发送数据 得到响应结果
HttpWebResponse res = req.GetResponse() as HttpWebResponse;
print(res.StatusCode);

#endregion
```

#### ContentType类型

```c++
#region ContentType的常用类型
//ContentType的构成：
//内容类型;charset=编码格式;boundary=边界字符串
//text/html;charset=utf-8;boundary=自定义字符串

//其中内容类型有：
//文本类型text：
//text/plain 没有特定子类型就是它（重要）
//text/html
//text/css
//text/javascript

//图片类型image：
//image/gif
//image/png
//image/jpeg
//image/bm
//image/webp
//image/x-icon
//image/vnd.microsoft.icon

//音频类型audio：
//audio/midi
//audio/mpeg
//audio/webm
//audio/ogg
//audio/wav

//视频类型video:
//video/webm
//video/ogg

//二进制类型application:
//application/octet-stream 没有特定子类型就是它（重要）
//application/x-www-form-urlencoded 传递参数时使用键值对形式（重要）
//application/pkcs12
//application/xhtml+xml
//application/xml
//application/pdf
//application/vnd.mspowerpoint

//复合内容multipart:
//multipart/form-data  复合内容，有多种内容组合（重要）
//multipart/byteranges  特殊的复合文件


//关于ContentType更多内容可以前往
//https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type
//关于媒体类型可以前往
//https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types
#endregion

#region ContentType中对于我们来说重要的类型
//1.通用2进制类型
//application/octet-stream
//2.通用文本类型
//text/plain 
//3.键值对参数
//application/x-www-form-urlencoded
//4.复合类型（传递的信息有多种类型组成,比如有键值对参数,有文件信息等等,上传资源服务器时需要用该类型）
//multipart/form-data
#endregion
```



### Unity中的HTTP类

#### WWW类

```c++
#region WWW类的作用
//WWW是Unity提供给我们简单的访问网页的类
//我们可以通过该类下载和上传一些数据
//在使用http协议时，默认的请求类型是Get，如果想要Post上传，需要配合下节课学习的WWWFrom类使用
//它主要支持的协议
//1.http://和https:// 超文本传输协议
//2.ftp:// 文件传输协议（但仅限于匿名下载）
//3.file:// 本地文件传输协议，可以使用该协议异步加载本地文件（PC、IOS、Android都支持）
//我们本节课主要学习利用WWW来进行数据的下载或加载

//注意：
//1.该类一般配合协同程序使用
//2.该类在较新Unity版本中会提示过时，但是仍可以使用，新版本将其功能整合进了UnityWebRequest类（之后讲解）
#endregion

#region WWW类的常用方法和变量
#region 常用方法
//1.WWW：构造函数，用于创建一个WWW请求
WWW www = new WWW("http://192.168.50.109:8000/Http_Server/test.jpg");
//2.GetAudioClip：从下载数据返回一个音效切片AudioClip对象
//www.GetAudioClip()
//3.LoadImageIntoTexture：用下载数据中的图像来替换现有的一个Texture2D对象
//Texture2D tex = new Texture2D(100, 100);
//www.LoadImageIntoTexture(tex);
//4.LoadFromCacheOrDownload：从缓存加载AB包对象，如果该包不在缓存则自动下载存储到缓存中，以便以后直接从本地缓存中加载
//WWW.LoadFromCacheOrDownload("http://192.168.50.109:8000/Http_Server/test.assetbundle", 1);
#endregion

#region 常用变量
//1.assetBundle：如果加载的数据是AB包，可以通过该变量直接获取加载结果
//www.assetBundle
//2.audioClip：如果加载的数据是音效切片文件，可以通过该变量直接获取加载结果
//www.GetAudioClip
//3.bytes：以字节数组的形式获取加载到的内容
//www.bytes
//4.bytesDownloaded：过去已下载的字节数
//www.bytesDownloaded
//5.error：返回一个错误消息，如果下载期间出现错误，可以通过它获取错误信息
//www.error != null
//6.isDone：判断下载是否已经完成
//www.isDone
//7.movie：如果下载的视频，可以获取一个MovieTexture类型结果
//www.GetMovieTexture()
//8.progress:下载进度
//www.progress
//9.text：如果下载的数据是字符串，以字符串的形式返回内容
//www.text
//10.texture：如果下载的数据是图片，以Texture2D的形式返回加载结果
//www.texture
#endregion
#endregion

#region 利用WWW类来异步下载或加载文件
#region 1.下载HTTP服务器上的内容
StartCoroutine(DownLoadHttp());
#endregion

#region 2.下载FTP服务器上的内容（FTP服务器一定要支持匿名账户）
StartCoroutine(DownLoadFtp());
#endregion

#region 3.本地内容加载（一般移动平台加载数据都会使用该方式）
StartCoroutine(DownLoadLocal());
#endregion
#endregion

#region 总结
//Unity中的WWW类比使用C#中的Http相关类更加的方便
//建议大家使用Unity当中为我们封装好的类来处理下载、加载相关逻辑
#endregion

IEnumerator DownLoadHttp()
{
    //1.创建WWW对象
    WWW www = new WWW("https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fi2.hdslb.com%2Fbfs%2Farchive%2F8cc2b9a7868b266800f98d42fc5d257021e75103.jpg&refer=http%3A%2F%2Fi2.hdslb.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1654745686&t=b7691b6e546367610e4331039d1a10ec");

    //2.就是等待加载结束
    while (!www.isDone)
    {
        print(www.bytesDownloaded);
        print(www.progress);
        yield return null;
    }

    print(www.bytesDownloaded);
    print(www.progress);

    //3.使用加载结束后的资源
    if (www.error == null)
    {
        image.texture = www.texture;
    }
    else
        print(www.error);
}

IEnumerator DownLoadFtp()
{
    //1.创建WWW对象
    WWW www = new WWW("ftp://127.0.0.1/test.jpg");

    //2.就是等待加载结束
    while (!www.isDone)
    {
        print(www.bytesDownloaded);
        print(www.progress);
        yield return null;
    }

    print(www.bytesDownloaded);
    print(www.progress);

    //3.使用加载结束后的资源
    if (www.error == null)
    {
        image.texture = www.texture;
    }
    else
        print(www.error);
}

IEnumerator DownLoadLocal()
{
    //1.创建WWW对象
    WWW www = new WWW("file://" + Application.streamingAssetsPath + "/test.png");

    //2.就是等待加载结束
    while (!www.isDone)
    {
        print(www.bytesDownloaded);
        print(www.progress);
        yield return null;
    }

    print(www.bytesDownloaded);
    print(www.progress);

    //3.使用加载结束后的资源
    if (www.error == null)
    {
        image.texture = www.texture;
    }
    else
        print(www.error);
}
```

#### WWWForm类

```c++
#region WWWFrom类的作用
//上节课学习了使用WWW类来下载数据
//如果想要使用WWW上传数据时，就需要配合WWWFrom类进行使用了
//而WWWFrom主要就是用于集成数据的，我们可以设置上传的参数或者2进制数据
//当结合WWWFrom上传数据时
//它主要用到的请求类型是Post
//它使用Http协议进行上传处理

//注意：
//使用WWW结合WWWFrom上传数据一般需要配合后端程序制定上传规则
#endregion

#region WWWFrom类的常用方法和变量
//该类当中我们主要就使用方法，相关变量很少使用，我们主要就着重讲解方法
//1.WWWForm：构造函数
WWWForm data = new WWWForm();
//2.AddBinaryData：添加二进制数据
//data.AddBinaryData()
//3.AddField：添加字段
//data.AddField()
#endregion

#region WWW结合WWWFrom对象来异步上传数据
StartCoroutine(UpLoadData());
#endregion

#region 总结
//WWW结合WWWFrom上传数据
//需要配合后端服务器来指定上传规则
//也就是说我们上传的数据，后端需要知道收到数据后应该如何处理
//通过这种方式我们没办法像C#类当中完成文件的上传
//但是该方式非常适合用于制作短连接游戏的前端网络层
//我们可以对WWW进行二次封装，专门用于上传自定义消息给对应的Web服务器
#endregion

IEnumerator UpLoadData()
    {
        WWWForm data = new WWWForm();
        //上传的数据 对应的后端程序 必须要有处理的规则 才能生效
        data.AddField("Name", "liuyingbo", Encoding.UTF8);
        data.AddField("Age", 99);
        data.AddBinaryData("file", File.ReadAllBytes(Application.streamingAssetsPath + "/test.png"), "testtest.png", "application/octet-stream");

        WWW www = new WWW("http://192.168.50.109:8000/Http_Server/", data);

        yield return www;

        if (www.error == null)
        {
            print("上传成功");
            //www.bytes
        }
        else
            print("上传失败" + www.error);
    }

```



#### UnityWebRequest

```c++
#region UnityWebRequest是什么？
//UnityWebRequest是一个Unity提供的一个模块化的系统类
//用于构成HTTP请求和处理HTTP响应
//它主要目标是让Unity游戏和Web服务端进行交互
//它将之前WWW的相关功能都集成在了其中
//所以新版本中都建议使用UnityWebRequest类来代替WWW类

//它在使用上和WWW很类似
//主要的区别就是UnityWebRequest把下载下来的数据处理单独提取出来了
//我们可以根据自己的需求选择对应的数据处理对象来获取数据

//注意：
//1.UnityWebRequest和WWW一样，需要配合协同程序使用
//2.UnityWebRequest和WWW一样，支持http、ftp、file协议下载或加载资源
//3.UnityWebRequest能够上传文件到HTTP资源服务器
#endregion

#region UnityWebRequest类的常用操作
//1.使用Get请求获取文本或二进制数据
//2.使用Get请求获取纹理数据
//3.使用Get请求获取AB包数据
//4.使用Post请求发送数据
//5.使用Put请求上传数据
#endregion

IEnumerator LoadText()
{
    UnityWebRequest req = UnityWebRequest.Get("http://192.168.50.109:8000/Http_Server/test.txt");
    //就会等待 服务器端响应后 断开连接后 再继续执行后面的内容
    yield return req.SendWebRequest();

    //如果处理成功 结果就是成功枚举
    if(req.result == UnityWebRequest.Result.Success)
    {
        //文本 字符串
        print(req.downloadHandler.text);
        //字节数组
        byte[] bytes = req.downloadHandler.data;
        print("字节数组长度" + bytes.Length);
    }
    else
    {
        print("获取失败:" + req.result + req.error + req.responseCode);
    }
}

#region 总结 
//UnityWebRequest使用上和WWW类很类似
//我们需要注意的是
//1.获取文本或二进制数据时
//  使用UnityWebRequest.Get
//2.获取纹理图片数据时
//  使用UnityWebRequestTexture.GetTexture
//  以及DownloadHandlerTexture.GetContent
//3.获取AB包数据时
//  使用UnityWebRequestAssetBundle.GetAssetBundle
//  以及DownloadHandlerAssetBundle.GetContent
#endregion

#region 上传相关数据类
//父接口
//IMultipartFormSection
//数据相关类都继承该接口
//我们可以用父类装子类
List<IMultipartFormSection> dataList = new List<IMultipartFormSection>();

//子类数据
//MultipartFormDataSection
//1.二进制字节数组
dataList.Add(new MultipartFormDataSection(Encoding.UTF8.GetBytes("123123123123123")));
//2.字符串
dataList.Add(new MultipartFormDataSection("12312312312312312dsfasdf"));
//3.参数名，参数值（字节数组，字符串），编码类型，资源类型（常用）
dataList.Add(new MultipartFormDataSection("Name", "liuyingbo", Encoding.UTF8, "application/...."));
dataList.Add(new MultipartFormDataSection("Msg", new byte[1024], "appl....."));

//MultipartFormFileSection
//1.字节数组
dataList.Add(new MultipartFormFileSection(File.ReadAllBytes(Application.streamingAssetsPath + "/test.png")));

//2.文件名，字节数组（常用）
dataList.Add(new MultipartFormFileSection("上传的文件.png", File.ReadAllBytes(Application.streamingAssetsPath + "/test.png")));
//3.字符串数据，文件名（常用）
dataList.Add(new MultipartFormFileSection("12312313212312", "test.txt"));
//4.字符串数据，编码格式，文件名（常用）
dataList.Add(new MultipartFormFileSection("12312313212312", Encoding.UTF8, "test.txt"));

//5.表单名，字节数组，文件名，文件类型
dataList.Add(new MultipartFormFileSection("file", new byte[1024], "test.txt", ""));
//6.表单名，字符串数据，编码格式，文件名
dataList.Add(new MultipartFormFileSection("file", "123123123", Encoding.UTF8, "test.txt"));
#endregion

#region 知识点二 Post发送相关
StartCoroutine(Upload());
#endregion

#region 知识点三 Put上传相关
//注意：Put请求类型不是所有的web服务器都认，必须要服务器处理该请求类型那么才能有相应
#endregion

#region 总结
//我们可以利用Post上传数据或上传文件
//Put主要用于上传文件，但是必须资源服务器支持Put请求类型
//为了通用性，我们可以统一使用Post请求类型进行数据和资源的上传
//它的使用和之前的WWW类似，只要前后端制定好规则就可以相互通信了
#endregion

IEnumerator Upload()
{
    //准备上传的数据 
    List<IMultipartFormSection> data = new List<IMultipartFormSection>();
    //键值对相关的 信息 字段数据
    data.Add(new MultipartFormDataSection("Name", "liuyingbo"));
    //PlayerMsg msg = new PlayerMsg();
    //data.Add(new MultipartFormDataSection("Msg", msg.Writing()));
    //添加一些文件上传文件
    //传2进制文件
    data.Add(new MultipartFormFileSection("TestTest123.png", File.ReadAllBytes(Application.streamingAssetsPath + "/test.png")));
    //传文本文件
    data.Add(new MultipartFormFileSection("123123123123123", "Test123.txt"));

    UnityWebRequest req = UnityWebRequest.Post("http://192.168.50.109:8000/Http_Server/", data);

    req.SendWebRequest();

    while (!req.isDone)
    {
        print(req.uploadProgress);
        print(req.uploadedBytes);
        yield return null;
    }

    print(req.uploadProgress);
    print(req.uploadedBytes);

    if (req.result == UnityWebRequest.Result.Success)
    {
        print("上传成功");
        //req.downloadHandler.data
    }
    else
        print("上传失败" + req.error + req.responseCode + req.result);
}

#region 高级操作指什么？
//在常用操作中我们使用的是Unity为我们封装好的一些方法
//我们可以方便的进行一些指定类型的数据获取

//比如
//下载数据时：
//1.文本和2进制
//2.图片
//3.AB包
//如果我们想要获取其它类型的数据应该如何处理呢？

//上传数据时：
//1.可以指定参数和值
//2.可以上传文件
//如果想要上传一些基于HTTP规则的其它数据应该如何处理呢？

//高级操作就是用来处理 常用操作不能完成的需求的
//它的核心思想就是：UnityWebRequest中可以将数据处理分离开
//比如常规操作中我们用到的
//DownloadHandlerTexture 和 DownloadHandlerAssetBundle两个类
//就是用来将2进制字节数组转换成对应类型进行处理的

//所以高级操作时指 让你按照规则来实现更多的数据获取、上传等功能
#endregion

#region UnityWebRequest类的更多内容
//UnityWebRequest req = UnityWebRequest.Get("");
//UnityWebRequest req = UnityWebRequestTexture.GetTexture("");
//UnityWebRequest req = UnityWebRequestAssetBundle.GetAssetBundle("");
//UnityWebRequest req = UnityWebRequest.Put()
//UnityWebRequest req = UnityWebRequest.Post

//req.isDone
//req.downloadProgress;
//req.downloadedBytes;
//req.uploadProgress;
//req.uploadedBytes

//req.SendWebRequest()

//更多内容
//1.构造函数
//UnityWebRequest req = new UnityWebRequest();

//2.请求地址
//req.url = "服务器地址";

//3.请求类型
//req.method = UnityWebRequest.kHttpVerbPOST;

//4.进度
//req.downloadProgress
//req.uploadProgress

//5.超时设置
//req.timeout = 2000;

//6.上传、下载的字节数
//req.downloadedBytes
//req.uploadedBytes

//7.重定向次数 设置为0表示不进行重定向 可以设置次数
//req.redirectLimit = 10;

//8.状态码、结果、错误内容
//req.result
//req.error
//req.responseCode

//9.下载、上传处理对象
//req.downloadHandler
//req.uploadHandler

//更多内容
//https://docs.unity.cn/cn/2020.3/ScriptReference/Networking.UnityWebRequest.html
#endregion

#region 自定义获取数据DownloadHandler相关类
//关键类：
//1.DownloadHandlerBuffer 用于简单的数据存储，得到对应的2进制数据。
//2.DownloadHandlerFile 用于下载文件并将文件保存到磁盘（内存占用少）。
//3.DownloadHandlerTexture 用于下载图像。
//4.DownloadHandlerAssetBundle 用于提取 AssetBundle。
//5.DownloadHandlerAudioClip 用于下载音频文件。

StartCoroutine(DownLoadTex());

StartCoroutine(DownLoadAB());

//以上的这些类，其实就是Unity帮助我们实现好的，用于解析下载下来的数据的类
//使用对应的类处理下载数据，他们就会在内部将下载的数据处理为对应的类型，方便我们使用

//DownloadHandlerScript 是一个特殊类。就其本身而言，不会执行任何操作。
//但是，此类可由用户定义的类继承。此类接收来自 UnityWebRequest 系统的回调，
//然后可以使用这些回调在数据从网络到达时执行完全自定义的数据处理。

StartCoroutine(DownLoadCustomHandler());
#endregion

#region 总结
//我们可以自己设置UnityWebRequest当中的下载处理对象
//当设置后，下载数据后它会使用该对象中对应的函数处理数据
//让我们更方便的获取我们想要的数据
//方便我们对数据下载或获取进行拓展
#endregion
```

