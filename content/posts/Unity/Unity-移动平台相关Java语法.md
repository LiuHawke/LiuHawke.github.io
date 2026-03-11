---
title: "Unity-移动平台相关Java语法"
date: 2022-11-24T21:56:45+08:00

tags:
  - Unity
  - 移动平台
  - Android

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211242151718.jpg
---
**简单**了解Java语法

## 环境

Java环境：www.baidu.com

写Java编辑器推荐是IDEA

但是我们是Unity导出的Android项目所以还是用AndroidStudio

## 变量

### Java中的有符号整型

```java
//1.byte(1个字节，8位，-2^7 ~ 2^7 - 1，-128 ~ 127)
System.out.println("byte位数" + Byte.SIZE);
System.out.println("byte最大值" + Byte.MAX_VALUE);
System.out.println("byte最小值" + Byte.MIN_VALUE);
//2.short(2个字节，16位，-2^15 ~ 2^15 - 1，-32768 ~ 32767)
System.out.println("short位数" + Short.SIZE);
System.out.println("short最大值" + Short.MAX_VALUE);
System.out.println("short最小值" + Short.MIN_VALUE);
//3.int(4个字节，32位，-2^31 ~ 2^31 - 1，-2,147,483,648 ~ 2,147,483,647)
System.out.println("int位数" + Integer.SIZE);
System.out.println("int最大值" + Integer.MAX_VALUE);
System.out.println("int最小值" + Integer.MIN_VALUE);
//4.long(8个字节，64位，-2^63 ~ 2^63 - 1，9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807)
System.out.println("long位数" + Long.SIZE);
System.out.println("long最大值" + Long.MAX_VALUE);
System.out.println("long最小值" + Long.MIN_VALUE);
//注意：Java最初设计中没有无符号类型
//Java 8中添加了一些和无符号类型有关的一些方法(但是一般使用较少)
```



### Java中的浮点型

```Java
//1.float(4个字节，32位)
System.out.println("float位数" + Float.SIZE);
System.out.println("float最大值" + Float.MAX_VALUE);
System.out.println("float最小值" + Float.MIN_VALUE);
float f = .5f;
System.out.println(f);
f = 1.f;
System.out.println(f);
//2.double(8个字节，64位)
System.out.println("double位数" + Double.SIZE);
System.out.println("double最大值" + Double.MAX_VALUE);
System.out.println("double最小值" + Double.MIN_VALUE);
double d = 1.5;
```

### Java中的其它类型

```Java
//1.boolean(单个boolean在编译时使用int类型，这时4个字节；boolean数组时，编译时用字节数组，所以占1个字节)
//默认值是false
boolean b0 = true;
boolean[] bs;
//2.char(2个字节，16位)
System.out.println("char位数" + Character.SIZE);
char c = 'A';
System.out.println(c);
//3.String(根据字符串长度而定)
String str = "123";
System.out.println(str);
```

### Java中的常量

关键字：final

特点：必须初始化，不能被修改

### 类型转换

```Java
//1.隐式转换：低级（字节少的）到高级（字节多的）
//记住一句话 大容器 可以装 小容器 不需要我们去处理
//byte——>short——>char——>int——>long——>float——>double
byte b2 = 111;
short s2 = b2;
//2.显示转换：括号强转，高级到低级会丢失精度或者报错
int i3 = 40000;
s2 = (short) i3;
System.out.println(s2);
float f2 = 2.5f;
i3 = (int)f2;
System.out.println(i3);
//3.字符串转数值 包装类中的parse相关方法
i3 = Integer.parseInt("101", 2);
System.out.println(i3);
```

### 总结

Java的变量类型和C#非常的类似

其中需要注意的是

1.Java中没有专门的无符号类型

2.Java中的常量关键字和C#不一样

3.一些关键字和方法写法不同

其它的变量相关操作表现和C#中极奇相似



## 运算符、条件语句、循环语句、数组

Java和C#基本一样

就把它当做C#来写即可

## 

## Arrays静态类

Arrays类是什么

Arrays类是Java提供的一个专门对数组进行操作的类

它提供了很多静态方法，共我们对数组进行处理

比如排序、查找、填充等等方法

