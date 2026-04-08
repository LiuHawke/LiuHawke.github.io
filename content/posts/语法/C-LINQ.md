---
title: C# LINQ
date: 2022-11-24 22:49:16
tags:
  - C#
  - 语法
categories: C#
cover: 
    image : https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211191917907.png
---

LINQ 是 Language INtegrated Query 单词的首字母缩写，翻译过来是语言集成查询。它为查询跨各种数据源和格式的数据提供了一致的模型，所以叫集成查询。由于这种查询并没有制造新的语言而只是在现有的语言基础上来实现，所以叫语言集成查询。

## 基础

在 C# 中，从功能上 LINQ 可分为两类：LINQ to Object 和 LINQ to XML；从语法上 LINQ 可以分为 LINQ to Object 和 LINQ 扩展方法

纠正：

从功能上 LINQ 可分为两类：

- LINQ to Object，查询内存集合，直接把查询编译成 .NET 代码执行。
- LINQ to Provider，查询自定义数据源，由开发者提供相应数据源的 Provider 并翻译和执行自定义查询，如 XML、JSON 等都可以作为 Provider 对应的数据源，数据源对应的 LINQ 查询叫 LINQ to <数据源>，比如：LINQ to XML。

从语法上 LINQ 可以分为：

- SQL风格：语法和 SQL 相似，部分复杂查询用 SQL 风格语义会更清晰明了，比如 SelectMany 和 Join 查询。SQL 风格的可读性有绝对优势，但不支持全部标准 LINQ 函数，不支持自定义函数。纯粹的语法糖。
- 函数风格：以 C# 扩展方法的方式实现，扩展方法即可以是标准库提供的也可以是自己实现的，完全的原生编程风格，编译后的代码都是函数调用。支持全部标准 LINQ 函数和任何自定义函数。随着查询复杂度的提高，可读性不如 SQL 风格。

感谢 @coredx 的纠正。

所有 LINQ to Object 都可以用 LINQ 扩展方法实现等同的效果，而且平时开发中用的最多的是 LINQ 扩展方法。

LINQ to Object 多用于映射数据库的查询，LINQ to XML 用于查询 XML 元素数据。使用 LINQ 查询的前提是对象必须是一个 IEnumerable 集合（注意，为了描述方便，本文说的集合都是指 IEnumerable 对象，包含字面上的 ICollection 对象）。另外，LINQ 查询大多是都是链式查询，即操作的数据源是 `IEnumerable<T1>` 类型，返回的是 `IEnumerable<T2>` 类型，T1 和 T2 可以相同，也可以不同。

形如下面这样的查询就是 LINQ to Object：

```cs
var list = from user in users
  where user.Name.Contains("Wang")
  select user.Id;
```

等同于使用下面的 LINQ 扩展方法：

```cs
var list = users
  .Where(u => user.Name.Contains("Wang"))
  .Select(u => u.id);
```

LINQ 查询支持在语句中间根据需要定义变量，比如取出数组中平方值大于平均值的数字：

```cs
int[] numbers = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
var result = from number in numbers
            let average = numbers.Average()
            let squared = Math.Pow(number, 2)
            where squared > average
            select number;
// 平均值为 4.5, result 为 { 3, 4, 5, 6, 7, 8, 9 }
```

其中的 Select 方法接收的参数用的最多的是 `Func<TSource, TResult>`，它还可以接收 `Func<TSource, int, TResult>` 参数，示例:

```cs
var collectionWithRowNumber = collection.
    .Select((item, index) => new { Item = item, RowNumber =index })
    .ToList();
```

再来看一下 LINQ to XML 的示例。假如我们有如下 XML 文件：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Employees>
  <Employee>
    <EmpId>1</EmpId>
    <Name>Liam</Name>
    <Sex>男</Sex>
  </Employee>
  <Employee>
    <EmpId>2</EmpId>
    ...
  </Employee>
