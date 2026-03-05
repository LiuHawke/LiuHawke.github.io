---
title: C#数据结构与算法
tags:
  - C#
  - 语法
categories:
  - C#
date: 2022-02-10 22:17:46
cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191917907.png
---

## 数据结构与算法

### 什么是数据结构与算法？

在学习数据结构与算法的时候，常常会思考一个问题：什么是数据结构与算法？

这里比较找到了一个比较好的答案。

从宏观来讲：

- 数据结构是一组数据的存储结构。
- 算法就是操作数据的方法。
- 数据结构为算法服务，算法要作用在特定的数据结构中。

从狭义上来讲，就是数据结构与算法这门学科就是去学习比较经典的数据结构和算法。

有点像学习设计模式一样，学习 23 种设计模式。

### 如何学习数据结构与算法？

说到设计模式，学习数据结构与算法与学习设计模式非常相似的。

我们知道设计模式有 23 种，而一般一个开发者学习设计模式的时候，往往会对 23 个设计模式都从头到尾学一遍，但是学完之后发现除了记了个名字之外没啥卵用，就连名字也不一定记得住。

那么是以为 23 种设计模式的核心并不是设计模式本身，而是 23 种设计模式所遵循的 SOLID 原则，即六大设计原则，而开发者一开始最应该掌握的是六大设计原则，而不是设计模式，六大设计原则是核心，而 23 种设计模式算是 23 种对于六大设计原则的案例一样，一定要先搞清楚这一点。否则学习就会事倍功半，当然如果有一定的编码经验再去学设计模式更好一些。

学习数据结构与算法也是一样的，数据结构与算法的核心是时间复杂度和空间复杂度，就像对于设计模式中的六大设计原则一样，掌握了时间复杂度和空间复杂度的评估，那么剩下的经典的常用的数据结构和算法就是一个个案例而已，而不用死记硬背，当然有一定的编码经验再去学习会更好一些。

这就是数据结构与算法的学习思路。

### 为什么要学习数据结构与算法？

- 国内大厂的面试比较爱考，但是考得不是很难
- Google、Facebook、微软等更爱考，而且非常难。
- 可以评估代码性能，写出高性能的代码
- 开发时选择合适的数据结构
- 比较有名的基础框架，都柔和了很多基础数据解耦股和算法的设计思想
- 阅读源码的时候可以减少阻碍（比如遇到一些算法的实现）
- 提高编程能力
- 思维提升
- 简历上可以写一个精通数据结构与算法

### 数据结构与算法是不是高智商开发者的专属？

当然不是，数据结构与算法的常用的知识点不多，而且也不需要高智商。重点还是熟能生巧。

### 数据结构与算法学科的解决问题重点是什么？

- 更省更快地存储和处理数据的问题
  ![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923357.png)

### 时间复杂度

时间复杂度的定义很简单：

- 算法的执行时间与数据规模之间的增长关系

时间复杂度的全称其实叫做：渐进时间复杂度，我们就叫时间复杂度即可。

时间复杂度的定义难得的清晰，不像一些设计模式这样的定义非常抽象。

OK，既然时间复杂度的定义这么简单，那么我们也顺便说一下空间复杂度吧，空间复杂度的定义如下：

- 算法的存储空间与数据规模之间的增长关系。

空间复杂度的定义也非常直白。

 List 的遍历，代码如下：

```c#
var list = new List<string>(){"a","b","c"};
		
foreach (var s in list)
{
    Debug.Log(s);
}
```

代码中的 list 的数据规模是 3，其实就是数据量，那么这一段代码所执行的时间就是， Debug.Log(s) 所执行的时间 x 3。

用公式表示如下：

- 代码总共执行的时间 = Debug.Log(s) 执行一次的时间 x list.Count

那么该代码的执行时间与数据规模之间的增长关系是怎么样的呢？

其实以上的公式就回答了这个问题，代码的执行时间与数据规模之间的增长关系是一比一关系，即数据规模增长一倍，那么代码的执行时间也会增长一倍。

这里我们用 T(n) 表示代码总共执行的时间，用 UnitTime 表示 Debug.Log(s) 执行一次的时间，用 n 表示 数据的规模（lisnt.Count)，代入以上公式，如下：

- T(n) = UnitTime x n

通过以上公式就能够算出以上代码所执行花费的时间，只不过 UnitTime 的执行时间非常依赖具体的软硬件环境，而以上这个公式实际上是 **事后统计法** 的统计方式。