使用它 需要导入java.util.Arrays包（类似C#中的命名空间）

### 填充替换数组元素

```Java
//Arrays.fill 方法可以帮助我们对数组进行填充
//它有两个重载
//1.Arrays.fill(数组,填充值)
int arr[] = new int[5];
for (int n : arr) {
    System.out.println(n);
}
Arrays.fill(arr, 5);
System.out.println("***************");
for (int n : arr) {
    System.out.println(n);
}
//2.Arrays.fill(数组,填充起始位置(包括),填充结束位置(不包括),填充值)
Arrays.fill(arr, 1, 4, 1);
System.out.println("***************");
for (int n : arr) {
    System.out.println(n);
}

Arrays.fill(arr, 1, arr.length, 0);
System.out.println("***************");
for (int n : arr) {
    System.out.println(n);
}
```

### 数组排序

```Java
//Arrays.sort(数组) 方法可以对数组进行默认的升序排序
arr = new int[]{4,3,6,1,2,8,1};
Arrays.sort(arr);
System.out.println("***************");
for (int n : arr) {
    System.out.println(n);
}
```

### 复制数组

```Java
//1.Arrays.copyOf(数组，复制长度) 方法可以赋值指定数组的指定长度
//若长度 小于传入数组，则截取；若长度 大于传入数组，则用默认值填充
int[] arr2 = Arrays.copyOf(arr, 10);
System.out.println("***************");
for (int n : arr2) {
    System.out.println(n);
}

//2.Arrays.copyOfRange(数组，开始复制索引（包括），赋值的最后索引位置（不包括）)
arr2 = Arrays.copyOfRange(arr, 1, 4);
System.out.println("***************");
for (int n : arr2) {
    System.out.println(n);
}
```

### 查询数组

```Java
//Arrays.binarySearch(数组，搜索元素)
//注意：由于binarySearch内部是对数组进行二分查找
//所以使用前必须对数组进行排序
Arrays.sort(arr);
//一定该记住 使用该API之前 一定概要排序
//如果数组当中没有想要寻找的元素 那么返回值为负数
int index = Arrays.binarySearch(arr, 1);
System.out.println("***************");
System.out.println(index);

//Arrays.binarySearch(数组，开始索引(包括)，结束索引(不包括)，搜索元素)
```

### 总结

Java中如果相对数组进行填充、替换、排序、复制、查询等操作

我们可以使用Arrays类中的静态方法

填充替换：fill

排序：sort

复制：copyOf、copyOfRange

查询：binarySearch



## 函数

Java中函数和C#中函数的使用基本一致

函数的声明、重载、使用等就按照C#中的基础规则使用即可

主要区别：

1.Java中没有ref和out关键字

2.Java中可变参数写法有些许不同

3.Java中函数参数没有默认参数用法

4.Java中没有结构体，所以不存在结构体语句块

5.Java中一般使用驼峰命名法  myName（驼峰命名法）  MyName(帕斯卡命名法)



## 面向对象

概念：万物皆对象，用程序来抽象（形容）对象，用面向对象的思想来编程

面向对象三大特性：封装、继承、多态

面向对象是一种编程范式

面向对象编程语言的编程思想和使用几乎是一致的

使用C#中的面向对象编程思想去写Java即可

Java中也是通过class类去封装对象的

### 注意：

Java中没有C#中的

1.成员属性（有类似自动生成的get、set方法）

2.析构函数（有类似的finalize函数）

3.索引器

4.运算符重载

### 封装

Java中的类声明和使用和C#中基本一模一样

### 继承

#### Java中继承和C#中的相同点

1.类与类之间只能单继承

2.万物之父都是Object

3.遵循里氏替换原则(可以父类装子类对象)

#### Java中 继承的写法

//关键字：extends

#### 万物之父Object中的关键方法

1.getClass():类似C#中的getType()

Father f = new Father();

System.out.println(f.getClass());

2.toString():将对象以字符串形式返回

3.equals():比较两个对象的内部内容，而==是判断引用地址是否相等

父子类的上下转换（里氏替换原则）

1.子类自动向上转换

``Father f2 = new Son();``

2.父类强制向下转换

``Son s = (Son)f2;``