</Employees>
```

使用 LINQ to XML 查询所有含有指定节点值的元素：

```cs
XElement xelement = XElement.Load("Employees.xml");
var els = from el in xelement.Elements("Employee")
          where (string)el.Element("Sex") == "Male"
          select el;
```

等同于使用 LINQ 扩展方法：

```cs
var els = xelement.Elements("Employee")
    .Where(el => (string)el.Element("Sex") == "Male");
```

LINQ to XML 操作 XML 非常方便和灵活，大家可以在具体使用的时候去探索，这里就不展开讲了。

LINQ 查询有很多方法，由于篇幅原因，就不一一列举演示了，这里只选取一些强大的查询方法，这些方法若使用非 LINQ 来实现可能会比较麻烦。

LINQ 之所以强大，是因为它可以轻松实现复杂的查询，下面我们来总结一下 C# LINQ 的强大之处。

## First、Last 和 Single 等

First、FirstOrDefault、Last、LastOrDefault、Single 和 SingleOrDefault 是快速查询集合中的第一个或最后一个元素的方法。如果集合是空的，Fist、Last 和 Single 都会报错，如果使其不报错而在空集合时使用默认值可以使用 FirstOrDefault、LastOrDefault 和 SingleOrDefault。Single/SingleOrDefault 和其它方法的区别是，它限定查询结果只有一个元素，如果查询结果集合中包含多个元素时会报错。具体看下面几个示例：

```cs
new[] { "a", "b" }.First(x => x.Equals("b")); // 返回 ”b“
new[] { "a", "b" }.First(x => x.Equals("c")); // 抛出 InvalidOperationException 异常
new[] { "a", "b" }.FirstOrDefault(x => x.Equals("c")); // 返回 null

new[] { "a", "b" }.Single(x => x.Equals("b")); // 返回 ”b“
new[] { "a", "b" }.Single(x => x.Equals("c")); // 抛出 InvalidOperationException 异常
new[] { "a", "b" }.SingleOrDefault(x => x.Equals("c")); // 返回 null
new[] { "a", "a" }.Single(); // 抛出 InvalidOperationException 异常
```

在实际应用中，如果要确保查询结果的唯一性（比如通过手机号查询用户），使用 Single/SingleOrDefaut，其它情况应尽量使用 First/FirstOrDefault。虽然 FirstOrDefault 也可以根据条件判断元素是否存在，但使用 Any 更高效。

## Except 取差集

LINQ 的 Except 方法用来取差集，即取出集合中与另一个集合所有元素不同的元素。

示例：

```cs
int[] first = { 1, 2, 3, 4 };
int[] second = { 0, 2, 3, 5 };
IEnumerable<int> result = first.Except(second);
// result = { 1, 4 }
```

注意 Except 方法会去除重复元素：

```cs
int[] second = { 0, 2, 3, 5 };
int[] third = { 1, 1, 1, 2, 3, 4 };
IEnumerable<int> result = third.Except(second);
// result = { 1, 4 }
```

对于简单类型（int、float、string 等）使用 Except 很简单，但对于自定义类型（或者叫复合类型，下同）的 Object 如何使用 Except 呢？此时需要将自定义类型实现`IEquatable<T>`接口，示例：

```cs
class User : IEquatable<User>
{
    public string Name { get; set; }

    public bool Equals(User other)
    {
        return Name == other.Name;
    }

    public override int GetHashCode()
    {
        return Name?.GetHashCode() ?? 0;
    }
}

class Program
{
    static void Main(string[] args)
    {
        var list1 = new List<User>
        {
            new User{ Name = "User1"},
            new User{ Name = "User2"},
        };

        var list2 = new List<User>
        {
            new User{ Name = "User2"},
            new User{ Name = "User3"},
        };

        var result = list1.Except(list2);
        result.ForEach(u => Console.WriteLine(u.Name));
        // 输出：User1
    }
}
```

## SelectMany 集合降维

SelectMany 可以把多维集合降维，比如把二维的集合平铺成一个一维的集合。举例：

```cs
var collection = new int[][]
{
    new int[] {1, 2, 3},
    new int[] {4, 5, 6},
};
var result = collection.SelectMany(x => x);
// result = [1, 2, 3, 4, 5, 6]
```

再来举个更贴合实际应用的例子。例如有如下实体类（一个部门有多个员工）：

```cs
class Department
{
    public Employee[] Employees { get; set; }
}

