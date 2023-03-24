# 使用 ObjectsComparer 比较中的对象。网

> 原文：<https://levelup.gitconnected.com/using-objectscomparer-to-compare-objects-in-net-c-2a4faab9630e>

![](img/5b55695209398ae1835415ffd3a0ef51.png)

迪特尔马·贝克尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

对象比较器框架提供了通过属性递归比较复杂对象的机制(支持数组、列表、不同类型的动态对象等)，允许覆盖特定属性和类型的比较规则。

# 介绍

当需要比较复杂的对象时，这是很常见的情况。有时对象可以包含嵌套元素，或者一些成员应该从比较中排除(自动生成的标识符、创建/更新日期等。)，或者某些成员可以有自定义的比较规则(不同格式的相同数据，如电话号码)。这个小框架就是为解决这类问题而开发的。

简而言之，Objects Comparer 是一个对象对对象的比较器，它允许递归地逐个成员地比较对象，并为某些属性、字段或类型定义自定义的比较规则。

对象比较器可用于。净核心项目(。Net 标准 1.6、2.0 或以上)和 in。Net 项目(4.5 版或更高版本)。

支持的对象:

*   原始类型
*   类别和结构
*   可枚举(数组、集合、列表、字典等。)
*   多维数组
*   列举
*   旗帜
*   动态对象(`ExpandoObject`、`DynamicObject`和编译器生成的动态对象)。

# 装置

对象比较器可以作为 NuGet 包安装。

安装包对象比较器

# 源代码

