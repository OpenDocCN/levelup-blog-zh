# 大幅提高代码可读性的简单技术

> 原文：<https://levelup.gitconnected.com/simple-techniques-to-drastically-improve-code-readability-5a84d98fa2c1>

## 如何缓解阅读源代码时的上下文切换问题？

![](img/2c544195c7c5fc8110d5c64b3fd6c18f.png)

Emile Perron 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我敢打赌，如果我说开发人员大部分时间都在阅读源代码，而不是编写源代码，我不会让你感到惊讶。软件开发人员经常出于各种目的阅读代码，其中一些目的是:

*   在用新行为扩展代码之前，先对代码进行调查。
*   找到缺陷的根本原因。
*   调查重用现有功能的能力。
*   查看拉请求中的代码。

代码调查过程通常是什么样的？开发人员开始阅读他感兴趣的方法`A`的代码。然而，方法`A`调用方法`B`，后者又调用方法`C`，以此类推。

开发人员可能需要导航到链中的每个方法，以至少读取它们的签名，或者在最坏的情况下，读取它们的实现。在完成嵌套方法的分析后，开发人员返回到方法`A`并继续探索它的代码。

这里的问题是，开发人员每次导航到嵌套方法并返回到父方法时，都需要**切换和恢复上下文**。在大型代码库中，这项工作可能会很困难，有一些简单的技术可以使这项任务变得更容易。

# 命名参数

使用方法参数的目的之一是能够在运行时根据参数值改变方法的行为。

## 严重的

例如，可能有一个`FileParsingResult Parse(bool includeMetadata)`方法。传递`true`将导致该方法在`FileParsingResult`对象中包含一些额外的元数据。

```
public void DoSomething()
{
    FileParsingResult parser = new FileParser();
    var file = parser.Parse(**true**);
}
```

仅仅通过阅读`DoSomething`方法，并不清楚`true`参数实际上是什么意思。开发人员必须跳到`Parse`方法定义中，至少要读取参数名。当用 IDE 阅读代码时，这可能不是问题，IDE 可以通过简单地将鼠标悬停在`true`文字或方法名上来显示参数名。

然而，对于那些以纯文本形式阅读代码的人来说，这将带来不便，这通常是代码审查的情况。

## 较好的

```
public void DoSmth()
{
    var parser = new FileParser();
    var file = parser.Parse(**includeMetadata**: true);
}
```

选择自描述的方法参数名和使用命名参数特性在许多情况下可以防止开发人员在源代码的不同部分之间切换。

# 价值对象

值对象是一个包装了一个或多个原始类型的对象，用来表示某个域的一些概念。使用值对象的一大好处是它们可以极大地提高整个代码库的可读性。

## 严重的

```
public interface IUserService
{
    IDictionary<string, string> GetUserBankAccounts();
}
```

如果不深入研究`GetUserBankAccounts`方法的实现细节，很难从应用领域的角度理解什么是`string`键和`string`值。

## 较好的

```
public interface IUserService
{
  IDictionary<UserEmail, BankAccountNumber> GetUserBankAccounts();
}public class UserEmail
{
  public string Email { get; set; }
  //Remaining implementation of Value Object is missing
}public class BankAccountNumber
{
  public string Number { get; set; }
  //Remaining implementation of Value Object is missing
}
```

除了明显的可读性好处之外，正确实现的值对象还可以封装业务规则并减少副作用的机会。

# 冗余接口

接口是面向对象编程的基本特征之一。尽管接口有很多优点，但它们的缺点是降低了代码的可读性。

这并不意味着根本不应该使用接口。当运行时需要替换多个实现时，或者当需要隔离测试某个类的行为时，应该使用接口。但是，不应该盲目地为任何类提取接口。

## 严重的

```
public class EmailValidator
{
   private readonly INullOrEmptyValidator _nullOrEmptyValidator; public EmailValidator(INullOrEmptyValidator nullOrEmptyValidator) 
       => _nullOrEmptyValidator = nullOrEmptyValidator; public bool Valid(string email)
   {
       bool valid = _nullOrEmptyValidator.Valid(email);
       if (!valid)
       {
           return false;
       } //Remaining validation rules
    }
}
```

开发人员需要去实现`IsNullOrEmptyValidator`接口，以准确理解它的行为。此外，这个接口污染了`EmailValidator`类的代码。

## 较好的

```
public class EmailValidator
{
   public bool Valid(string email)
   {
       if (string.IsNullOrEmpty(email))
       {
           return false;
       } //Remaining validation rules
    }
}
```

事实上，根本不需要`IsNullOrEmptyValidator`接口及其实现，因为它包含相当简单的逻辑，可以简单地放入外部类中。在不牺牲代码质量的情况下，`EmailValidator`的可读性得到了简化。

# 扩展方法

通过扩展方法，开发人员可以将复杂的编程表达式转换成每个人都可以像读小说一样阅读的代码。

## 严重的

```
public bool Validate(List<int> ids)
{
    List<int> allIds = GetIds(); bool valid = ids.All(id => allIds.Contains(id));
    return valid;
}
```

表达式`ids.All(id => allIds.Contains(id))`是一个 LINQ 表达式，有经验的开发人员可以轻松阅读，但即使是他们也要花几秒钟来解析包含 lambda 表达式和嵌套方法调用的这一行，以确保他们理解正确。这又是一次上下文切换，尽管不需要跳转到嵌套方法。

## 较好的

```
public static bool Validate(List<int> ids)
{
    List<int> allIds = GetIds(); bool valid = ids.IsSubsetOf(allIds);
    return valid;
}public static class Extensions
{
    public static bool IsSubsetOf<TSource>(
       this IEnumerable<TSource> sourceElements,
       IEnumerable<TSource> values)
    {
        bool all = sourceElements
          .All(sourceElement => values.Contains(sourceElement)); return all;
    }
}
```

`Validate`方法现在非常容易阅读。此外，`ContainsEveryElementFrom`方法可以在整个应用程序代码库中重用。

# 结论

在大型代码库中无止境的地方实现小的改进，可以大大减少开发人员花在分析代码上的时间。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) [## 5 种免费提高 C#代码性能的方法

### 慢速代码是可选的。

levelup.gitconnected.com](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da)