class Employee
{
    public string Name { get; set; }
}
```

此时，我们拥有一个这样的数据集合：

```cs
var departments = new[]
{
    new Department()
    {
        Employees = new []
        {
            new Employee { Name = "Bob" },
            new Employee { Name = "Jack" }
        }
    },
    new Department()
    {
        Employees = new []
        {
            new Employee { Name = "Jim" },
            new Employee { Name = "John" }
        }
    }
};
```

现在我们可以使用 SelectMany 把各部门的员工查询到一个结果集中：

```cs
var allEmployees = departments.SelectMany(x => x.Employees);
foreach(var emp in allEmployees)
{
    Console.WriteLine(emp.Name);
}
// 依次输出：Bob Jack Jim John
```

## SelectMany 迪卡尔积运算

SelectMany 不光适用于单个包含多维集合对象的降维，也适用于多个集合之前的两两相互操作，比如进行迪卡尔积运算。比如我们有这样两个集合：

```cs
var list1 = new List<string> { "a1", "a2" };
var list2 = new List<string> { "b1", "b2", "b3" };
```

现在我们需要把它进行两两组合，使用普通的方法，我们需要用嵌套循环语句来实现：

```cs
var result = newList<string>();
foreach (var s1 in list1)
    foreach (var s2 in list2)
        result.Add($"{s1}{s2}");
// result = ["a1b1", "a1b2", "a1b3", "a2b1", "a2b2", "a2b3"]
```

改用 SelectMany 实现：

```cs
var result = list1.SelectMany(x => list2.Select(y => $"{x}{y}"));
// result = ["a1b1", "a1b2", "a1b3", "a2b1", "a2b2", "a2b3"]
```

具有挑战性的问题来了，如何对 N 个集合进行迪卡尔积运算呢，比如有这样的集合数据：

```cs
var arrList = new List<string[]>
{
    new string[] { "a1", "a2" },
    new string[] { "b1", "b2", "b3" },
    new string[] { "c1" },
    // ...
};
```

如何对上面的 arrList 中的各个集合进行两两组合呢？在电商业务尤其是零售业务中的产品组合促销中这种需求很常见。

下面是一个使用 SelectMany 的实现，需要用到递归：

```cs
class Program
{
    static void Main(string[] args)
    {
        var arrList = new List<string[]>
        {
            new string[] { "a1", "a2" },
            new string[] { "b1", "b2", "b3" },
            new string[] { "c1" },
            // ...
        };

        var result = Recursion(arrList, 0, new List<string>());
        result.ForEach(x => Console.WriteLine(x));
    }

