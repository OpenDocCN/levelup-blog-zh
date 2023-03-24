# 抽象 vs 封装:你能很快看出区别吗？

> 原文：<https://levelup.gitconnected.com/abstraction-vs-encapsulation-can-you-quickly-tell-whats-the-difference-f676dfae1751>

## C#示例。

![](img/5e8e9a85671ce8e47907087008fee6ad.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@safarslife?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

封装和抽象是基本的编程概念。理解并定期应用它们可以极大地提高软件的质量，并使其更易于维护。然而，这两个术语经常互换使用，尽管它们之间有明显的界限。

我建议理解封装和抽象概念之间的区别，而不求助于任何维基百科的定义或聪明的词语，而是简单地使用小代码示例。

# 封装到底是什么？

封装有助于将对象的状态保持在**有效**和**一致**的状态。

```
public class Email
{
   public string EmailAddress { get; set; }
}
```

`Email`对象是否被正确封装？绝对不是。`Email` object 的消费者*(任何处理对象的代码)*不仅可以将`EmailAddress`属性设置为有效的电子邮件地址，还可以设置为 C#中的`string`类型允许的任何值:null、空字符串、‘qwerty’等。

这是一个问题，因为任何使用`Email`类型的人都必须总是验证`EmailAddress`属性，以确保总是有一个有效值。即使`Email`对象现在包含一个有效的电子邮件地址，也不能保证以后没有人会设置一个无效的值。

允许对象在创建时只初始化一次，这样就没有人能在对象创建后更改它的属性，这样如何？

```
public class Email
{
    public Email(string emailAddress)
    {
        EmailAddress = emailAddress;
    } public string EmailAddress { get; **private** set; }
}
```

setter 是私有的，初始化对象的唯一方法是向构造函数传递一个电子邮件地址。这比 public setter 要好，但是仍然没有实现封装。消费者仍然可以在对象创建期间设置无效的电子邮件值。

在我们的例子中，为了实现正确的封装，`Email`类型本身必须以正确和一致的状态维护其数据，如下例所示:

```
public class Email
{
    public Email(string emailAddress) 
    {
        if (!IsValid(emailAddress))
        {
           throw new ArgumentException("Email address is invalid.");
        }

        EmailAddress = emailAddress;
    } public string EmailAddress { get; private set; } private bool IsValid(string emailAddress)
    {
        var match = Regex.Match(
            emailAddress,
            "^\\S+@\\S+$",
            RegexOptions.IgnoreCase); return match.Success;
    }
}
```

> 封装完成！现在，不能在无效状态下创建对象，并且在对象的生存期内，外部代码不能将状态从有效更改为无效。

对象不一定要验证构造函数中的数据，如果数据是坏的就抛出异常。还有一些其他方法来维护对象的有效和一致状态，比如接受已经有效的数据，返回`true/false`或`Result<T>`而不是抛出异常，在方法中验证数据，等等。

# 抽象到底是什么？

抽象的原则是创建一个简单的接口并向消费者公开，并向他们隐藏不必要的细节。

```
public interface IJsonFileParser
{
    bool Exists(string filePath);
    Stream GetFileStream(string filePath);
    T Parse<T>(Stream stream);
    Dispose(Stream stream);
}
```

`IFileParser`接口可以被需要解析一些 JSON 文件内容到一个对象的消费者使用。然而，这个接口并不真正方便，因为消费者可能忘记调用`Exists`方法，或者将错误的`Stream`对象传递给`Parse<T>`方法，忘记调用`Dispose`方法，混淆调用顺序等等。

此外，该接口向消费者公开了文件解析的不必要的实现细节，比如关于`Stream`类型的知识。

该接口可能从消费者那里抽象出一些细节，比如打开文件、读取文件内容、将文件反序列化为对象。然而，这里有更多的空间进行适当的抽象，所以让我们改进一下`IJsonFileParser`接口:

```
public interface IJsonFileParser
{
    T Parse<T>(string filePath); 
}
```

现在只有一种方法`Parse<T>`是消费者应该处理的。在一个`Parse<T>`方法的实现中，旧的接口暴露的所有其他细节，比如检查文件的存在，创建/处理流，应该对消费者完全隐藏。这将大大简化消费者代码。

# 总结抽象和封装之间的区别

*   封装确保对象的状态总是有效和一致的。抽象确保消费者拥有尽可能简单的接口来处理对象。
*   抽象可以在不实现封装的情况下实现，反之亦然。
*   封装是一个定性单位*(对象被封装了吗？—是或否)*，而抽象是量化的*(界面使用起来有多容易？—容易，不那么容易，难，相当难……)。*

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](https://betterprogramming.pub/5-ways-to-implement-factory-design-pattern-in-c-382c0992a3ff) [## 在 C#中实现工厂设计模式的 5 种方法

### 静态、异步、参数化、内部和抽象工厂

better 编程. pub](https://betterprogramming.pub/5-ways-to-implement-factory-design-pattern-in-c-382c0992a3ff) 

还有，考虑成为[中等会员](https://esashamathews.medium.com/membership)。