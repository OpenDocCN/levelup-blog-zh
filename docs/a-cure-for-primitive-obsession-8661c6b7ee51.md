# 治愈原始痴迷的方法

> 原文：<https://levelup.gitconnected.com/a-cure-for-primitive-obsession-8661c6b7ee51>

![](img/a178434d35cf144f116b80e7e0928940.png)

# 什么是原始执念？

首先，原语是大多数语言中可用的基本数据类型。这些数据类型包括字符串、数字(int、floats)和布尔值。

原始痴迷是一种代码味道，其中原始数据类型被过度使用来表示您的数据模型。原语的问题在于它们非常通用。例如，一个字符串可以代表一个名字、一个地址，甚至一个 ID。为什么这是一个问题？

*   它们不能包含任何特定于模型的逻辑或行为，这意味着任何逻辑都必须存储在包含类中。这意味着你最终会得到包含许多不相关逻辑的类，这违反了单一责任原则。
*   它们失去了类型安全。字符串就是字符串。编译器不知道你传递给它的是一个代表名字还是地址的字符串。这意味着很容易意外地将一个值赋给错误的字段，代码将运行和编译良好，并且您可能直到数据完全混乱时才注意到。

# 治愈原始执念

下面是一个患有原始强迫症的人的例子:

```
public class Person
{
    public Person(string id, string firstName, string lastName, string address, string postcode, string city, string country)
    {
        // initialisation logic
    } public string Id { get; set; } public string FirstName { get; set; }
    public string LastName { get; set; } public string Address { get; set; }
    public string PostCode { get; set; }
    public string City { get; set; }
    public string Country { get; set; } public void ChangeAddress(string address, string postcode, string city, string country)
    {
        // change address logic
    }
}
```

这里出了什么问题？我们有一个完全由字符串属性组成的类。构造函数由一长串字符串参数组成——我可以保证，在某个时候，错误的值将被赋给错误的参数槽！我们也有一个方法来改变地址，但实际上这个逻辑不应该是 Person 类的责任。最后，ID 也是一个字符串，所以可能会意外地用作其他类型的 ID。

那么我们能做什么呢？

我们首先要看的是，有没有什么属性可以归为一类？一个很好的测试是询问，这些属性中的哪些可能会一起更新？在我们的例子中，很明显，地址、邮政编码、城市和国家字段应该分组(如果你搬家，很可能所有或大部分这些属性将一起更新)。让我们将这些属性重构到它们自己的类中。

```
public class Address
{
    public Address(string address, string postCode, string city, string country)
    {
        // initialisation logic
    } public string Address { get; set; }
    public string PostCode { get; set; }
    public string City { get; set; }
    public string Country { get; set; }
}public class Person
{
    public Person(string id, string firstName, string lastName, Address address)
    {
        // initialisation logic
    } public string Id { get; set; } public string FirstName { get; set; }
    public string LastName { get; set; } public Address Address { get; set; }
}
```

我们的人类已经看起来好多了。与地址相关的所有逻辑现在都封装在 address 类中，我们已经设法从构造函数中消除了许多字符串参数。

你可能已经注意到，总的来说，我们仍然有相同数量的字符串属性。这很好，因为最终很可能需要将数据存储在原语中。避免原语困扰的重要部分是将这些原语封装到定义良好的对象中，这些对象实际上代表了它们的含义。