而我们的时间复杂度，需要算出来的并不是代码中共执行所花费总时间，而是表示一个关系，即：代码执行时间与数据规模这件的增长关系。

而目前的 T(n) = UnitTime x n 这个公式，根据我们的所学的数学知识，能够得出的增长关系就是正比例关系。

但是在数据结构与算法中用大 O 表示法来表示算法执行时间与数据规模的增长关系，那么大 O 表示法如何使用呢？

很简单，就是 O(T(n))，但是这个 O(T(n)) 不是最终计算完的版本，还需要一些计算的过程。

第一步是吧 T(n) 代入到 O(T(n)) 中，结果如下：

- O(UnitTime x n)

接着把系数、加减的常量、数量级更低的 n 都排除掉。

在 UnitTime x n 中，UnitTime 虽然目前是未知数，但是我们可以确定的是它肯定是一个常数，所以就直接把 UnitTime 去掉。

最终得到的执行时间与数据规模的增长关系如下：

- O(n)

即，遍历 list 这个算法的执行时间与数据规模的增长关系，也就是所谓的时间复杂度为 O(n)。

时间复杂度这个概念对大家来说不是很难，但是一定要掌握，因为时间复杂度这个概念是整个 数据结构与算法 这门学科的灵魂。



## List

```c#
using System.Collections.Generic;
using UnityEngine;

namespace DataStructure
{
	public class ListExample : MonoBehaviour
	{
		void Start()
		{
			// ["List","数据结构与算法"]
			var list = new List<string>()
			{
				"List",
				"数据结构与算法"
			};
		
			// ["List","数据结构与算法","框架搭建"]
			list.Add("框架搭建");
		
			// ["List","数据结构与算法","框架搭建","Shader 案例"]
			list.Add("Shader 案例");
		
			// ["数据结构与算法","框架搭建","Shader 案例"]
			list.Remove("List");
		
			// ["框架搭建","Shader 案例"]
			list.RemoveAll((content) => content.StartsWith("数据"));

			// ["插入的数据","框架搭建","Shader 案例"]
			list.Insert(0, "插入的数据");

			// ["插入的数据","Shader 案例"]
			list.RemoveAt(1);

			foreach (var content in list)
			{
				Debug.Log(content);
			}
		}
	}
}
```

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923086.png)

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923377.png)

### List 中的数据结构概念

List 有一些数据结构相关的特性，比如：

- 动态扩容
- 内存连续（任意访问）

这些仅仅是一些特性，并不是概念。

那么 List 中有哪些数据结构的概念呢？

答案是线性表。

## 线性表 与 非线性表

线性表，全名为线性存储结构。使用线性表存储数据的方式可以这样理解，即“把所有数据用一根线儿串起来，再存储到物理空间中”。

每个线性表上的数据最多只有前和后两个方向。

List 就是一种线性表，因为 List 符合以上的线性表的定义。

List 本质是一个数组，只不过这个数组是支持动态扩容的。通过看 List 源码就可以知道 List 内部维护了一个数组。

所以数组也是一种线性表，所以 List 与数组一样，也是一种线性表。

到这里大家可能会问，除了数组是线性表之外，还有哪些类型的数据结构是线性表？

答案是：链表、栈、队列。

而恰好，C# 中也有实现的数据结构与之对应，如下：
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923712.png)

而这些数据结构则是接下来要学习的内容。

既然有了线性表，那么就应该有非线性表，非线性表的常见数据结构如下：

- 散列/哈希表（hash）
- 树：二叉树、堆等
- 图

之所以这些数据结构叫非线性表是因为数据之间并不是简单的前后关系。

## Dictionary

### 字典简介

Dictionary 是 C# 中比较常用的数据结构，从功能角度来说，用 List 和 Dictionary 其实就能满足大部分的业务开发需求。

Dictionary 是 key-value 存储的数据结构，即通过 key 去访问 value 值。而 List 则是通过索引去访问 value 值。

