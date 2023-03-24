# 断言。应该约束 IsNotNull()

> 原文：<https://levelup.gitconnected.com/assert-isnotnull-should-be-constrained-3da531537b94>

![](img/88130bed723f42095d32b85a0c47e13f.png)

照片由[格雷格·拉科齐](https://unsplash.com/@grakozy)在 [Unsplash](https://unsplash.com/) 上拍摄

几天前，我在我目前从事的项目中看到了一些代码。我的一个同事写过这样的东西:

```
int x = GetSomeIntegerValue();
Assert.IsNotNull(x);
```

这个`int`和`NotNull`立刻引起了我的注意。

我考虑了一下，意识到这绝对不会产生一个`Exception`。

我问同事为什么写这个他说是为了测试`GetSomeIntegerValue()`不返回 0。

我向他解释如下:

*   `IsNotNull()`方法中的参数属于`Object`类型。
*   `int`是值类型。
*   当传递给需要一个`Object`的方法时，任何值类型都将被装箱到一个`Object` 中。
*   装箱的值类型永远不能是`null`。

然后他明白了为什么他的测试永远不会失败，并相应地改变了它**(旁注——总是确保你的测试可以通过也可以失败，否则，测试还有什么意义？).**

这是一个很好的例子，说明很好地理解较低层次的概念是多么重要，比如拳击。

# 它应该(或者说“可能”)是什么样子

该方法的全名是:

`Microsoft.VisualStudio.TestTools.UnitTesting.Assert.IsNotNull()`

方法签名是:

`public static void IsNotNull(Object value)`

我很好奇为什么微软没有使用以下方法将参数约束为引用类型:

`public static void IsNotNull<T>(T value) where T : class`

使用[重载](https://www.reddit.com/r/csharp/comments/m6kiyl/assertisnotnull_should_be_constrained/gr7v37d/?context=8&depth=9)来允许可空值类型:

`public void IsNotNull<T>(T? v) where T : struct`

所以我在 StackOverflow.com 上发了一个[问题](https://stackoverflow.com/questions/32555937/assert-isnotnull-should-constrain-parameter-to-class)

简而言之，微软没有使用参数约束的原因是因为泛型在 C# 1.0 中并不存在

如果他们把它改成使用泛型和参数约束，就会破坏用 C# 1.0 编写的代码。

这只是你必须忍受的事情之一，因为这不是世界末日，也不会破坏旧代码。只要你保持对幕后发生的事情的了解，你就会是对的。

**更新:**自从我写了这篇文章，我就被告知答案是使用流畅的断言(fluentassertions.com)，当我有时间的时候，我必须看看这个。

如果参数是值类型，则使用约束的替代方法是测试失败:

`if(typeof(obj).IsValueType) /* fail the test… */`