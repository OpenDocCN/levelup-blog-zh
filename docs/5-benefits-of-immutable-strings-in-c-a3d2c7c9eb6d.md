# C#中字符串不变性的 5 个好处

> 原文：<https://levelup.gitconnected.com/5-benefits-of-immutable-strings-in-c-a3d2c7c9eb6d>

![](img/00e8d125e95591bf5e9635e48b03f08d.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[记者](https://unsplash.com/@perloov?utm_source=medium&utm_medium=referral)拍摄

如果你试图在 C#中修改一个字符串对象，将会创建一个新的对象。原始字符串将保持不变。这种技术被称为**不变性**。像任何其他技术一样，不变性有它的优点和缺点。C#开发人员都知道这个缺点，通过用 StringBuilder 类型替换字符串可以减轻这个缺点。字符串不变性的优点更值得讨论，这是我们接下来 5 分钟的计划。

# 没有副作用

应用程序的不同部分可以依赖于单个对象。应用程序的一部分可以在某个条件语句中使用这个对象，根据对象的状态做出决定。应用程序的第二、第三和其他部分具有类似的逻辑。一切都会很顺利，直到有人决定改变物体的状态。很难预测这种变化将如何影响应用程序的不同部分。如果不进行深入研究，甚至很难确切知道哪些功能面临风险。

[](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) 

上述情况称为副作用。最小化应用程序中副作用风险的一种方法是使用不可变的数据类型，string 就是其中之一。代码的几个部分可以依赖于同一个 string 对象，但是它们不会受到修改字符串的影响，因为字符串是不可变的。任何试图改变字符串的人都会得到一个副本。

不变性剩下的四个好处只是“无副作用”好处的特例。然而，仅仅笼统地谈论副作用就像描述单一责任原则，却只字不提解决 SRP 问题的模式，如装饰者、中介状态、责任链等。

所以，我们继续。

# 即时克隆

当需要克隆某种引用数据类型的机制时，开发人员实现原型设计模式。根据克隆的类型(深层或浅层)、对象图的大小以及其他额外要求，克隆逻辑可以复杂、不复杂或相对简单。

然而，对于不可变的数据类型，克隆逻辑非常简单:**只需从克隆方法**返回“this”指针。

如果您使用 dotPeek 之类的工具检查 string 数据类型的实现，您会发现 Clone 方法的以下代码:

```
public sealed partial class String : IComparable, IEnumerable,...
{ 

    ... public object Clone()
    {
        return this;
    } ...}
```

以下是为字符串和其他不可变数据类型(包括自定义数据类型)实现克隆的意义。不需要分配内存，复制原始字符串，并返回对新副本的引用。

如果某个消费者需要一个克隆的字符串，它会获得对原始字符串对象的引用。如果消费者需要在只读模式下使用字符串对象，他可以这样做，因为这是安全的。如果使用者需要修改字符串，将创建副本，因此原始对象不受影响。

如果某个消费者需要一个克隆的字符串，它会获得对原始字符串对象的引用。如果使用者需要在只读模式下使用字符串对象，它可以这样做，因为它是安全的。只要消费者需要更改字符串，就会进行复制，因此原始字符串不会受到影响。

# 字符串实习

字符串滞留机制通过在内存中为几个相同的字符串创建一个对象来帮助减少内存消耗。

内存中不会有三个 string 对象，执行下面这段代码时只有一个:

```
string name1 = "Guy Ritchie";
string name2 = "Guy Ritchie";
string name3 = "Guy Ritchie";
```

编译器将检测相同的字符串文字，并生成在托管堆中分配单个盖·里奇字符串对象的指令，所有三个变量 name1、name2、name3 将指向同一个对象。

例如，如果代码试图用变量 name3 修改盖·里奇对象，将创建一个新对象，name3 将引用新创建的对象，而变量 name1 和 name2 将继续引用旧对象。

但是假设字符串是 C#中普通的可变对象。编译器仍然为多个相同的字符串创建一个对象，不同的变量引用同一个字符串对象。你认为这是一种预期的行为吗？

# 线程安全

当两个或多个线程同时修改同一个 string 对象时，不需要担心同步技术。不变性使得字符串线程安全，所以并发或死锁问题是不可能的。只要有几个线程处理同一个字符串对象，它就是完全安全的。

但是，如果多个线程修改同一个字符串对象，则不需要同步对该字符串的访问。每次尝试修改字符串对象都会导致创建一个新的对象，因此每个线程都将使用自己的字符串副本。

# 代码可读性

当代码处理不可变数据类型而不是可变数据类型时，它更容易阅读。

```
public void DoSomething()
{
    var person = new Person();
    person.Name = "Guy Ritchie";
    DoSomethingMore(person);
    Console.WriteLine(person.Name);
}
```

开发人员只能尝试预测将显示哪个名称。这个名字可能仍然是盖·里奇。但也可能是布拉德·皮特或其他名字，或者根本不是一个名字。通过阅读代码找出真正价值的唯一方法是更深入地研究嵌套方法调用的实现细节。

但是当开发人员阅读使用不可变数据类型编写的代码时，情况就不同了。

```
public void DoSomething()
{
   string name = "Guy Ritchie";
   DoSomethingMore(name);
   Console.WriteLine(name);
}
```

开发人员甚至不需要检查 DoSomethingMore 方法的实现细节就可以确定名称保持不变。这节省了大量学习现有代码行为的时间。

# 结论

记住，不变性不仅仅是关于字符串。不变性方法只在字符串和许多其他数据类型中实现。您还可以使您的自定义数据类型不可变，以获得我们讨论的所有好处。

# 关于可维护性的更多信息

[](https://medium.com/codex/how-dependency-inversion-and-inversion-of-control-help-to-build-maintainable-software-systems-df9b61b07c75) [## 依赖反转和控制反转如何帮助构建可维护的软件系统

### 对象是面向对象编程的基本概念之一。对象封装了一组数据…

medium.com](https://medium.com/codex/how-dependency-inversion-and-inversion-of-control-help-to-build-maintainable-software-systems-df9b61b07c75)