```c#
using System.Collections.Generic;
using System.Linq;
using UnityEngine;

namespace DataStructure2020
{
	public class DictionaryExample : MonoBehaviour
	{
		void Start()
		{
			// {
			// 	"name":"Unity C# 数据结构与算法精讲,
			// 	"price":"9.9"
			// }
			var dictionary = new Dictionary<string,string>()
			{
				{"name","Unity C# 数据结构与算法精讲"},
				{"price","9.9"}
			};
		
			// {
			// 	"name":"Unity C# 数据结构与算法精讲,
			// 	"price":"9.9",
			//	"createAt":"2021 年 12 月 12 日"
			// }
			dictionary.Add("createAt","2021 年 12 月 12 日");
		
			// {
			// 	"name":"Unity C# 数据结构与算法精讲,
			// 	"price":"9.9",
			//	"createAt":"2021 年 12 月 12 日"
			//	"updateAt":"2021 年 12 月 12 日"
			// }
			dictionary.Add("updateAt","2021 年 12 月 12 日");

			// {
			// 	"name":"Unity C# 数据结构与算法精讲,
			// 	"price":"9.9",
			//	"updateAt":"2021 年 12 月 12 日"
			// }
			dictionary.Remove("createAt");
		
			// {
			// 	"name":"Unity C# 数据结构与算法精讲,
			// 	"price":"9.9",
			//	"updateAt":"2021 年 12 月 12 日"
			// }
			dictionary["updateAt"] = "2021 年 12 月 12 日";
		
			Debug.Log("@@@@ keys @@@@");
			dictionary.Keys.ToList().ForEach(Debug.Log);
		
			Debug.Log("@@@@ values @@@@");
			dictionary.Values.ToList().ForEach(Debug.Log);

			Debug.Log("@@@@ key:value @@@@");
			dictionary.ToList().ForEach(kv => Debug.LogFormat("key:{0},value:{1}", kv.Key, kv.Value));
		
			Debug.Log("@@@@ containsKey @@@@");
			Debug.LogFormat("dictionary.ContainsKey(updateAt):{0}", dictionary.ContainsKey("updateAt"));
			Debug.LogFormat("dictionary.ContainsKey(createAt):{0}", dictionary.ContainsKey("createAt"));
		}
	}
}
```

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923831.png)

## Stack

### Stack 简介

Stack 叫做栈，栈数据结构的一个特点就是：后进先出，先进后出。

就是一摞叠在一起的盘子。我们平时放盘子的时候，都是从下往上一个一个放，取的时候，我们也是从上往下一个个地依次取，不能从中间人物抽出。

从栈的操作特性上来看，栈是一种“操作受限”的线性表，只允许在一端插入和删除数据。

```c#
using System.Collections.Generic;
using UnityEngine;

namespace DataStructure
{
	public class StackExample : MonoBehaviour
	{
		void Start()
		{
			// []
			var stack = new Stack<string>();

			// ["数据结构"]
			stack.Push("数据结构");

			// ["数据结构","算法"]
			stack.Push("算法");

			// ["数据结构","Stack","算法"]
			stack.Push("Stack");

			// ["数据结构","算法"]
			var liangxie = stack.Pop();

			Debug.Log("pop liangxie:" + liangxie);

			// ["数据结构","算法"]
			var algo = stack.Peek();

			Debug.Log("peek algo:" + algo);

			Debug.Log("@@@@ stack foreach @@@@");

			foreach (var title in stack)
			{
				Debug.Log(title);
			}
		}
	}
}
```

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923146.png)

## Queue

### Queue 简介

Queue 即，队列，队列是一种先进先出，后进后出的线性表。

那么队列也有两个基本的操作：

- Enqueue：入队
- Dequeue：出队

```c#
using System.Collections.Generic;
using UnityEngine;

namespace DataStructure
{
	public class QueueExample : MonoBehaviour
	{
		void Start()
		{
			var queue = new Queue<string>();

			// ["数据结构"]
			queue.Enqueue("数据结构");

			// ["数据结构","算法"]
			queue.Enqueue("算法");

			// ["数据结构","算法","Queue"]
			queue.Enqueue("Queue");

			// ["算法","Queue"]
			var dataStructure = queue.Dequeue();

			Debug.Log("dequeue dataStructure:" + dataStructure);

			var algo = queue.Peek();

			Debug.Log("peek algo:" + algo);

			Debug.Log("@@@@ foreach queue @@@@");
			foreach (var title in queue)
			{
				Debug.Log(title);
			}
		}
	}
}
```

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923546.png)

## HashSet

### HashSet 基本使用

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace DataStructure
{
	public class HashSetExample : MonoBehaviour
	{
		void Start()
		{
			var set = new HashSet<string>();

			// ["数据结构"]
			set.Add("数据结构");
		
			// ["数据结构","算法"]
			set.Add("算法");

			// ["数据结构","算法"]
			set.Add("算法");
		
			// ["数据结构","算法","HashSet"]
			set.Add("HashSet");

			// ["数据结构","算法"]
			set.Remove("HashSet");

			Debug.Log("contains 算法:" + set.Contains("算法"));
			Debug.Log("contains HashSet:" + set.Contains("HashSet"));
		
			Debug.Log("@@@@ set foreach @@@@");
			foreach (var title in set)
			{
				Debug.Log(title);
			}
		}
	}
}
```

![](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191923618.png)



## LinkedList 

```c#
using System.Collections.Generic;
using UnityEngine;