#### instanceof关键字

```Java
//用于判断对象类型，类似C#中的is关键字

if(f2 instanceof Father)

{

System.out.println("是Father类");

}

if(f2 instanceof Son)

{

Son s2 = (Son)f2;

System.out.println("是Son类");

}
```

#### final关键字

类似C#中的密封关键字

让变量无法被修改（常量）

让函数无法被重写

让类无法被继承

### 多态

#### Java中的vob

Java中没有vo关键字，也就是virtual(虚函数)和override(重写)

我们如果要重写基类的方法，直接在子类中实现同名方法即可

它会直接覆盖父类方法！！

Java中有类似 base 的关键字 super



重写时Java会自动补全一个@Override

它类似于注释，可有可无，主要是用来帮助我们阅读代码

#### 抽象类和抽象方法

Java中的抽象类和抽象方法使用和C#中基本一致

关键字：abstract

抽象类不能被实例化

抽象方法在抽象类中声明，必须被实现

#### 接口

1.Java中接口中方法默认(只能)为public

2.Java中接口可以声明字段，但是默认(只能)是static和final的，也就是静态常量

3.Java中继承接口的关键字是implements，一般写在继承类之后

4.继承多个接口用逗号分隔

5.Java中不存在显示实现接口，重写不同接口的同名方式时，"两个接口共享"



## 包

### Java中的包是什么？

Java中的包（package）类似C#中的命名空间

主要用于解决类名冲突

同名类做不同逻辑时

可以将两个类放到两个不同的包中

### 包名规则

多人完成，版权属于发起者

indi.发起这名.项目名.模块名....



独立完成，公开，版权属于个人

pers.个人名.项目名.模块名



独立完成，非公开，版权属于个人

priv.个人名.项目名.模块名



团队研发，版权属于团队

team.团队名.项目名.模块名



公司研发，版权属于公司

com.公司名.项目名.模块名



注意：Java中规定包名统一用小写，用.分割



## Java中的内部类

Java中的内部类的使用和C#中不同

```Java
//1.Java中的内部类在外部使用时必须伴随包裹它的外部类的出现而出现
//  声明语法为
//  外部类 outer = new 外部类();
//  外部类.内部类 inner = outer.new 内部类();
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
inner.Test();
//2.外部类中的所有内容（包括私有）都可以直接在内部类中使用
//3.内部类和外部类中的同名成员，通过this 和 外部类.this来区分
```

### 匿名内部类

匿名内部类，在声明一个类对象时临时拓展该对象

但是拓展的内容一般是基于外部类的

注意：

1.不能写构造方法

2.不能定义静态成员

3.一般基于外部类已有方法实现内容

4.如果匿名类创建的对象不记录，用完一次后会马上被销毁

```Java
Outer outer2 = new Outer(){
    @Override
    public void Eat() {
        System.out.println("吃东西");
    }

    @Override
    public void Speak() {
        System.out.println("说话");
    }
};
outer2.Eat();
outer2.Speak();
```



## String和StringBuilder

Java中String在使用上和C#类似，但是需要注意以下两点区别

1.直接赋值字符串，如果字符串相同，则指向同一引用;new String，即使字符串相同引用也不同

2.Java中字符串比较使用equals，用==号是比较的引用地址



Java中的StringBuilder的使用和作用和C#中基本一致

如果项目中的字符串会频繁修改，建议使用StringBuilder



## 泛型

 Java中泛型使用的基本规则和C#中一致

泛型可以代表一切类型，主要使用上就是泛型类和泛型方法



### Java中的泛型中的类型通配符 ?

泛型类名称<? extends 类名> 变量名 = null;

? extends 类名 表示类未知，需要使用该泛型对象时，可以单独实例化

```TestT<?> obj2 = null;```



## Collection接口

Java中的集合类就类似C#中的List和Dictionary的存在

主要用于批量存储对象，并且可以动态添加，动态删除

![image-20221217234153008](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202212172341179.png)

Collection接口中的方法

1.add(E e) 添加元素

2.remove(Object o) 移除元素

3.clear() 清空元素

4.isEmpty() 是否为空

5.iterator() 获取迭代器，可以用于遍历

