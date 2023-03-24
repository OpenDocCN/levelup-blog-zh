# Python 日志教程

> 原文：<https://levelup.gitconnected.com/tutorial-on-python-logging-ac5f21e0a00>

## Python 日志和代码片段供您立即使用

日志对于程序员来说是一个非常重要的功能。对于调试和显示运行时信息，日志记录同样有用。在本文中，我将介绍为什么以及如何在程序中使用 python 的日志模块。

![](img/5c63acf00277c662fb96b2ff7fafea0e.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/codes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 为什么记录而不打印()

打印语句和日志输出之间有一个关键的区别。通常，print 语句会写入到 **stdout** (标准输出)中，这应该是有用的信息或程序的输出。但是，日志被写入 **stderr** (标准错误)。我们可以如下演示这个场景。

```
import logginglogging.basicConfig(level=logging.INFO) **#We'll talk about this soon!**logging.warning('Something bad could happen!')
logging.info('You are running the program')
logging.error('Aw snap! Everything failed.')print("This is the program output")
```

现在，如果我运行这个程序，我将在命令行中看到以下内容。

```
$ python log_test.py
WARNING:root:Something bad could happen!
INFO:root:You are running the program
ERROR:root:Aw snap! Everything failed.
This is the program output
```

但是，对于通常的用户来说，这些信息太多了。虽然这实际上是在命令行中一起显示的，但是数据被写入两个独立的流中。所以一个典型的用户应该这样做。

```
$ python log_test.py > program_output.txt
WARNING:root:Something bad could happen!
INFO:root:You are running the program
ERROR:root:Aw snap! Everything failed.$ cat program_output.txt
This is the program output
```

在这里，有用的程序输出通过重定向`>`写入文件。因此，我们可以看到终端上发生了什么，并方便地将输出保存在一个文件中。现在让我们试着理解日志级别！

# 日志记录和日志级别

![](img/084923a06907798fe7caeb08eee48c40.png)

Edvard Alexander lvaag 在 [Unsplash](https://unsplash.com/s/photos/hierarchy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

日志记录可能出于不同的原因。这些原因分为以下严重程度。

*   **调试**:为开发者提供的调试信息，如计算值、估计参数、URL、API 调用等。
*   **信息**:信息，没什么大事。
*   **警告**:对用户输入、参数等的警告。
*   **错误**:报告用户在程序中做的或发生的事情引起的错误。
*   **临界**:最高优先级日志输出。用于关键问题(取决于用例)。

最常见的日志类型有**调试**、**信息**和**错误**。然而，您很容易会遇到 python 抛出版本不匹配警告的情况。

# 配置记录器和日志处理程序

记录器可以在不同的参数下进行配置。记录器可以配置为遵循特定的日志级别、文件名、文件模式和格式来打印日志输出。

![](img/ceec27cdbe2060e46ef8a0db8f558a19.png)

图片由 [2427999](https://pixabay.com/users/2427999-2427999/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3714727) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3714727)

## 配置记录器参数

记录器可配置如下。

```
import logging

logging.basicConfig(filename='program.log', filemode='w', level=logging.DEBUG)
logging.warning('You are given a warning!')
```

上述设置要求记录器将日志输出到名为`program.log`的文件中。`filemode=’w’`定义了写入文件的性质。例如，`'w'`打开一个新文件，覆盖原有的内容。默认情况下，该参数为`'a'`，它将以追加模式打开日志文件。有时，拥有日志历史是很有用的。level 参数定义日志记录的最低严重性。例如，如果将此项设置为**信息**，则**调试**日志将不会被打印。你可能已经看到程序需要在`verbose=debug`模式下运行才能看到一些参数。默认情况下，级别为**信息**。

## 创建日志处理程序

虽然上面的方法对于简单的应用程序来说很简单，但是对于生产就绪的软件或服务来说，我们需要一个全面的日志记录过程。这是因为可能很难在数百万个**调试**日志中找到特定的**错误**日志。此外，我们需要在整个程序和模块中使用一个日志记录器。这样，我们可以正确地将日志附加到同一个文件中。为此，我们可以为这个任务使用不同配置的处理程序。

```
import logginglogger = logging.getLogger("My Logger")
logger.setLevel(logging.DEBUG)console_handler = logging.StreamHandler()
file_handler = logging.FileHandler('file.log', mode='w')
console_handler.setLevel(logging.INFO)
file_handler.setLevel(logging.DEBUG)logger.addHandler(console_handler)
logger.addHandler(file_handler)
```

您可以看到，我们首先让一个记录器传递一个名称。这使我们能够在程序的任何地方重用同一个日志记录器。我们将全局日志级别设置为 **DEBUG** 。这是最低的日志级别，因此使我们能够在其他处理程序中使用任何日志级别。

接下来，我们为**控制台**和**文件**的写入创建两个处理程序。对于每个处理程序，我们提供一个日志级别。这有助于减少控制台输出的开销，并将它们转移到文件处理程序。使以后处理调试变得容易。

# 格式化日志输出

记录不仅仅是打印我们自己的信息。有时我们需要打印其他信息，如时间、日志级别和进程 id。对于这个任务，我们可以使用日志格式。让我们看看下面的代码。

```
console_format = logging.Formatter('%(name)s - %(levelname)s - %(message)s')
file_format = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')console_handler.setFormatter(console_format)
file_handler.setFormatter(file_format)
```

在我们向日志记录器添加处理程序之前，我们可以像上面那样格式化日志输出。为此，您可以使用更多的参数。你可以在这里找到所有的[。](https://docs.python.org/3/library/logging.html#logrecord-attributes)

# 可重复使用的代码

下面是我在许多应用程序中继续使用的日志代码片段。我想这可能对你这个读者有用。

![](img/0fc77bc1cc9775aeb037a355e84218e0.png)

图片由[布鲁诺/德](https://pixabay.com/users/Bru-nO-1161770/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1006172)来自[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1006172)

## 使用多线程登录

日志模块的设计考虑到了线程安全。因此，当您从不同的线程登录时，除了极少数例外(超出了本文的主要范围)，不需要任何特殊的操作。

我希望这是一篇简单但对许多初露头角的程序员和工程师有用的文章。下面是一些你可能想看的 python 教程。快乐阅读。干杯！:-)

[](/python-decorators-b530bff0f3e3) [## Python 装饰者

### 关于如何使用 python decorator 语法进行更简洁编码的教程

levelup.gitconnected.com](/python-decorators-b530bff0f3e3) [](https://towardsdatascience.com/python-generators-393455aa48a3) [## Python 生成器

### 使用 yield 关键字开发 python 生成器函数的教程

towardsdatascience.com](https://towardsdatascience.com/python-generators-393455aa48a3)