namespace DataStructure
{
	public class LinkedListExample : MonoBehaviour
	{
		void Start()
		{
			var linkedList = new LinkedList<string>();

			// "数据结构"
			var dataStructureNode = linkedList.AddFirst("数据结构");

			// "算法"<=>"数据结构"
			var algoNode = linkedList.AddFirst("算法");

			// "算法"<=>"数据结构"<=>"LinkedList"
			var liangxieNode = linkedList.AddLast("LinkedList");

			// "算法"<=>"专栏"<=>"数据结构"<=>"LinkedList"
			linkedList.AddAfter(algoNode, "专栏");

			// "算法"<=>"专栏"<=>"数据结构"<=>"笔记"<=>"LinkedList"
			linkedList.AddBefore(liangxieNode, "笔记");

			// "算法"<=>"专栏"<=>"笔记"<=>"LinkedList"
			linkedList.Remove("数据结构");

			// "算法"<=>"专栏"<=>"笔记"
			linkedList.Remove(liangxieNode);

			var zhuanlanNode = linkedList.Find("专栏");

			Debug.Log("find zhuanlanNode:" + zhuanlanNode.Value);

			Debug.Log("@@@@ linkedList foreach @@@@");

			foreach (var s in linkedList)
			{
				Debug.Log(s);
			}
		}
	}
}
```

从结果和代码中注释和结果来看，LinkedList 中存储的元素并不是数据，而是一个个节点 LinkedListNode，而其中的节点才会去存储真正的数据。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924101.png)

### 链表 简介

链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的

定义中有两个重点：

- 内存中不连续、不顺序
- 逻辑上通过链表节点的指针实现

也就是说与数组相反，链表在内存中是不连续的，既然不连续，那就不支持任意访问。不连续还有个好处就是不用考虑因动态扩容造成的 GC Alloc 和 GC Collect。

首先我们先定义一个链表节点对象，如下：
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924665.png)
即，该类有两个字段：

- 指向下一个节点的 Next 字段
- 用于存储数据的 Data

而一个比较典型的链表数据结构如下图所示；
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924769.png)

图中的这种链表叫做单向链表。

单向链表的特点就是只有一个 Next 指针指向下一个节点。

那么到这里大家可能会问，链表 与 数组相比有什么优劣呢？

这里笔者简单总结下它们的优劣：

链表：

- 不支持任意访问，想要实现任意访问的时间复杂度为 O(n)
- 天然支持动态扩容
- 在头结点或者尾结点插入数据的时间复杂度为 O(1)
- 在中间插入结点的方式分为两种：
  - 得到插入位置结点的情况下，插入一个节点的时间复杂度为 O(1)
  - 在没有得到插入位置结点的情况下，插入一个时间复杂度为 O(n)
- 查询某个符合条件的节点的时间复杂度为 O(n)
- 删除节点的时间复杂度也同理：
  - 在头和尾，或者得到插入位置的节点情况下是 O(1)
  - 否则是 O(n)
- 内存中不连续

数组：

- 支持任意访问，时间复杂度为 O(1)
- 不支持动态扩容，List 支持
- 插入数据的时间复杂度为 O(n)，因为要考虑数据搬移
- 删除数据的时间复杂度为 O(n)，也是要考虑数据搬移
- 查询符合条件的某个数据的时间复杂度是 O(n)
- 内存中连续

以上就是链表和数组的简单对比，不同的存储形式有不同的功能和作用，所以如何挑选合适的数据结构对程序的性能来说是非常重要的。

## Hash

### Hash 简介

Hash 即哈希或者叫散列。

```c#
using System;
using System.Linq;
using UnityEngine;

namespace DataStructure2020
{
	public class HashPrincipleExample : MonoBehaviour
	{
		/// <summary>
		/// 学生类
		/// </summary>
		class Student
		{
			public string Id;

			public string Name;
		}
	
		/// <summary>
		/// 学生数组
		/// </summary>
		Student[] mStudent = new Student[]
		{
			new Student() {Id ="1001",Name = "小数"}, 
			new Student() {Id ="1002",Name = "小据"}, 
			new Student() {Id ="1003",Name = "小结"}, 
			new Student() {Id ="1004",Name = "小构"}, 
			new Student() {Id ="1005",Name = "小与"}, 
		};