我们需要处理的下一件事是 ID。为了将类型安全带回 ID，我们可以创建所谓的强类型 ID。简而言之，这只是包装在特定于该实体的容器对象中的原始值。例如，PersonId 对象的实现可能看起来像(改编自[这里是](https://andrewlock.net/using-strongly-typed-entity-ids-to-avoid-primitive-obsession-part-1/)):

```
public readonly struct PersonId : IComparable<PersonId>, IEquatable<PersonId>
{
    public string Value { get; }public PersonId(string value)
    {
        Value = value;
    }public static PersonId New() => new PersonId(Guid.NewGuid().ToString());public bool Equals(PersonId other) => this.Value.Equals(other.Value);
    public int CompareTo(PersonId other) => Value.CompareTo(other.Value);public override bool Equals(object obj)
    {
        if (ReferenceEquals(null, obj)) return false;
        return obj is PersonId other && Equals(other);
    } public override int GetHashCode() => Value.GetHashCode();
    public override string ToString() => Value.ToString(); public static bool operator ==(PersonId a, PersonId b) => a.CompareTo(b) == 0;
    public static bool operator !=(PersonId a, PersonId b) => !(a == b);
}public class Person
{
    public Person(PersonId id, string firstName, string lastName, Address address)
    {
        // initialisation logic
    } public PersonId Id { get; set; } public string FirstName { get; set; }
    public string LastName { get; set; } public Address Address { get; set; }
}
```

这很好，因为现在 Person 类使用自己的 PersonId 作为 Id。它不可能被意外地用作另一个类型的 ID，因为它有自己的强类型 ID。

但是，您可能已经注意到这个 PersonId 定义有点大，如果必须为您希望创建的每个强类型 Id 声明这个定义，会很麻烦。老实说，这个障碍可能是强类型 id 在 C#开发中仍然不常见的原因。

幸运的是，在 C# 9 中，我们有了新的记录类型，使得定义强类型 id 变得更加容易，所以希望它的使用会变得更加普遍(你可以在这里阅读更多关于记录[)。](https://samwalpole.com/exciting-new-features-in-net-5)

```
// PersonId as a record
public record PersonId(string Value);// how to initialise
var personId = new PersonId("my-id");
```

对，就是字面意思。这声明了一个 PersonId 记录，它有一个名为 Value 的字符串属性，可以通过构造函数传递。记录自动基于它们的属性值进行相等，因此不需要在声明中添加任何其他内容。

# F#中的原始执念

尽管本文主要关注的是 C#，并且上面应用的所有原则都可以在 F#中使用，但我认为 F#中有一个独特的特性值得注意。

区别联合是 F#中的一种数据类型(以及许多其他语言，但不是 C#)，它允许根据情况返回不同类型的数据(要了解更多关于区别联合的信息，请参见[这里](https://samwalpole.com/a-brief-introduction-to-f-for-object-oriented-developers))。一个常见的用例是错误处理:

```
type Result<'a> =
    | Data of 'a
    | Error of string
```

上面的例子允许一个函数返回一些通用的数据类型，但是如果有错误，返回一个字符串。

但是，单例鉴别联合可以充当基元类型的包装，并为您的函数提供类型安全。例如，我们可能有这个人的记录:

```
type Person = {
    FirstName: string;
    LastName: string;
    EmailAddress: string;
}
```

这里的问题是，电子邮件地址实际上不仅仅是一个字符串——它们有特定的格式，因此我们可能需要为它设计一些特定的验证逻辑。正因为如此，最好将其封装在自己的类型中。对于个案歧视工会，这很容易做到:

```
type EmailAddress = EmailAddress of stringtype Person = {
    FirstName: string;
    LastName: string;
    EmailAddres: EmailAddress;
}
```

现在，人员记录将只接受 email address 类型的电子邮件地址。

# 结论

在这篇文章中，我介绍了原始执念的概念，它可能会导致什么问题，以及如何解决这些问题。希望您可以从中提取一些信息，并在您自己的代码库中使用它们来创建更安全、更易维护的代码。

我发布的大部分内容都是关于全栈的。NET 和 Vue web 开发(很快可能会有更多的 F#内容！).为了确保你不会错过任何帖子，请关注这个博客并[订阅我的简讯](https://samwalpole.com)。如果你觉得这篇文章有帮助，请喜欢它并分享它。你也可以在[推特](https://twitter.com/dr_sam_walpole)上找到我。

【https://samwalpole.com】最初发表于[](https://samwalpole.com/a-cure-for-primitive-obsession)**。**