6.size() 集合中元素个数

7.contains() 判断元素是否存在

8.toArray() 将容器中元素转为数组



### ArrayList和LinkedList类

两者方法上的使用完全一致，因为他们继承相同的接口

ArrayList本质是数组，是顺序存储

LinkedList本质是链表，是链式存储



### HashSet和TreeSet的区别

相同点：他们都不允许存储重复的元素，如果传入重复的只会记录一个

他们相对上节课的ArrayList和LinkedList最大的使用区别就是他们可以去重

都不能像ArrayList和LinkedList通过索引获取或修改元素



不同点：他们的底层数据结构不同，HashSet底层结构是哈希表，TreeSet底层结构是树

TreeSet是有序排列的（会自动将加入元素进行排序），HashSet不一定有序

TreeSet相对HashSet由于多继承了接口，所以有更多的方法可以使用



注意：TreeSet具有排序功能

元素是数字时，按照大小升序排列

元素是字符串时，按照字符的编码值升序排列

元素是自定义类时，可以继承Comparable接口 重载其中的方法

特别是compareTo方法，它用于制定排序规则



## Map接口

Map集合类似C#中的Dictionary字典

以键值对形式存储数据

![image-20221217235027997](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202212172350034.png)





### Map接口中的方法

1.put(K key, V value) 添加键值对

2.containsKey(Object key) 判断是否存在键

3.containsValue(Object value) 判断是否存在值

4.get(Object key) 如果存在key，则返回对应值，否则返回null

5.keySet() 返回该集合中的所有key对象形成的Set集合

6.values() 返回该集合中所有value对象形成的Collection集合

7.size() 键值对 对数

8.isEmpty() 是否为空

9.remove(Object key) 根据键移除

10.clear() 清空容器



### HashMap和TreeMap之间的区别

相同点：他们都是以键值对形式存储数据，方法使用基本相同

不同点：

1.HashMap允许有null键和null值（但是必须保持键的唯一性）TreeMap不允许键为空

2.TreeMap中的映射关系具有一定的顺序，它会帮助我们进行排序因此在添加、删除、定位映射关系时，效率较HashMap差

3.HashMap数据结构基于哈希表，TreeMap数据结构基于树

在实际使用时，建议都使用HashMap，除非需要排序的Map时才用TreeMap



## 异常捕获

```Java
Java中的异常捕获和C#中使用基本一致 需要时直接使用即可
try{
    //需要进行异常捕获的代码块
}
catch (NullPointerException ex)
{
    //ex.getMessage()
    //捕获异常信息
}
catch (ArrayIndexOutOfBoundsException ex)
{
    //ex.getMessage()
    //捕获异常信息
}
catch (Exception ex)
{
    //ex.getMessage()
    //捕获异常信息
}
finally {
    //不管之前的代码块是否发生异常
    //finally中的内容都将执行
    //注意：以下几种情况下finally中内容不会执行
    //1.finally 语句块中发生异常
    //2.在finally之前执行了System.exit()退出程序
    //3.程序所在的线程销毁
    //4.关闭CPU
}

Java中的常见异常类
//NullPointerException:空指针异常
//IOException:输入输出异常
//FileNotFoundException:文件为找到异常
//ArrayIndexOutOfBoundsException:数组越界异常
//NegativeArraySizeException:数组元素个数为负数抛出异常
//ClassCastException:类型转化异常
//ArithmeticException:算数异常
//ArrayStoreException:数组包含不兼容的值抛出的异常
//ClassNotFoundException:未找到相应类异常
//等等

Java中自定义异常
//继承Exception异常基类
//实现一个有参构造函数
//通过throw关键字自己抛出异常对象
try
{
    int i = -3;
    if(i < 0)
    {
        MyException ex = new MyException("数组容量不合法");
        ex.i = i;
        throw ex;
    }
    int[] ints = new int[i];
}
catch (MyException ex)
{
    System.out.println(ex.getMessage() + "；数组容量为：" + ex.i);
}
finally {
    System.out.println("异常捕获结束");
}

方法中抛出异常
//如果某个方法可能发生异常，但是又不想再方法内处理这个异常
//想在调用这个方法的外部进行处理
//可以使用throws关键字在声明方法时抛出可能的异常
//多个异常可以使用逗号分割
//那么外部调用该方法时，发生对应异常时即可进行处理
//注意：
//继承时，覆盖有抛异常的方法时，该方法必须抛出相同的异常或者异常的子类
//如果父类中方法抛出多个异常，那么子类的覆盖方法必须抛出那些异常的子集，不能抛出新异常
try
{
    Test();
}
catch (NegativeArraySizeException ex)
{
    System.out.println("容量为负了");
}
catch (ArrayIndexOutOfBoundsException ex)
{
    System.out.println("索引越界");
}
```



