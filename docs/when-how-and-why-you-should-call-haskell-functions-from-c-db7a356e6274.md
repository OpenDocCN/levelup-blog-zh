# 如何从 C 中调用 Haskell 函数

> 原文：<https://levelup.gitconnected.com/when-how-and-why-you-should-call-haskell-functions-from-c-db7a356e6274>

![](img/d988132b069f20fc1a456c4e84a490a0.png)

将近 50 年后，C 编程语言仍然被认为是快速的低级代码选择之一。它是一种相对较小的语言，非常容易学习，广泛传播，而且大家都知道它很快。非常非常快。

C 的问题在于，由于语言的低级本质，一些算法可能变得难以编写、阅读和调试。有时候，用另一种语言编写这些算法可能是一个不错的选择。

有很多语言可以与 c 互操作。然而，有一种语言与 c 完全不同，但仍然是编写复杂算法的绝佳选择:Haskell。

事实上，Haskell 是一种纯函数式编程语言，与 C 相比有很多优势:

*   高级编程语言
*   否`null`
*   完全没有副作用
*   更模块化的代码
*   懒惰评估
*   更易于并行化

诸如此类。

就性能而言，在大多数情况下，Haskell 的执行速度非常接近 c。这使得 Haskell 成为更快编写复杂算法的好选择。

例如，假设我们想用两种语言实现快速排序算法。我们的 C 版本应该是这样的:

我们的 Haskell 版本:

好吧，也许这不是性能最好的 Haskell QuickSort 实现，但是你可以清楚地看到它是多么的简洁、容易和漂亮。

想再举个例子吗？让我们看看如何在两种语言中使用 Caesar 密码加密字符串。

使用 C #中的 Caesar 密码加密字符串:

在 Haskell 中使用 Caesar 密码加密字符串:

两种完全不同的方法。但是他们做同样的工作！现在让我们试着运行两者:

输出(在 MacBook Pro 2020 16 英寸、16GB 内存、2,6 GHz 6 核英特尔酷睿 i7 上):

哈斯克尔竞争相当激烈！如果你不熟悉语法，Haskell 一开始可能有点吓人。但是一旦你习惯了，你会发现 Haskell 程序比 C 程序更容易编写、理解和调试！

# 从 C 调用 Haskell

现在让我们举一个微不足道的例子(为了简单起见)。我们想在 Haskell 中创建一个简单的`fibonacci`函数，然后从 c 中调用它，首先:让我们写下我们的 Haskell 函数(取自 Haskell 官方 wiki):

难以置信的简单明了！现在我们可能想从 C 程序中调用它，如下所示:

我们应该如何编译我们的 Haskell 程序？首先，我们需要为 C 生成存根，以使它与 Haskell 类型一起工作:

如你所见，我们正在将`fibonacci_hs`参数从`CInt`转换为`Int`，以使其与我们原来的`fibonacci`函数兼容。然后我们将结果转换回`CInt`，使其与 c 兼容。最后但同样重要的是，我们使用`foreign export`导出我们的`fibonacci_hs`函数。

让我们编译我们的 Haskell 程序:

这将生成以下文件:

*   `main.hi`
*   `main.o`
*   `main_stub.h`

我们来看看`main_stub.h`的内容:

太好了！我们现在需要在 C 程序中包含这个头文件:

太好了！让我们用`gcc`编译这个文件并测试它:

厉害！您已经编译了您的第一个 Haskell-to-C 程序！

# 什么时候真正有用？

在很多情况下，将 Haskell 编译成 C 会非常有用。一个主要原因是当算法太复杂而不能用 c 语言编写时，Haskell 是一种高级的垃圾收集语言，所以大多数时候，编写算法会容易得多。

其他编程语言也可能出现同样的问题。有时候，用 Java、Go 和其他语言编写算法可能比用 Haskell 编写算法更复杂。用 C 编译你的函数意味着你可以从其他语言调用代码。事实上，许多编程语言可以与 C 互操作，用于低级任务或性能关键的算法。

将 Haskell 编译成 C 意味着您可以从以下语言中调用您的原始 Haskell 函数:

*   去
*   Java 语言(一种计算机语言，尤用于创建网站)
*   计算机编程语言
*   节点. js
*   二郎/仙丹

诸如此类。

那么…你准备好与 Haskell 互操作了吗？会很有趣的，我保证！您可以在这里找到包含代码示例[的存储库。](https://github.com/Hackdoor-io/articles-codebase/tree/master/articles/when-how-and-why-you-should-call-haskell-functions-from-c)

[![](img/e05f00ed2cddfd2907284cb397168c3d.png)](https://github.com/sponsors/micheleriva)