		/// <summary>
		/// 根据 Id 获取学生对象
		/// </summary>
		/// <param name="id"></param>
		/// <returns></returns>
		Student GetStudentById(string id)
		{
			var idNumber = int.Parse(id);
		
			var index = idNumber - 1001;

			return mStudent[index];
		}

		private void Start()
		{
			// 查询学生
			var student = GetStudentById("1003");
		
			Debug.Log(student.Id);
		}
	}
}
```

GetStudentById 的所使用的思想就是散列的思想，即通过把一个 key 转换成数组的下标，根据下标直接获取数据的思想。

### 散列表

散列表，即 Hashtable，是有一种使用散列思想的数据结构。而在 C# 中也有一个叫做 Hashtable 的数据结构。

我们都知道，数组是支持下标的任意访问的，散列表用的就是数组的任意访问的特性，所以散列表其实就是数组的一种扩展，由数组演化而来。可以说，如果没有数组，就没有散列表。

### 基本原理

散列表由三个部分组成，即：

- key（关键字）
- hash function（散列/哈希函数）
- table（用于存储的数组）

我们在上一篇知道，散列的思想本质上就是将 key 转换为 数组的下标，然后通过下标去访问数组的数据的一种思想，所以通过这一点不难看出，以上的三个部分中 hash function 就是将 key 转换为下标的一个方法。

而 table 一般就是用数组或者能够通过下标访问的一些数据结构实现。

其流程如下图所示：
![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924815.png)

### 散列冲突

Hashtable 的 hash function 其实就是将 key 的 hash code 转换为 数组下标。

代码如下：

- uint hashcode = InitHash(key, buckets.Length, out seed, out incr);
- int bucketNumber = (int) (seed % (uint)buckets.Length);

散列冲突指的就是，不同的 key 得到的 bucketNumber 有可能是一致的，也就是说数组的下标会产生重复的情况。

]把发生数组小标重复的情况叫做 散列冲突。

### 插槽

在发生散列冲突的时候，我们就需要解决掉这个冲突。

而解决这个冲突的方式一般有两种：

- 开放寻址法（或闭散列法）
- 链表法（或开散列法）

开放寻址法的特点非常简单，当发生散列冲突的时候，会在数组内寻找下一个可用的位置来存储数据，这时候会对数组进行遍历操作。

开放寻址法的缺陷就是当数组空余空间越少，发生散列冲突的概率越大，所以 C# 的 Hashtable 通过动态扩容来解决这个问题。

而链表法的特点则是，当法神散列冲突的时候，会在冲突位置来创建一个链表来存储数据。

而当发生冲突的时候，不管是开放寻址法还是链表法，都会去其他的内存空间中找到或者创建一个插槽（slot），用于存放数据。

在 C# 的 Hashtable 中，插槽可以理解为数组中一个空的 bucket。

由于在 C# Hashtable 中没有链表相关的代码，所以可以推测出 Hashtable 是使用开放寻址法来解决散列冲突。

### Hashtable 与 HashSet的区别

Hashtable 与 HashSet 的基本存储单元以及集合的不同导致了对应的 添加、删除、查询等算法的差异。

在 Hashtable 中，添加一个数据的流程如下：

- 获取 key 的 hashcode：
  - uint hashcode = InitHash(key, buckets.Length, out seed, out incr);
- 获取下标：
  - int bucketNumber = (int) (seed % (uint)buckets.Length);
- 存储值：
  - buckets[bucketNumber].val = nvalue;
  - buckets[bucketNumber].key = key;
  - buckets[bucketNumber].hash_coll |= (int) hashcode;

在 HashSet 中，添加一个数据的流程如下：

- 获取 hash code：
  - hashCode = value != null ? value.GetHashCode() : 0;
- 根据 hash code 获取 bucket （Entry 的下标）
  - bucket = ref GetBucketRef(hashCode);
- 下标前移

```
int i = bucket - 1; // Value in _buckets is 1-based
```

- 存储

```
ref Entry entry = ref entries![index];
entry.HashCode = hashCode;
entry.Next = bucket - 1; // Value in _buckets is 1-based
entry.Value = value;
bucket = index + 1;
_version++;
location = index;
```

其实，两者存储的流程是差不多的，区别在于当数据发生冲突的时候。

![image.png](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191924811.png)
