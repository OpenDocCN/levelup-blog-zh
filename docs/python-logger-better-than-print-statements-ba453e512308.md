# Python Logger:比打印语句更好

> 原文：<https://levelup.gitconnected.com/python-logger-better-than-print-statements-ba453e512308>

![](img/49a30fd687ce6264d648f7f1a7ee26d5.png)

伯纳德·赫尔曼特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为软件工程师，在学习一门新的编程语言时，我们首先要学习的是如何在控制台中打印出“Hello World”。

几个月后，我们已经非常熟悉这种语言，并且知道它的复杂性和不同的约定，我们仍然做的一件事是在调试时将东西“打印”到控制台上。

当我们对任何事情都不确定时，很容易就抛出一份书面声明。太方便了！这就是为什么我们从来不冒险走出它，看看我们可以采取什么其他方法来开始我们的调试过程。

我要告诉你的是，大多数语言都有某种日志库，信息丰富得多，而且几乎一样方便。具体来说，在这篇文章里，我会讲 Python 的`logging`模块。

我们先来看看 Python 的`logging`模块的基础知识。之后，我将把它与`print`进行比较，这将清楚地表明为什么日志记录是一种比用 Python 打印好得多的调试方式。

# 基本原则

你需要做的第一件事是导入 Python 的`logging`模块。

```
import logging
```

日志记录最基本的思想是“严重性级别”的思想。一些严重级别包括:

*   调试
*   信息
*   警告
*   错误
*   批评的

让我们看几个用例来了解如何区分这些级别:

*   您可以在开发时使用`DEBUG`语句来记录不同的变量值或您认为在开发时可能需要的任何其他信息。
*   您可以使用`INFO`语句来记录发生的任何事件。例如，传入的请求、按钮点击、正在发送的电子邮件等。
*   `WARNING`应用于任何可能导致您所知道的潜在错误的事件。
*   `ERROR`应该用于处理应用程序中出现的显式错误，即使应用程序运行正常。有一种简单的方法可以记录回溯而不会崩溃。
*   `CRITICAL`应该在你的应用即将失败的时候使用。一些例子是，超过磁盘使用率或您的 p50 计时持续超过您的服务 SLO。

使用不同的严重性级别进行日志记录非常容易！

```
logging.debug("This is a debug message")
logging.info("This is an info message")
logging.warning("This is a warning message")
logging.error("This is an error message")
logging.critical("This is a critical message")
```

默认情况下，日志模块记录严重级别高于`WARNING`的消息。这可以很容易地配置。

```
# Show all messages with severity levels including and above "DEBUG"
logging.baseConfig(level=logging.DEBUG)
```

既然我们已经知道了如何在控制台中记录消息，那么让我们看看可以配置它来满足我们的记录需求的多种方法。

# 配置

有多种方法可以配置您的日志记录器，使其提供更多信息和帮助。正如我们已经看到的，您可以配置的第一件事是您想要记录的严重性级别。

下一个配置是日志消息的格式。让我们看看默认格式:

```
logging.critical("This is a critical message")# OUTPUT
# CRITICAL:root:This is a formatted log
```

所以默认情况下，日志消息的格式是`<level>:<name>:<message>`。假设你想改变格式。超级简单！

```
logging.basicConfig(format='%(name)s - %(levelname)s - %(message)s')# New OUTPUT
# root - CRTICAL - This is a formatted log
```

您不仅限于使用这些信息。这就是日志的力量超越基本打印的地方。通过日志记录，您可以获得更多的信息。您可以免费获得的一些信息包括:

*   日志的时间戳
*   异常发生后记录的异常回溯
*   触发日志的文件名
*   触发日志的函数名
*   触发日志的行号
*   日志发生的进程的进程 ID

你可以免费获得所有这些信息！

让我们看看如何格式化我们的日志消息，以包括这些信息中的一些:

```
logging.basicConfig(format='%(asctime)s || %(levelname)s || %(message)s || %(lineno)d || %(process)d')
logging.warning('This is a Warning')# OUTPUT
# 2020-11-24 13:22:55,958 || WARNING || This is a Warning || 25 || 62244
```

如您所见，我们可以从一个简单的日志中获得更多的信息。我们必须设置这个配置一次，然后我们就可以免费获得这些信息。

# 将日志写入文件

到目前为止，我们只看到了如何将这些日志写入控制台。伐木给了我们更多的力量。我们可以写入文件而不是控制台。这使我们能够搜索、过滤甚至使用一些第三方软件来分析这些日志文件。

```
logging.basicConfig(filename='app.log', filemode='w')
logging.warning('This is a Warning')
```

这将在程序每次运行时创建一个新的日志文件。

如果您想保留旧的日志，您可以将文件模式更改为`a`,新的日志每次都会附加到文件的末尾。

你不能仅仅通过`print`获得这种力量，除非你做一些先进的管道。

# 记录回溯

如果我们遇到了预期的异常，那么默默失败是一种非常常见的做法。在这些情况下，我们使用`try-except`。如果你在`except`块中登录，你可以在你的日志中免费获得回溯，而不需要大声失败。

```
a = 10
b = 0
try:
	c = a / b
except Exception as e:
	logging.exception(f'Divide {a} by 0')
```

这差不多是开始使用 Python 登录所需的所有基础知识。现在让我们做一个回顾，看看我们从记录打印中得到什么好处。

# 记录相对于打印的优势

1.  更一致的日志消息
2.  可以轻松地写入文件
3.  让我们对这些日志文件进行搜索和过滤
4.  只需配置一次日志消息格式，就可以加载额外的元数据
5.  我们可以根据严重性对日志进行分类
6.  为了更好地监控，在部署代码后继续获取日志

如您所见，日志记录为我们提供了一种更加结构化的调试方式。它不仅有助于调试，而且我们的日志语句也可以保留在生产中，我们可以使用第三方软件与它们集成，让我们进行搜索、过滤和分析。

谢谢你一直读到最后。通过将这种技术添加到您的技能中，我希望您能成为一名更好的程序员，并减少令人沮丧的调试会话。