    static List<string> Recursion(List<string[]> list, int start, List<string> result)
    {
        if (start >= list.Count)
            return result;

        if (result.Count == 0)
            result = list[start].ToList();
        else
            result = result.SelectMany(x => list[start].Select(y => x + y)).ToList();

        result = Recursion(list, start + 1, result);

        return result;
    }
}
```

输出：

```bash
a1b1c1
a1b2c1
a1b3c1
a2b1c1
a2b2c1
a2b3c1
```

类似这种集合的迪卡尔积运算操作，也可以用 LINQ to Object 来代替 SelectMany 实现：

```cs
result = result.SelectMany(x => list[start].Select(y => x + y)).ToList();
// 等同使用扩展方法：
result = (from a in result from b in list[start] select a + b).ToList();
```

LINQ to Object 比扩展方法看上去易读性更好，但写起来扩展方法更方便。

## Aggregate 聚合

Aggregate 扩展方法可以对一个集合依次执行类似累加器的操作，就像滚雪球一样把数据逐步聚集在一起。比如实现从 1 加到 10，用 Aggregate 扩展方法就很方便：

```cs
int[] numbers = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
int sum = numbers.Aggregate((prevSum, current) => prevSum + current);
// sum = 55
```

我们来解析一下它的执行步骤

- 第一步，prevSum 取第一个元素的值，即 prevSum = 1
- 第二步，把第一步得到的 prevSum 的值加上第二个元素，即 prevSum = prevSum + 2
- 依此类推，第 i 步把第 i-1 得到的 prevSum 加上第 i 个元素

再来看一个字符串的例子加深理解：

```cs
string[] stringList = { "Hello", "World", "!" };
string joinedString = stringList.Aggregate((prev, current) => prev + " " + current);
// joinedString = "Hello World !"
```

Aggregate 还有一个重载方法，可以指定累加器的初始值。我们来看一个比较综合的复杂例子。假如我们有如下 1-12 的一个数字集合：

```CS
var items = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 };
```

现在我们想做如下计算：

- 计算集合元素的总数个数
- 计算值为偶数的元素个数
- 收集每第 4 个元素

当然通过普通的循环遍历也可以实现这三个计算，但使用 Aggregate 会更简洁，下面是 Aggregate 的实现：

```cs
var result = items.Aggregate(new { Total = 0, Even = 0, FourthItems = new List<int>() },
    (accum, item) =>
    new
    {
        Total = accum.Total + 1,
        Even = accum.Even + (item % 2 == 0 ? 1 : 0),
        FourthItems = (accum.Total + 1) % 4 == 0 ? new List<int>(accum.FourthItems) { item } : accum.FourthItems
    }
);

// result:
// Total = 12
// Even = 6
// FourthItems = [4, 8, 12]
```

这里为了简单起见使用匿名类型作为累加器的初始值，由于匿名类型的属性是只读的，所以在累加的过程都 new 了一个新对象。如果初始值使用的是自定义类型，那累加时就不需 new 新对象了。

## Join 关联查询

和 SQL 查询一样，LINQ 同样支持 Inner Join、Left Join、Right Join、Cross Join 和 Full Outer Join，有时候你可能看到不同的写法，其实是同一个意思，比如 Left Outer Join 就是 Left Join，Join 是 Inner Join 省略了 Inner 等。

假设我们有下面两个集合，分别表示左边的数据和右边的数据。

```cs
var first = new List<string>() { "a","b","c" }; // 左边
var second = new List<string>() { "a", "c", "d" }; // 右边
```

下面以此数据为例来演示各种关联查询。

### Inner Join

```cs
 var result = from f in first
              join s in second on f equals s
              select new { f, s };
// 等同使用扩展方法：
var result = first.Join(second,
    f => f,
    s => s,
    (f, s) => new { f, s });

// result: {"a","a"}
//         {"c","c"}
```

### Left Join

```cs
var result = from f in first
            join s in second on f equals s into temp
            from t in temp.DefaultIfEmpty()
            select new { First = f, Second = t };
// 或者：
var result = from f in first
            from s in second.Where(x => x == f).DefaultIfEmpty()
            select new { First = f, Second = s };

// 等同使用扩展方法：
var result = first.GroupJoin(second,
        f => f,
        s => s,
        (f, s) => new { First = f, Second = s })
    .SelectMany(temp => temp.Second.DefaultIfEmpty(),
        (f, s) => new { First = f.First, Second = s });

// result: {"a","a"}
//         {"b", null}
//         {"c","c"}
```

### Right Join

```cs
var result = from s in second
            join f in first on s equals f into temp
            from t in temp.DefaultIfEmpty()
            select new { First = t, Second = s };
// 其它和 Left Join 类似

// result: {"a","a"}
//         {"c","c"}
//         {null,"d"}
```

### Cross Join

```cs
var result = from f in first
            from s in second
            select new { f, s };