## lambda表达式

**Java中的lambda表达式和C#中区别很大**

**Java中没有委托和事件**

**只有通过函数式接口来存储lambda表达式来进行使用**

```Java
Java中 lambda表达式语法
//基本结构：
//(参数)->{代码块}
//基于基本结构的写法：
//1. ()->{代码块}
//2. ()->结果表达式（相当于返回值）;
//3. (参数)->{代码块}
//4. 参数->{代码块}
//5. 参数->结果表达式（相当于返回值）;
//6. (参数1,参数2,参数3...)->{代码块}
//7. (参数1,参数2,参数3...)->结果表达式（相当于返回值）;

用于装载lambda表达式的函数式接口
//函数式接口指仅仅包含一个抽象方法的接口
//声明函数式接口后，我们可以用以下语法来使用lambda表达式
//接口名 变量名 = lambda表达式
//注意：
//lambda表达式的结构必须和函数式接口中声明的方法一致（返回值，参数数量）
ITest t = ()->{
    int i = 1;
    int i2 = 2;
    return i + i2;
};

System.out.println(t.Test());

ITest2 t2 = (a,b)->{
    System.out.println(a);
    System.out.println(b);
};

t2.Test(10, "123");

int value1 = 10;
lambda表达式调用外部变量
//1.无法更改局部变量的值
//2.可以更改外部类的成员变量的值
t = ()->{
    value2 = 100;
    return value2;
};
System.out.println(t.Test());
```



## Java常用类库

### 常用类库指什么？

常用类库主要指Java中为了提升我们的开发效率

预先就已经写好的一些包，这些包中提供了很多常用类

目的是方便开发人员的使用，而无需再自己去实现

主要有以下内容

1.变量类型的包装类 Integer、Double、Boolean、Character、Number等

2.存储较大数据的 BigInteger类和BigDecimal类

3.数学计算类 Math

4.随机数类 Random

等等

本节课，我主要给大家指明方向，由于这些知识只是API的使用

所以对于这些类的常用方法我只会提及，不会举例

了解他们提供的方法名，基本就知道如何使用了



### 变量的包装类

#### Number类

   它是Byte Integer Short Long Float Double类的父类

   主要方法：

   byteValue()  以byte形式返回指定的数值

   intValue()   以int形式返回指定的数值

   floatValue() 以float形式返回指定的数值

   shortValue() 以short形式返回指定的数值

   longValue()  以long形式返回指定的数值

   doubleValue()  以double形式返回指定的数值

   所有继承Number类的子类都具备以上方法



#### Integer类

   它和Byte、Short、Long三个封装类方法基本相同

   区别就是封装的是不同的数据类型

   主要方法：

   parseInt(String str)                  将字符串转数值

   toString()                            将数值转字符串

   toBinaryString(int i)                 以二进制无符号整数形式返回一个整数参数的字符串表示形式

   toHexString(int i)                    以十六进制无符号整数形式返回一个整数参数的字符串表示形式

   toOctalString(int i)                  以八进制无符号整数形式返回一个整数参数的字符串表示形式

   equals(Object integerObj)             比较两个对象是否相等

   compareTo(Integer anotherInteger)     比较两个Integer对象，相等返回0；调用函数对象小于传入对象，返回负数；反之，返回正数



#### Double类

   它和Float类的方法基本相同

   区别就是封装的是不同的数据类型

   主要方法：

   parseDouble(String str)               将字符串转数值

   toString()                            将数值转字符串

   isNaN()                               如果该double值不是数字，返回true，否则返回false

   compareTo(Double d)                   和Integer类中方法作用一致

   equals(Object doubleObj)              和Integer类中方法作用一致

   toHexString(double d)                 返回double参数的十六进制字符串表示形式