源代码可以在 [**GitHub**](https://github.com/ValeraT1982/ObjectsComparer) 上找到。

# 基本示例

为了展示如何使用对象比较器，让我们创建两个类

```
public class ClassA
{
    public string StringProperty { get; set; }public int IntProperty { get; set; }public SubClassA SubClass { get; set; }
}public class SubClassA
{
    public bool BoolProperty { get; set; }
}
```

下面是一些例子，说明如何使用 Objects Comparer 来比较这些类的实例。

隐藏复制代码

```
*//Initialize objects and comparer*
var a1 = new ClassA { StringProperty = "String", IntProperty = 1 };
var a2 = new ClassA { StringProperty = "String", IntProperty = 1 };
var comparer = new Comparer<ClassA>();*//Compare objects*
IEnumerable<Difference> differences;
var isEqual = comparer.Compare(a1, a2, out differences);*//Print results*
Debug.WriteLine(isEqual ? "Objects are equal" : string.Join(Environment.NewLine, differenses));
```

> `*Objects are equal*`

在下面的例子中**比较对象**和**打印结果**为了简洁起见，除了一些情况外，将跳过这些块。

隐藏复制代码

```
var a1 = new ClassA { StringProperty = "String", IntProperty = 1 };
var a2 = new ClassA { StringProperty = "String", IntProperty = 2 };
var comparer = new Comparer<ClassA>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='IntProperty', Value1='1', Value2='2'.*`

```
var a1 = new ClassA { SubClass = new SubClassA { BoolProperty = true } };
var a2 = new ClassA { SubClass = new SubClassA { BoolProperty = false } };
var comparer = new Comparer<ClassA>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='SubClass.BoolProperty', Value1='True', Value2='False'.*`

```
var a1 = new StringBuilder("abc");
var a2 = new StringBuilder("abd");
var comparer = new Comparer<StringBuilder>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='', Value1='abc', Value2='abd'.*`

# 可枚举(数组、集合、列表等。)

在这种情况下，可枚举数可以有不同数量的元素，或者某些元素可以有不同的值。可枚举数可以是泛型或非泛型的。在非泛型可枚举元素的情况下，如果该元素的类型相等，则具有相同索引的元素将被比较，否则与`DifferenceType=TypeMismatch` 的差异将被添加到差异列表中。

```
var a1 = new[] { 1, 2, 3 };
var a2 = new[] { 1, 2, 3 };
var comparer = new Comparer<int[]>();
```

> `*Objects are equal*`

```
var a1 = new[] { 1, 2 };
var a2 = new[] { 1, 2, 3 };
var comparer = new Comparer<int[]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Length', Value1='2', Value2='3'.*`

```
var a1 = new[] { 1, 2, 3 };
var a2 = new[] { 1, 4, 3 };
var comparer = new Comparer<int[]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='[1]', Value1='2', Value2='4'.*`

```
var a1 = new ArrayList { "Str1", "Str2" };
var a2 = new ArrayList { "Str1", 5 };
var comparer = new Comparer<ArrayList>();
```

> `*Difference: DifferenceType=TypeMismatch, MemberPath='[1]', Value1='Str2', Value2='5'.*`

# 设置

```
var a1 = new[] { 1, 2, 3 };
var a2 = new[] { 1, 2, 3 };
var comparer = new Comparer<int[]>();
```

> `*Objects are equal*`

```
var a1 = new HashSet<int> { 1, 2, 3 };
var a2 = new HashSet<int> { 2, 1, 4 };
var comparer = new Comparer<HashSet<int>>();
```

> `*Difference: DifferenceType=MissedElementInSecondObject, MemberPath='', Value1='3', Value2=''.<br /> Difference: DifferenceType=MissedElementInFirstObject, MemberPath='', Value1='', Value2='4'.*`

# 多维数组

```
var a1 = new[] { new[] { 1, 2 } };
var a2 = new[] { new[] { 1, 3 } };
var comparer = new Comparer<int[][]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='[0][1]', Value1='2', Value2='3'.*`

```
var a1 = new[] { new[] { 1, 2 } };
var a2 = new[] { new[] { 2, 2 }, new[] { 3, 5 } };
var comparer = new Comparer<int[][]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Length', Value1='1', Value2='2'.*`

```
var a1 = new[] { new[] { 1, 2 }, new[] { 3, 5 } };
var a2 = new[] { new[] { 1, 2 }, new[] { 3, 5, 6 } };
var comparer = new Comparer<int[][]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='[1].Length', Value1='2', Value2='3'.*`

```
var a1 = new[,] { { 1, 2 }, { 1, 3 } };
var a2 = new[,] { { 1, 3, 4 }, { 1, 3, 8 } };
var comparer = new Comparer<int[,]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Dimension1', Value1='2', Value2='3'.*`

```
var a1 = new[,] { { 1, 2 } };
var a2 = new[,] { { 1, 3 } };
var comparer = new Comparer<int[,]>();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='[0,1]', Value1='2', Value2='3'.*`

# 动态对象

C#支持几种类型的对象，这些对象的成员可以在运行时动态添加和移除。

# ExpandoObject

如果你不熟悉如何使用`ExpandoObject` 你可以阅读[这个](https://www.oreilly.com/learning/building-c-objects-dynamically)或者搜索另一个例子。

```
dynamic a1 = new ExpandoObject();
a1.Field1 = "A";
a1.Field2 = 5;
a1.Field4 = 4;
dynamic a2 = new ExpandoObject();
a2.Field1 = "B";
a2.Field3 = false;
a2.Field4 = "C";
var comparer = new Comparer();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Field1', Value1='A', Value2='B'.*`
> 
> `*Difference: DifferenceType=MissedMemberInSecondObject, MemberPath='Field2', Value1='5', Value2=''.*`
> 
> `*Difference: DifferenceType=TypeMismatch, MemberPath='Field4', Value1='4', Value2='C'.*`
> 
> `*Difference: DifferenceType=MissedMemberInFirstObject, MemberPath='Field3', Value1='', Value2='False'.*`

```
dynamic a1 = new ExpandoObject();
a1.Field1 = "A";
a1.Field2 = 5;
dynamic a2 = new ExpandoObject();
a2.Field1 = "B";
a2.Field3 = false;
var comparer = new Comparer();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Field1', Value1='A', Value2='B'.*`
> 
> `*Difference: DifferenceType=MissedMemberInSecondObject, MemberPath='Field2', Value1='5', Value2=''.*`
> 
> `*Difference: DifferenceType=MissedMemberInFirstObject, MemberPath='Field3', Value1='', Value2='False'.*`

如果成员不存在，可以通过提供自定义的`ComparisonSettings`来改变行为(参见下面的比较设置)。

```
dynamic a1 = new ExpandoObject();
a1.Field1 = "A";
a1.Field2 = 0;
dynamic a2 = new ExpandoObject();
a2.Field1 = "B";
a2.Field3 = false;
a2.Field4 = "S";
var comparer = new Comparer(new ComparisonSettings { UseDefaultIfMemberNotExist = true });
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Field1', Value1='A', Value2='B'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Field4', Value1='', Value2='S'.*`

# 动态对象

`DynamicObject`是抽象类，不能直接实例化。让我们假设我们有 DynamicObject 类的这样一个实现。正确实现方法`GetDynamicMemberNames`是必要的，否则对象比较器不会以正确的方式工作。

如果你不熟悉如何使用`DynamicObject` 你可以阅读[这个](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/types/walkthrough-creating-and-using-dynamic-objects)或者搜索另一个例子。

```
private class DynamicDictionary : DynamicObject
{
    public int IntProperty { get; set; }private readonly Dictionary<string, object> _dictionary = new Dictionary<string, object>();public override bool TryGetMember(GetMemberBinder binder, out object result)
    {
        var name = binder.Name;return _dictionary.TryGetValue(name, out result);
    }public override bool TrySetMember(SetMemberBinder binder, object value)
    {
        _dictionary[binder.Name] = value;return true;
    }public override IEnumerable<string> GetDynamicMemberNames()
    {
        return _dictionary.Keys;
    }
}dynamic a1 = new DynamicDictionary();
a1.Field1 = "A";
a1.Field3 = true;
dynamic a2 = new DynamicDictionary();
a2.Field1 = "B";
a2.Field2 = 8;
a2.Field3 = 1;
var comparer = new Comparer();
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Field1', Value1='A', Value2='B'.*`
> 
> `*Difference: DifferenceType=TypeMismatch, MemberPath='Field3', Value1='True', Value2='1'.*`
> 
> `*Difference: DifferenceType=MissedMemberInFirstObject, MemberPath='Field2', Value1='', Value2='8'.*`

# 编译器生成的对象

这种类型的动态对象最受欢迎，也最容易创建。

```
dynamic a1 = new
{
    Field1 = "A",
    Field2 = 5,
    Field3 = true
};
dynamic a2 = new
{
    Field1 = "B",
    Field2 = 8
};
var comparer = new Comparer();IEnumerable<Difference> differences;
var isEqual = comparer.Compare((object)a1, (object)a2, out differences);
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Field1', Value1='A', Value2='B'.*`
> 
> `*Difference: DifferenceType=TypeMismatch, MemberPath='Field2', Value1='5', Value2='8'.*`
> 
> `*Difference: DifferenceType=MissedMemberInSecondObject, MemberPath='Field3', Value1='True', Value2=''.*`

这个例子需要一些额外的解释。对象 a1 和 a2 的类型由编译器生成，并且当且仅当对象`a1`和`a2`具有相同的成员集(相同的名称和相同的类型)时被认为是相同的类型。如果跳过对**(对象)**的造型，在不同的成员集合中`RuntimeBinderException`将被抛出。

# 覆盖比较规则

有时，一些成员需要自定义比较逻辑。要覆盖比较规则，我们需要创建自定义值比较器或提供函数如何比较对象以及如何将这些对象转换为字符串(可选)和过滤函数(可选)。值比较器应该从`AbstractValueComparer`继承或者应该实现`IValueComparer`。

```
public class MyValueComparer: AbstractValueComparer<string>
{
    public override bool Compare(string obj1, string obj2, ComparisonSettings settings)
    {
        return obj1 == obj2; *//Implement comparison logic here*
    }
}
```

覆盖特定类型对象的比较规则。

```
*//Use MyComparer to compare all members of type string* 
comparer.AddComparerOverride<string>(new MyValueComparer());
comparer.AddComparerOverride(typeof(string), new MyValueComparer());
*//Use MyComparer to compare all members of type string except members which name starts with "Xyz"*
comparer.AddComparerOverride(typeof(string), new MyValueComparer(), member => !member.Name.StartsWith("Xyz"));
comparer.AddComparerOverride<string>(new MyValueComparer(), member => !member.Name.StartsWith("Xyz"));
```

覆盖特定成员(字段或属性)的比较规则。如果没有提供`toStringFunction`参数，对象将使用`ToString()`方法转换为字符串。

```
*//Use MyValueComparer to compare StringProperty of ClassA*
comparer.AddComparerOverride(() => new ClassA().StringProperty, new MyValueComparer());
comparer.AddComparerOverride(
    typeof(ClassA).GetTypeInfo().GetMember("StringProperty").First(),
    new MyValueComparer());
*//Compare StringProperty of ClassA by length. If length equal consider that values are equal*
comparer.AddComparerOverride(
    () => new ClassA().StringProperty,
    (s1, s2, parentSettings) => s1?.Length == s2?.Length,
    s => s?.ToString());
comparer.AddComparerOverride(
    () => new ClassA().StringProperty,
    (s1, s2, parentSettings) => s1?.Length == s2?.Length);
```

按名称覆盖特定成员(字段或属性)的比较规则。

```
*//Use MyValueComparer to compare all members with name equal to "StringProperty"*
comparer.AddComparerOverride("StringProperty", new MyValueComparer());
```

按类型覆盖的优先级最高，按成员覆盖和按成员名称覆盖的优先级最低。如果同一类型(按类型/按名称/按成员名称)的多个值比较器可以应用于同一个成员，则在比较期间将引发异常`AmbiguousComparerOverrideResolutionException`。

示例:

```
var a1 = new ClassA();
var a2 = new ClassA();
comparer.AddComparerOverride<string>(valueComparer1, member => member.Name.StartsWith("String"));
comparer.AddComparerOverride<string>(valueComparer2, member => member.Name.EndsWith("Property"));**var result = comparer.Compare(a1, a2);*//Exception here***
```

# 比较设置

比较器构造函数有一个可选的**设置**参数来配置比较的某些方面。

# 递归比较

默认情况下为 True。如果为真，所有不是基本类型、没有自定义比较规则和没有实现`ICompareble`的成员将使用与根对象相同的规则进行比较。

# EmptyAndNullEnumerablesEqual

默认情况下为 False。如果为真，则为空的可枚举数(数组、集合、列表等。)和空值将被视为相等的值。

# UseDefaultIfMemberNotExist

如果为 true 并且成员不存在，对象比较器将认为该成员等于相反成员类型的默认值。仅适用于动态类型比较。默认情况下为 False。

比较设置类允许存储可在自定义比较器中使用的自定义值。

```
SetCustomSetting<T>(T value, string key = null)
GetCustomSetting<T>(string key = null)
```

# 工厂

工厂提供了一种封装比较器创建和配置的方法。工厂应该实现`IComparersFactory` 或者应该继承`ComparersFactory`。

```
public class MyComparersFactory: ComparersFactory
{
    public override IComparer<T> GetObjectsComparer<T>(ComparisonSettings settings = null, IBaseComparer parentComparer = null)
    {
        if (typeof(T) == typeof(ClassA))
        {
            var comparer = new Comparer<ClassA>(settings, parentComparer, this);
            comparer.AddComparerOverride<Guid>(new MyCustomGuidComparer());return (IComparer<T>)comparer;
        }return base.GetObjectsComparer<T>(settings, parentComparer);
    }
}
```

# 非泛型比较器

此比较器为每个比较创建比较器的泛型实现。

```
var comparer = new Comparer();
var isEqual = comparer.Compare(a1, a2);
```

# 有用值比较器

框架包含几个自定义比较器，在许多情况下都很有用。

# 不要比较值比较器

允许跳过某些字段/类型。有单体实现(`DoNotCompareValueComparer.Instance`)。

# 动态值比较器

将比较规则作为函数接收。

# nullablestringsvaluecomparer

Null 和空字符串被视为相等的值。有单体实现(`NulableStringsValueComparer.Instance`)。

# 默认值值比较器

允许将指定类型的提供值和默认值视为相等的值(参见下面的示例 3)。

# IgnoreCaseStringsValueComparer

允许比较字符串时忽略大小写。有单独的实现(`IgnoreCaseStringsValueComparer.Instance`)。

# 尿液比较仪

允许比较 Uri 对象。

# 例子

还有一些更复杂的例子来说明如何使用对象比较器。

# 示例 1:预期消息

## 挑战

检查收到的消息是否与预期的消息相同。

## 问题

*   需要跳过`DateCreated`、`DateSent`和`DateReceived`属性
*   需要跳过自动生成的`Id`属性
*   需要跳过`Error`类的`Message`属性

## 解决办法

```
public class Error
{
    public int Id { get; set; }public string Messgae { get; set; }
}public class Message
{
    public string Id { get; set; }public DateTime DateCreated { get; set; }public DateTime DateSent { get; set; }public DateTime DateReceived { get; set; }public int MessageType { get; set; }public int Status { get; set; }public List<Error> Errors { get; set; }public override string ToString()
    {
        return $"Id:{Id}, Type:{MessageType}, Status:{Status}";
    }
}
```

正在配置比较器。

```
_comparer = new Comparer<Message>(
    new ComparisonSettings
    {
        *//Null and empty error lists are equal*
        EmptyAndNullEnumerablesEqual = true
    });
*//Do not compare Dates* 
_comparer.AddComparerOverride<DateTime>(DoNotCompareValueComparer.Instance);
*//Do not compare Id*
_comparer.AddComparerOverride(() => new Message().Id, DoNotCompareValueComparer.Instance);
*//Do not compare Message Text*
_comparer.AddComparerOverride(() => new Error().Messgae, DoNotCompareValueComparer.Instance);var expectedMessage = new Message
{
    MessageType = 1,
    Status = 0
};var actualMessage = new Message
{
    Id = "M12345",
    DateCreated = DateTime.Now,
    DateSent = DateTime.Now,
    DateReceived = DateTime.Now,
    MessageType = 1,
    Status = 0
};IEnumerable<Difference> differences;
var isEqual = _comparer.Compare(expectedMessage, actualMessage, out differences);
```

> `*Objects are equal*`

```
var expectedMessage = new Message
{
    MessageType = 1,
    Status = 1,
    Errors = new List<Error>
    {
        new Error { Id = 2 },
        new Error { Id = 7 }
    }
};var actualMessage = new Message
{
    Id = "M12345",
    DateCreated = DateTime.Now,
    DateSent = DateTime.Now,
    DateReceived = DateTime.Now,
    MessageType = 1,
    Status = 1,
    Errors = new List<Error>
    {
        new Error { Id = 2, Messgae = "Some error #2" },
        new Error { Id = 7, Messgae = "Some error #7" },
    }
};IEnumerable<Difference> differences;
var isEqual = _comparer.Compare(expectedMessage, actualMessage, out differences);
```

> `*Objects are equal*`

```
var expectedMessage = new Message
{
    MessageType = 1,
    Status = 1,
    Errors = new List<Error>
    {
        new Error { Id = 2, Messgae = "Some error #2" },
        new Error { Id = 8, Messgae = "Some error #8" }
    }
};var actualMessage = new Message
{
    Id = "M12345",
    DateCreated = DateTime.Now,
    DateSent = DateTime.Now,
    DateReceived = DateTime.Now,
    MessageType = 1,
    Status = 2,
    Errors = new List<Error>
    {
        new Error { Id = 2, Messgae = "Some error #2" },
        new Error { Id = 7, Messgae = "Some error #7" }
    }
};IEnumerable<Difference> differences;
var isEqual = _comparer.Compare(expectedMessage, actualMessage, out differences);
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Status', Value1='1', Value2='2'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Errors[1].Id', Value1='8', Value2='7'.*`

# 示例 2:人员比较

## 挑战

比较不同来源的人。

## 问题

*   `PhoneNumber`格式可以不同。例如:“111-555-8888”和“(111) 555 8888”
*   `MiddleName`可以存在于一个源中但不存在于另一个源中。只有当`MiddleName`在两个源中都有值时，比较它才有意义。
*   `PersonId`需要跳过的属性

## 解决办法

```
public class Person
{
    public Guid PersonId { get; set; }public string FirstName { get; set; }public string LastName { get; set; }public string MiddleName { get; set; }public string PhoneNumber { get; set; }public override string ToString()
    {
        return $"{FirstName} {MiddleName} {LastName} ({PhoneNumber})";
    }
}
```

电话号码可以有不同的格式。我们只比较数字吧。

```
public class PhoneNumberComparer: AbstractValueComparer<string>
{
    public override bool Compare(string obj1, string obj2, ComparisonSettings settings)
    {
        return ExtractDigits(obj1) == ExtractDigits(obj2);
    }private string ExtractDigits(string str)
    {
        return string.Join(
            string.Empty, 
            (str ?? string.Empty)
                .ToCharArray()
                .Where(char.IsDigit));
    }
}
```

工厂不允许我们每次需要创建比较器时都配置它。

```
public class MyComparersFactory: ComparersFactory
{
    public override IComparer<T> GetObjectsComparer<T>(ComparisonSettings settings = null, IBaseComparer parentComparer = null)
    {
        if (typeof(T) == typeof(Person))
        {
            var comparer = new Comparer<Person>(settings, parentComparer, this);
            *//Do not compare PersonId*
            comparer.AddComparerOverride<Guid>(DoNotCompareValueComparer.Instance);
            *//Sometimes MiddleName can be skipped. Compare only if property has value.*
            comparer.AddComparerOverride(
                () => new Person().MiddleName,
                (s1, s2, parentSettings) => string.IsNullOrWhiteSpace(s1) || string.IsNullOrWhiteSpace(s2) || s1 == s2);
            comparer.AddComparerOverride(
                () => new Person().PhoneNumber,
                new PhoneNumberComparer());return (IComparer<T>)comparer;
        }return base.GetObjectsComparer<T>(settings, parentComparer);
    }
}
```

正在配置比较器。

```
_factory = new MyComparersFactory();
_comparer = _factory.GetObjectsComparer<Person>();var person1 = new Person
{
    PersonId = Guid.NewGuid(),
    FirstName = "John",
    LastName = "Doe",
    MiddleName = "F",
    PhoneNumber = "111-555-8888"
};
var person2 = new Person
{
    PersonId = Guid.NewGuid(),
    FirstName = "John",
    LastName = "Doe",
    PhoneNumber = "(111) 555 8888"
};IEnumerable<Difference> differences;
var isEqual = _comparer.Compare(person1, person2, out differences);
```

> *对象相等*

```
var person1 = new Person
{
    PersonId = Guid.NewGuid(),
    FirstName = "Jack",
    LastName = "Doe",
    MiddleName = "F",
    PhoneNumber = "111-555-8888"
};
var person2 = new Person
{
    PersonId = Guid.NewGuid(),
    FirstName = "John",
    LastName = "Doe",
    MiddleName = "L",
    PhoneNumber = "222-555-9999"
};IEnumerable<Difference> differences;
var isEqual = _comparer.Compare(person1, person2, out differences);
```

> *差异:差异类型=值不匹配，成员路径= '名字'，值 1= '杰克'，值 2= '约翰'。*
> 
> *差异:差异类型=值不匹配，成员路径= '中间名'，值 1='F '，值 2='L '。*
> 
> *差异:差异类型=值不匹配，成员路径= '电话号码'，值 1 = ' 111–555–8888 '，值 2 = ' 222–555–9999 '。*

# 示例 3:比较 JSON 配置文件

## 挑战

需要找到一些具有不同设置的文件。Json.NET 用于反序列化 JSON 数据。

## 问题

*   URL 可以有也可以没有 http 前缀。
*   **数据压缩**默认情况下**关闭**
*   **智能模式 1…3** 默认禁用
*   **需要跳过 ConnectionString** 、 **Email** 和**通知**
*   如果 **ProcessTaskTimeout** 或 **TotalProcessTimeout** 设置被跳过，将使用默认值，因此如果在一个文件中设置不存在，而在另一个文件中该设置有默认值，实际上是相同的。

## 文件

设置 0

```
{
  "ConnectionString": "USER ID=superuser;PASSWORD=superpassword;DATA SOURCE=localhost:1111",
  "Email": {
    "Port": 25,
    "Host": "MyHost.com",
    "EmailAddress": "test@MyHost.com"
  },
  "Settings": {
    "DataCompression": "On",
    "DataSourceType": "MultiDataSource",
    "SomeUrl": "http://MyHost.com/VeryImportantData",
    "SomeOtherUrl": "http://MyHost.com/NotSoImportantData/",
    "CacheMode": "Memory",
    "MaxCacheSize": "1GB",
    "SuperModes": {
      "SmartMode1": "Enabled",
      "SmartMode2": "Disabled",
      "SmartMode3": "Enabled"
    }
  },
  "Timeouts": {
    "TotalProcessTimeout": 500,
    "ProcessTaskTimeout": 100
  },
  "BackupSettings": {
    "BackupIntervalUnit": "Day",
    "BackupInterval": 100
  },
  "Notifications": [
    {
      "Phone": "111-222-3333"
    },
    {
      "Phone": "111-222-4444"
    },
    {
      "EMail": "support@MyHost.com"
    }
  ],
  "Logging": {
    "Enabled": true,
    "Pattern": "Logs\\MyApplication.%data{yyyyMMdd}.log",
    "MaximumFileSize": "20MB",
    "Level": "ALL"
  }
}
```

设置 1

```
{
  "ConnectionString": "USER ID=admin;PASSWORD=*****;DATA SOURCE=localhost:22222",
  "Email": {
    "Port": 25,
    "Host": "MyHost.com",
    "EmailAddress": "test@MyHost.com"
  },
  "Settings": {
    "DataCompression": "On",
    "DataSourceType": "MultiDataSource",
    "SomeUrl": "MyHost.com/VeryImportantData",
    "SomeOtherUrl": "MyHost.com/NotSoImportantData/",
    "CacheMode": "Memory",
    "MaxCacheSize": "1GB",
    "SuperModes": {
      "SmartMode1": "enabled",
      "SmartMode3": "enabled"
    }
  },
  "BackupSettings": {
    "BackupIntervalUnit": "Day",
    "BackupInterval": 100
  },
  "Notifications": [
    {
      "Phone": "111-222-3333"
    },
    {
      "EMail": "support@MyHost.com"
    }
  ],
  "Logging": {
    "Enabled": true,
    "Pattern": "Logs\\MyApplication.%data{yyyyMMdd}.log",
    "MaximumFileSize": "20MB",
    "Level": "ALL"
  }
}
```

设置 2

```
var settings0Json = LoadJson("Settings0.json");
var settings0 = JsonConvert.DeserializeObject<ExpandoObject>(settings0Json);
var settings2Json = LoadJson("Settings2.json");
var settings2 = JsonConvert.DeserializeObject<ExpandoObject>(settings2Json);IEnumerable<Difference> differences;
var isEqual = _comparer.Compare(settings0, settings2, out differences);
```

> `*Difference: DifferenceType=ValueMismatch, MemberPath='Settings.DataCompression', Value1='On', Value2='Off'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Settings.SuperModes.SmartMode1', Value1='Enabled', Value2='Disabled'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Timeouts.ProcessTaskTimeout', Value1='100', Value2='200'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='BackupSettings.BackupIntervalUnit', Value1='Day', Value2='Week'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='BackupSettings.BackupInterval', Value1='100', Value2='2'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Logging.Enabled', Value1='True', Value2='False'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Logging.MaximumFileSize', Value1='20MB', Value2='40MB'.*`
> 
> `*Difference: DifferenceType=ValueMismatch, MemberPath='Logging.Level', Value1='ALL', Value2='ERROR'.*`

就是这样。享受吧。