// result: {"a","a"}
//         {"a","c"}
//         {"a","d"}
//         {"b","a"}
//         {"b","c"}
//         {"b","d"}
//         {"c","a"}
//         {"c","c"}
//         {"c","d"}
```

### Full Outer Join

```cs
var leftJoin = from f in first
            join s in second on f equals s into temp
            from t in temp.DefaultIfEmpty()
            select new { First = f, Second = t };
var rightJoin = from s in second
            join f in first on s equals f into temp
            from t in temp.DefaultIfEmpty()
            select new { First = t, Second = s };
var fullOuterJoin = leftJoin.Union(rightJoin);
```

### 根据多个键关联

在 SQL 中，表与表进行关联查询时 on 条件可以指定多个键的逻辑判断，用 and 或 or 连接。但 C# 的 LINQ 不支持 and 关键字，若要根据多键关联，需要把要关联的键值分别以相同的属性名放到匿名对象中，然后使用 equals 比较两个匿名对象是否相等。示例：

```cs
var stringProps = typeof(string).GetProperties();
var builderProps = typeof(StringBuilder).GetProperties();
var query =
    from s in stringProps
    join b in builderProps
    on new { s.Name, s.PropertyType } equals new { b.Name, b.PropertyType }
    select new
    {
        s.Name,
        s.PropertyType
    };
```

以上均使用两个集合做为示例，LINQ 关联查询也支持多个集合关联，就像 SQL 的多表关联，只需往后继续追加 join 操作即可，不再累述。

LINQ 关联查与 SQL 相似，但使用上有很大区别。LINQ 关联查询的用法有很多，也很灵活，不用刻意去记住它们，只要熟悉简单常用的，其它的在实际用到的时候再查询相关文档。

## Skip & Take 分页

Skip 扩展方法用来跳过从起始位置开始的指定数量的元素读取集合；Take 扩展方法用来从集合中只读取指定数量的元素。

```cs
var values = new[] { 5, 4, 3, 2, 1 };
var skipTwo = values.Skip(2);  // { 3, 2, 1 }
var takeThree = values.Take(3);  // { 5, 4, 3 }
var skipOneTakeTwo = values.Skip(1).Take(2); // { 4, 3 }
```

Skip 与 Take 两个方法结合即可实现我们常见的分页查询：

```cs
public IEnumerable<T> GetPage<T>(this IEnumerable<T> collection, int pageNumber, int pageSize)
{
    int startIndex = (pageNumber - 1) * pageSize;
    return collection.Skip(startIndex).Take(pageSize);
}
```

使用过 EF (Core) 的同学一定很熟悉。

另外，还有 SkipWhile 和 TakeWhile 扩展方法，它与 Skip 和 Take 不同的是，它们的参数是具体的条件。SkipWhile 从起始位置开始忽略元素，直到遇到不符合条件的元素则停止忽略，往后就是要查询的结果；TakeWhile 从起始位置开始读取符合条件的元素，一旦遇到不符合条件的就停止读取，即使后面还有符合条件的也不再读取。示例：

SkipWhile：

```cs
int[] list = { 42, 42, 6, 6, 6, 42 };
var result = list.SkipWhile(i => i == 42);
// result: 6, 6, 6, 42
```

TakeWhile：

```cs
int[] list = { 1, 10, 40, 50, 44, 70, 4 };
var result = list.TakeWhile(item => item < 50).ToList();
// result = { 1, 10, 40 }
```

## Zip 拉链

Zip 扩展方法操作的对象是两个集合，它就像拉链一样，根据位置将两个系列中的每个元素依次配对在一起。其接收的参数是一个 Func 实例，该 Func 实例允许我们成对在处理两个集合中的元素。如果两个集合中的元素个数不相等，那么多出来的将会被忽略。

示例：

```cs
int[] numbers = { 3, 5, 7 };
string[] words = { "three", "five", "seven", "ignored" };
IEnumerable<string> zip = numbers.Zip(words, (n, w) => n + "=" + w);

