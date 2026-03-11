---
title: "Unity-简易对象池"
date: 2022-03-01T22:51:57+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192048985.png
---

在Unity中我们经常会用到对象池，使用对象池无非就是解决两个问题:

- 一是减少 new 时候寻址造成的消耗，该消耗的原因是内存碎片。
- 二是减少 Object.Instantiate 时内部进行序列化和反序列化而造成的CPU消耗。

## 设计：

从字面上理解对象池，池的意思就是容器。我们可以从池中获取一个对象(一条鱼)，也可以向池中放入一个对象(一条鱼)。获取的操作我们叫Allocate(分配),而放入一个对象我们叫Recycle(回收)。所以我们可以定义池的接口为如下:

```
public interface IPool<T>
{
	T Allocate();

	bool Recycle(T obj);
}
```

为什么要用泛型呢？如何实现一个精简并且灵活的对象池。这个灵活很大一部分是通过泛型体现的。

池是容器的意思，在C#中可以是List,Queue或者Stack甚至是数组。所以对象池本身要维护一个容器。本篇我们选取Stack来作为池容器，原因是当我们在Allocate和Recycle时并不关心缓存的存储的顺序，只要求缓存对象的地址是连续的。代码如下所示:

```
using System.Collections.Generic;

public abstract class Pool<T> : IPool<T>
{
	...
	protected Stack<T> mCacheStack = new Stack<T>();
	...
}
```

Pool是个抽象类，实现一个精简并且灵活的对象池。这个灵活很大一部分是通过抽象类体现的。

现在对象的存取和缓存接口都设计好了，那么这些对象是从哪里来的呢？我们分析下，创建对象我们知道有两种方式，反射构造方法和new一个对象。对象池的一个重要功能就是缓存，要想实现缓存就要求对象可以在对象池内部进行创建。所以我们要抽象出一个对象的工厂，代码如下所示:

```
	public interface IObjectFactory<T>
	{
		T Create();
	}
```

为什么要用工厂? 实现一个精简并且灵活的对象池。这个灵活很大一部分是通过工厂体现的。

OK，现在对象的创建，存取，缓存的接口都设计好了。下面放出Pool的全部代码。

```
	using System.Collections.Generic;

	public abstract class Pool<T> : IPool<T>
	{
		#region ICountObserverable
		/// <summary>
		/// Gets the current count.
		/// </summary>
		/// <value>The current count.</value>
		public int CurCount
		{
			get { return mCacheStack.Count; }
		}
		#endregion
	
		protected IObjectFactory<T> mFactory;

		protected Stack<T> mCacheStack = new Stack<T>();

		/// <summary>
		/// default is 5
		/// </summary>
		protected int mMaxCount = 5;

		public virtual T Allocate()
		{
			return mCacheStack.Count == 0
				? mFactory.Create()
				: mCacheStack.Pop();
		}

		public abstract bool Recycle(T obj);
	}
```

## 对象池实现

首先要实现一个对象的创建器,代码如下所示:

```
	using System;

	public class CustomObjectFactory<T> : IObjectFactory<T>
	{
		public CustomObjectFactory(Func<T> factoryMethod)
		{
			mFactoryMethod = factoryMethod;
		}
	
		protected Func<T> mFactoryMethod;

		public T Create()
		{
			return mFactoryMethod();
		}
	}
```

比较简单，只是维护了一个返回值为T的委托。
对象池实现:

```
	using System;

	/// <summary>
	/// unsafe but fast
	/// </summary>
	/// <typeparam name="T"></typeparam>
	public class SimpleObjectPool<T> : Pool<T>
	{
		readonly Action<T> mResetMethod;

		public SimpleObjectPool(Func<T> factoryMethod, Action<T> resetMethod = null,int initCount = 0)
		{
			mFactory = new CustomObjectFactory<T>(factoryMethod);
			mResetMethod = resetMethod;

			for (int i = 0; i < initCount; i++)
			{
				mCacheStack.Push(mFactory.Create());
			}
		}

		public override bool Recycle(T obj)
		{
			mResetMethod.InvokeGracefully(obj);
			mCacheStack.Push(obj);
			return true;
		}
	}
```

## 如何使用?

```
			var fishPool = new SimpleObjectPool<Fish>(() => new Fish(), null, 100);

			Log.I("fishPool.CurCount:{0}", fishPool.CurCount);

			var fishOne = fishPool.Allocate();
		
			Log.I("fishPool.CurCount:{0}", fishPool.CurCount);

			fishPool.Recycle(fishOne);
		
			Log.I("fishPool.CurCount:{0}", fishPool.CurCount);

			for (int i = 0; i < 10; i++)
			{
				fishPool.Allocate();
			}
		
			Log.I("fishPool.CurCount:{0}", fishPool.CurCount);
```

运行结果:

```
fishPool.CurCount:100
fishPool.CurCount:99
fishPool.CurCount:100
fishPool.CurCount:90
```