#### Boolean类

   equals(Object obj)                    和Integer类中方法作用一致

   parseBoolean(String s)                将字符串转Boolean

   toString()                            将数值转字符串

   valueOf(String s)                     返回一个用指定的字符串表示的boolean值



#### Character类

   compareTo(Character anotherCharacter) 比较两个Character对象，若两个对象相等则返回0

   equals(Object obj)                    和Integer类中方法作用一致

   toString()                            转字符串

   toUpperCase(char ch)                  将字符转大写

   toLowerCase(char ch)                  将字符转小写

   isUpperCase(char ch)                  判断字符是否是大写

   isLowerCase(char ch)                  判断字符是否是小写

   isLetter(char ch)                     判断字符是否是字母

   isDigit(char ch)                      判断字符是否为数字



### 大数据类

#### BigInteger

   该类主要用于存储任意大小的整数，也就是说它可以表示任何大小的整数值而不会丢失信息

   因为传统的整形类型都有最大最小区间，而该类没有，主要用于存储大数据

   主要方法：

   add(BigInteger val)               加法

   subtract(BigInteger val)          减法

   multiply(BigInteger val)          乘法

   divide(BigInteger val)            除法

   remainder(BigInteger val)         取余

   pow(int exponent)                 计算exponent次方

   negate()                          取反

   shiftLeft(int n)                  左移n位

   shiftRight(int n)                 右移n位

   and(BigInterger val)              位与

   or(BigInteger val)                位或

   compareTo(BigInteger val)         比较，类似Integer中

   equals(Object x)                  判断数值是否相等

   min(BigInteger val)               取最小

   max(BigInteger val)               取最大、





#### BigDecimal

   该类和BigInteger用于表示大数据，但是它主要用于表示浮点数（有小数点的数值）

   它的主要方法和BigInteger类似



### 数学计算类

#### Math类

提供众多数学函数方法，方便我们进行数学计算

主要包含三角函数、指数函数、取整函数、最大值、最小值、平均值等等

这些方法都是静态方法，都通过Math点出使用

主要方法：

三角函数

sin(double a)                         正弦

cos(double a)                         余弦

tan(double a)                         正切

asin(double a)                        反正弦

acos(double a)                        反余弦

atan(double a)                        反正切

toRadians(double angdeg)              角度转弧度

toDegrees(double angrad)              弧度转角度

指数

exp(double a)                         获取e的a次方

log(double a)                         取自然对数

log10(double a)                       取底数为10的a的对数

sqrt(double a)                        取a的平方根

cbrt(double a)                        取a的立方根

pow(double a, double b)               取a的b次方

取整

ceil(double a)                        向上取整

floor(double a)                       向下取整

rint(double a)                        返回与a最接近的整数，如果有两个，取偶数

round(float a)                        将参数a加上0.5后返回与其最近的整数

rount(double a)                       将参数a加上0.5后返回与其最近的整数，然后强转为Long

其他

max(参数1，参数2)                      最大值

min(参数1，参数2)                      最小值

abs(参数)                             绝对值



### 随机数类

Random

用于生成随机数的类

主要方法：

Random r = new Random();              以当前系统时间作为随机数生成器种子

Random r = new Random(seedValue);     自己设置随机数种子

nextInt()                             返回一个随机整数

nextInt(int n)                        返回大于等于0且小于n的随机整数

nextLong()                            返回一个随机长整型

nextBoolean()                         返回一个随机布尔值

nextFloat()                           返回一个随机单精度浮点

nextDouble()                          返回一个随机双精度浮点

nextGaussian()                        返回一个概率密度为高斯分步的双精度浮点



### 其他类

Data：日期类 获取日期时间相关方法

Calendar：日历类 比起Date更加国际化

System:系统类 有获取当前时间的方法

等等



总结

Java中提供了很多现成的类和方法供我们使用，对他们产生印象，当自己开发时有类似需求时，直接使用他们，可以提升我们的开发效率

因为无需我们自己去实现对应逻辑，直接使用即可