foreach (string s in zip)
{
    Console.WriteLine(s);
}
```

输出：

```bash
3=three
5=five
7=seven
```

## OfType 和 Cast 类型过滤与转换

OfType 用于筛选集合中指定类型的元素，Cast 可以把集合转换为指定类型，但要求源类型必须可以隐式转换为目标类型。假如有如下数据：

```cs
interface IFoo { }
class Foo : IFoo { }
class Bar : IFoo { }

var item0 = new Foo();
var item1 = new Foo();
var item2 = new Bar();
var item3 = new Bar();
var collection = new IFoo[] { item0, item1, item2, item3 };
```

OfType 示例：

```cs
var foos = collection.OfType<Foo>(); // result: item0, item1
var bars = collection.OfType<Bar>(); // result: item2, item3
var foosAndBars = collection.OfType<IFoo>(); // result: item0, item1, item2, item3

// 等同于使用 Where
var foos = collection.Where(item => item is Foo); // result: item0, item1
var bars = collection.Where(item => item is Bar); // result: item2, item3
```

Cast 示例：

```cs
var bars = collection.Cast<Bar>();  // InvalidCastException 异常
var foos = collection.Cast<Foo>();  // InvalidCastException 异常
var foosAndBars = collection.Cast<IFoo>();  // OK
```

## ToLookup 索引式查找

ToLookup 扩展方法返回的是可索引查找的数据结构，它是一个 ILookup 实例，所有元素根据指定的键进行分组并可以按键进行索引。这样说有点抽象，来看具体示例：

```cs
string[] array = { "one", "two", "three" };
// 根据元素字符串长度创建一个查找对象
var lookup = array.ToLookup(item => item.Length);

// 查找字符串长度为 3 的元素
var result = lookup[3];
// result: one,two
```

再来一个示例：

```cs
int[] array = { 1,2,3,4,5,6,7,8 };
// 创建一个奇偶查找（键为 0 和 1）
var lookup = array.ToLookup(item => item % 2);

// 查找偶数
var even = lookup[0];
// even: 2,4,6,8

