# C++:带有 plog 和 fmt::格式库的现代日志记录器

> 原文：<https://levelup.gitconnected.com/modern-c-logging-with-plog-and-fmt-format-1397ab4e2350>

## C++模式

## 以最有效的方式使用文本日志和格式

![](img/0290c9ab65c3065e2a34df8f2c2f3fe7.png)

照片由[帕特里克·福尔](https://unsplash.com/@patrickian4?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

欢迎来到实用 C++解决方案第四期。如果你读过[以前的](https://medium.com/swlh/construct-variadic-flexible-and-scalable-interface-for-c-container-f1c5190afdd1) [三篇](https://medium.com/swlh/c-container-with-conditionally-protected-access-9d249393183e) [文章](https://medium.com/swlh/functional-minimalistic-and-useful-c-map-like-container-bac0db734c71)你肯定已经注意到我专注于容器设计。让我们暂时离开一下，谈谈大型软件项目中最重要的部分之一。它是软件的状态记录。为什么有必要？它允许出于安全原因或为了场景重建而跟踪用户的所有动作。如果设置了适当的跟踪级别，这对于 bug 跟踪非常有帮助。我认为即使是这些原因也足以让一个日志记录过程尽可能的快速和简单。

首先我要介绍两个牛逼的库: **fmtlib** 和 **plog** 。

[**plog**](https://github.com/SergiusTheBest/plog) 是一个小而强大的只支持头文件的日志库。它提供了一个极简的框架来创建定制的日志，所以你不需要重新发明一个轮子来寻找完美的日志工具。它是跨平台、线程安全、可移植的，只需查看其[文档](https://github.com/SergiusTheBest/plog)和[测试示例](https://github.com/SergiusTheBest/plog/tree/master/samples)。

另一方面， [**fmtlib**](https://github.com/fmtlib/fmt) 积累了一套用于字符串分配、格式化、连接和打印(包括 Pythonic 风格)的乐器。所有这些操作不仅实现了内存安全，而且是所有类似操作中最快的。不说让你的代码整洁、快速、漂亮的大量特性。所以，让[看看它](https://github.com/fmtlib/fmt)并检查[文档](https://fmt.dev/latest/api.html)中可用的 API。

所以，看起来这些库是为了一起使用而创建的。让我们仔细看看完美的共生！

## 1.准备容器

当然，日志记录和格式化不能脱离实际的例子而单独存在。出于演示的目的，c 我们将使用一个类似容器的框架(只是因为容器太棒了，我看不出有什么理由要避开它们)。让我们保持简单，并根据以下原则构建代码:

*   创建一个简约整洁的界面；
*   让我们的容器做一件非常简单的工作:只需保存数字并计算它们；
*   构建单个记录器实现。当然，我们可以提供 PIMPL 习语，但让我们保持简单；
*   保持记录器实现与容器分离。

好了，现在我们可以根据这些原则编写代码了:

如你所见——它简单而愚蠢，正如我们所希望的那样。现在我们可以专注于重要的事情。

## 2.记录器设计

下一步是记录器的设计。下面是(我认为)将展示这些库的巨大潜力的特性列表:

*   记录器实现基于容器类型，但是容器独立于记录器；
*   格式化程序和记录器本身是分离的，所以每个部分都可以独立更改；
*   记录器有自己的实例，独立于主程序(所以 plog 可以重复使用，如果需要)；
*   信息同时被载入文件和控制台。

## 3.一个伐木工，两个伐木工

现在我们必须根据前面的计划创建实现。 **plog** 的框架设计会帮助我们。如库所述，记录器由一个附加器、格式化器和转换器组成。Appender 将消息写入所需的源(控制台、存储、文件等)。).格式化程序根据传递的数据以我们需要的形式和格式创建消息。转换器提供了一个额外的格式化层(我们不会在我们的例子中使用它)。所以，让我们从阑尾开始。

实际上，我们将有两个 appenders:第一个用于登录文件，第二个用于登录控制台。方便的是，这些实现中的每一个都只需要一个带有单个方法重载的短模板。此外，我们将保持 **plog** 的标准，并在记录之前调用格式化程序。开始记录到文件中:

登录到控制台甚至更容易实现。此外，这也是使用 **fmtlib** 功能的绝佳场所——控制台的替代输出。如前所述——它是安全的，而且比标准的要快得多。

仅此而已，我们有工具将日志放入所需的源中。

## 4.更多模板

下一步是将记录器绑定到容器的数据类型。换句话说，我们将封装数据类型解析和格式化。之后，我们将使用这一行代码来创建日志。我们需要两个新模板:一个用于日志记录器理解容器的类型，另一个用于封装数据类型格式的 **fmtlib** 特性。让我们从格式化模板开始:

实际上，我们欺骗并添加了公共的`Format()`方法(和一些其他的特性)到我们的类中:

然而，这仅仅是为了简化的目的。在另一种情况下，我们必须向容器中添加许多公共方法，并将所有格式化逻辑移到新模板中。现在我们需要一行代码来创建一个基于容器的字符串:

```
MyPreciousContainer cont
auto str = fmt::format(FMT_STRING(“{}\n”), cont)
```

因此，我们在记录器的流操作符模板中使用这一行:

在这段代码之后，我们使用一行代码创建日志，如下所示:

```
MyPreciousContainer cont
PLOG_INFO << cont;
```

## 5.添加格式

实际上，我们已经实现了所有日志阶段所需的组件:将记录传递给记录器，将记录格式化为字符串，并将该字符串放入目的地。尽管添加更多细节(如记录时间、严重性等)会很有帮助。)并具有改变框架的能力。另一个 **plog** 特性将帮助我们——格式化程序。

这是一个简单的模板，在输出之前在日志字符串周围添加一些框架。

正如您所记得的，appender 中使用了`format`方法。

现在我们已经为测试做好了准备。

## 6.记录器已完成

最后一步是启动记录器并将实际的记录器调用添加到容器代码中:

我们得到了美丽的琴弦，比如:

```
2020–04–26, 13:41:40.875 INFO Instance : 1\. Element was deleted. Current size: 0\. Current operation number: 6.
```

所以，我们的任务完成了:container 现在有了 logger，它可以在不同的源中编写格式化的消息，支持 OOP 原则，并且可以很容易地被替换。

我对 C++和软件开发充满热情，在我的职业生涯中积累了许多有趣的技巧和解决方案。我想分享它们，这并不奇怪。这就是我写这一系列文章的原因。如果您对 C++和实用的解决方案感兴趣，欢迎来到我的 Github，这里有完整的工作示例。

[](https://github.com/Midvel/ModernLogging) [## 中级/现代日志

### plog 和 fmt::format 共生示例。通过在…上创建帐户，为中级/现代日志开发做出贡献

github.com](https://github.com/Midvel/ModernLogging) 

另外，确保你没有错过上一集:

[](https://medium.com/swlh/construct-variadic-flexible-and-scalable-interface-for-c-container-f1c5190afdd1) [## 为 C++容器构造可变的、灵活的和可扩展的接口

### 变量参数的打包和完美转发

medium.com](https://medium.com/swlh/construct-variadic-flexible-and-scalable-interface-for-c-container-f1c5190afdd1) 

请继续关注下一期 C++设计。在等待的时候，欢迎你分享你的经验。