// 查找奇数
var odd = lookup[1];
// odd: 1,3,5,7
```

## Distinct 去重

Distinct 方法用来去除重复项，这个容易理解。示例：

```cs
int[] array = { 1, 2, 3, 4, 2, 5, 3, 1, 2 };
var distinct = array.Distinct();
// distinct = { 1, 2, 3, 4, 5 }
```

简单类型的集合调用 Distinct 方法使用的是默认的比较器，Distinct 方法用此比较器来判断元素是否与其它元素重复，但对于自定义类型要实现去重则需要自定义比较器。示例：

```cs
public class IdEqualityComparer : IEqualityComparer<Person>
{
    public bool Equals(Person x, Person y) => x.Id == y.Id;
    public int GetHashCode(Person p) => p.Id;
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

class Program
{
    static void Main(string[] args)
    {
        var people = new List<Person>();
        var distinct = people.Distinct(new IdEqualityComparer());
    }
}
```

## ToDictionary 字典转换

ToDictionary 扩展方法可以把集合 `IEnumerable<TElement>` 转换为 `Dictionary<TKey, TValue>` 结构的字典，它接收一个 `Func<TSource, TKey>` 参数用来返回每个元素指定的键与值。示例：

```cs
IEnumerable<User> users = GetUsers();
Dictionary<int, User> usersById = users.ToDictionary(x => x.Id);
```

如果不用 ToDictionary，你需要这样写：

```cs
IEnumerable<User> users = GetUsers();
Dictionary<int, User> usersById = new Dictionary<int, User>();
foreach (User u in users)
{
    usersById.Add(u.Id, u);
}
```

上面 ToDictionary 返回的字典数据中的值是整个元素，你也可以通过它的第二个参数来自定义字典的值。示例：

```cs
Dictionary<int, string> userNamesById = users.ToDictionary(x => x.Id, x => x.Name);
```

你也可以为转换的字典指定其键是否区分大小写，即自定义字典的 IComparer，示例：

```cs
Dictionary<string, User> usersByCaseInsenstiveName = users.ToDictionary(x =>x.Name,
    StringComparer.InvariantCultureIgnoreCase);

var user1 =usersByCaseInsenstiveName["liam"];
var user2 =usersByCaseInsenstiveName["LIAM"];
user1 == user2; // true
```

注意，字典类型要求所有键不能重复，所以在使用 ToDictionary 方法时要确保作为字典的键的元素属性不能有重复值，否则会抛出异常。

## 其它常见扩展方法

LINQ 还有很多其它常见的扩展方法，大家在平时应该用的比较多，比如 Where、Any、All 等，这里也选几个简单举例介绍一下。

### Range 和 Repeat

Range 和 Repeat 用于生成简单的数字或字符串系列。示例：

```cs
// 生成 1-100 的数字，即结果为 [1, 2, ..., 99, 100]
var range = Enumerable.Range(1, 100);

// 生成三个重复的字符串“a”，即结果为 ["a", "a", "a"]
var repeatedValues = Enumerable.Repeat("a", 3);
```

### Any 和 All

Any 用来判断集合中是否存在任一一个元素符合条件，All 用来判断集合中是否所有元素符合条件。示例：

```cs
var numbers = new int[] {1, 2, 3, 4, 5 };
bool result = numbers.Any(); // true
bool result = numbers.Any(x => x == 6); // false
bool result = numbers.All(x => x > 0); // true
bool result = numbers.All(x => x > 1); // false
```

### Concat 和 Union

Concat 用来拼接两个集合，不会去除重复元素，示例：

```cs
List<int> foo = newList<int> { 1, 2, 3 };
List<int> bar = newList<int> { 3, 4, 5 };
// 通过 Enumerable 类的静态方法
var result = Enumerable.Concat(foo, bar).ToList(); // 1,2,3,3,4,5
// 通过扩展方法
var result = foo.Concat(bar).ToList(); // 1,2,3,3,4,5
```

Union 也是用来拼接两个集合，与 Concat 不同的是，它会去除重复项，示例：

```cs
var result = foo.Union(bar); // 1,2,3,4,5
```

### GroupBy 分组

GroupBy 扩展方法用来对集合进行分组，下面是一个根据奇偶进行分组的示例：

```cs
var list = new List<int>() { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
var grouped = list.GroupBy(x => x % 2 == 0);
// grouped: [1, 3, 5, 7, 9] 和 [2, 4, 6, 8]
```

还可以根据指定属性进行分组：

```cs
public class Person
{
    public int Age { get; set; }
    public string Name { get; set; }
}

var people = new List<Person>();
var query = people
    .GroupBy(x => x.Age)
    .Select(g => { Age = g.Key, Count = g.Count() });
```

### DefaultIfEmpty 空替换

在上面的关联查询中我们使用了 DefaultIfEmpty 扩展方法，它表示在没有查询到指定条件的元素时使用元素的默认值代替。其实 DefaultIfEmpty 还可以指定其它的默认值，示例：

```cs
var chars = new List<string>() { "a", "b", "c", "d" };
chars.Where(s => s.Length > 1).DefaultIfEmpty().First(); // 返回 null
chars.DefaultIfEmpty("N/A").FirstOrDefault(); // 返回 "a"
chars.Where(s => s.Length > 1).DefaultIfEmpty("N/A").FirstOrDefault(); // 返回 "N/A"
```

### SequenceEqual 集合相等

SequenceEqual 扩展方法用于比较集合系列各个相同位置的元素是否相等。示例：

```cs
int[] a = new int[] {1, 2, 3};
int[] b = new int[] {1, 2, 3};
int[] c = new int[] {1, 3, 2};

bool result1 = a.SequenceEqual(b); // true
bool result2 = a.SequenceEqual(c); // false
```

## 最后

还有一些常用和简单的扩展方法就不举例了，比如 OrderBy（排序）、Sum（求和）、Count（计数）、Reverse（反转）等，同时欢迎大家补充本文遗漏的强大或好用的 LINQ 语法糖。
