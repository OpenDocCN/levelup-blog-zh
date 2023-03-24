# 瞭望塔——简化 Amazon Cloudwatch 和 Python 应用程序日志的缺失部分

> 原文：<https://levelup.gitconnected.com/watchtower-the-missing-piece-in-streamlining-amazon-cloudwatch-and-application-logs-d18f990b133f>

![](img/d755f5d15859accdb811d4bebafecd9a.png)

[Vino Li](https://unsplash.com/@vinomamba24?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我的假设是，如果有一件事是我们所有数据人员都同意的，那就是日志对于任何数据应用程序都是至关重要的。如果设置正确，日志可以揭示关于应用程序操作的许多信息。如果应用程序运行正常，日志是确认其运行情况以及是否按预期运行的首选资源。如果遇到任何问题或异常，日志会提供很多关于哪里出了问题的见解。正是由于这一点以及其他各种优点，日志记录策略是数据应用程序操作化的关键考虑因素之一。

如果你对[亚马逊网络服务](https://aws.amazon.com/) (AWS)中的多功能和不断增长的服务有所了解，你可能知道 Cloudwatch 是那里的去因子日志服务。虽然 Cloudwatch 不仅仅是一个日志服务，但该服务的关键特性之一是存储和提供对日志的访问。作为 AWS 生态系统的一部分，它与其他服务紧密集成，许多(如果不是全部)服务将它们的日志发送到 Cloudwatch。同样，您也可以将自己应用的遥测/日志发送到 Cloudwatch。一旦日志出现在 Cloudwatch 中，就可以进一步利用它们根据定义的规则设置警报，从日志中生成指标，然后可以进一步分析这些指标以收集见解(例如，使用 Cloudwatch 的内置可视化和查询功能—告诉你，它不仅仅是一个简单的日志记录服务)。

所有这些在纸面上看起来都不错，但是当涉及到确定应用程序的日志并将其发送到 Cloudwatch 时，也有很多问题。如果您正在使用 Python，您可能已经投入了大量资金来调整其传奇的“日志”模块以满足您的需求，方法是定义您希望将日志发送到哪里(例如，发送到文件、标准输出)以及这些日志应该如何格式化。另一方面，如果您想将日志发送到 Cloudwatch，也有许多选项可供您选择:

1.  设置 Cloudwatch 代理—这包括在您的操作系统中安装 Cloudwatch 代理，定义它的配置以指定它应该监视哪些日志文件。一旦完成，它将开始向 Cloudwatch 发送日志文件的内容。因此，如果您已经在应用程序中按照自己的喜好设置了 Python 的日志模块，并且日志已经被写入系统中的特定位置，那么您可以指示代理将这些日志文件的内容流式传输到 Cloudwatch。使用基于代理的方法的一个很酷的好处是，您还可以通过在代理的配置(表示为 JSON)中添加几行配置，向 Cloudwatch 发送有趣的指标，例如 CPU 和内存利用率。如果除了代理发送的日志之外，您还想监视其他资源，这将非常方便。
2.  使用 boto3——Python 中与 AWS 的任何编程交互都以 boto3 开始(和结束), boto 3 是用于 Python 的 AWS SDK。使用 boto3，您可以定义 Cloudwatch 日志的客户端，并使用可用的函数将日志发送到那里。这种方法肯定是可行的，并且考虑到它是程序化的，它赋予了高度的灵活性。但是，为了能够向 Cloudwatch 发送日志事件，仍然需要遵循一些步骤。例如，考虑下面的代码片段，它利用 boto3 向 Cloudwatch 发送日志:

上述代码片段的要点如下:

*   在第 4 行创建一个使用 boto3.session.Session 对象的 Cloudwatch 日志客户端。
*   首先在第 8 行描述 Cloudwatch 日志流。这将返回一个 JSON 对象，其中包含关于日志流的元数据。这是必需的，因为您需要在下一次调用 put_log_events 函数时包含响应中指定的 SequenceToken。如果要将日志发送到新的日志流，但要将更多日志发送到现有日志流，则不需要提供有效的 SequenceToken。
*   第 13 到 21 行创建一个合格对象，然后作为关键字参数提供给 log_put_events 函数。

正如您在上面的例子中可能观察到的，向 Cloudwatch 发送单个日志事件确实需要几个步骤。当然，你可以有一个包装器函数，在这里或那里抽象出很多东西，但仍然会有一些开销。最后，它也将与您使用日志模块记录消息隔离开来。

如果有一种方法，您可以只使用日志记录模块，它还应该负责将日志发送到 Cloudwatch，以及将日志发送到您配置的处理程序，那会怎么样？(例如文件、标准输出)利用配置(例如格式化程序)。这就是*了望塔*发挥作用的地方，它充当 Cloudwatch 和 Python 的日志模块之间的适配器。

从技术上讲，PyPi 上有一个 [Python 模块](https://pypi.org/project/watchtower/),您可以很容易地将它集成到您现有的数据应用程序中。开始使用 watchtower 非常简单，几乎不需要额外配置，就可以让您的应用程序在无缝使用日志记录模块的同时向 Cloudwatch 发送日志。

首先，将它安装在您的 Python 环境中(如果您喜欢虚拟环境，也可以使用 venv):

```
pip install watchtower
```

安装后，您应该能够在 Python 脚本中导入瞭望塔。

在此之前，让我们考虑一个演示使用日志模块的基本示例:

虽然代码是不言自明的，但这里有几个要点:

*   导入日志模块后，我们实例化一个 logger 对象(使用 *logging.getLogger* 方法)
*   然后，我们将日志级别设置为 INFO。如果您还不知道日志记录模块中的其他日志记录级别，最好自己了解一下
*   然后，我们向日志记录器添加一个处理程序，它将定义日志将被路由到哪里。日志模块中有许多本机可用的处理程序，例如 StreamHandler(它将输出发送到流，如 sys.stdout、sys.stderr 或任何类似文件的对象)，FileHandler(它将输出发送到磁盘上的文件)。(*提示:那是了望塔将自己插入日志模块的地方，稍后会解释)*
*   然后我们通过 LOGGER.info()方法发送日志消息。在我们的示例中，我们刚刚配置了一个处理程序，即 StreamHandler，因此它将显示在您的控制台输出/屏幕上。

现在，要将日志消息也发送到 Cloudwatch，可以方便地实现如下操作:

*   您将瞭望塔导入到您的脚本中
*   瞭望塔模块定义了一个名为 CloudWatchHandler 的处理程序。当配置并添加到您的 Logger 对象时，它将负责将日志发送到 Cloudwatch。它接受一些在[文档](https://watchtower.readthedocs.io/en/latest/#module-watchtower)中突出显示的参数。我使用了最少的参数，即日志应该发送到哪个日志组和日志流。
*   配置完成后，您可以将其添加到 logger 对象中。完成后，您通过 logger 对象生成的每个日志消息，例如

```
LOGGER.info("some message")
```

此类消息将被发送到您现有的处理程序(如 StreamHandler)以及 Cloudwatch 和❤

下面是完成此任务的代码片段的完整视图:

如前所述，上述示例是一个最简单的示例，并没有突出与日志记录相关的许多概念，例如格式化程序、设置不同的日志程序而不仅仅是使用 root 等。但关键的想法是，所有这些概念都与了望塔协同工作。

一些号召:

*   瞭望塔。CloudWatchHandler 还接受 boto3 会话，这增加了很大的灵活性，即如果您有一个应用了一些自定义配置(例如代理设置)的 boto3 会话，它可以利用这一点。
*   如果不指定 log_group 参数，默认情况下，它会将日志发送到 Cloudwatch 中的“瞭望塔”日志组。这可能是你想要的，也可能不是。
*   CloudWatchHandler 还有一个名为 create_log_group 的参数，当该参数设置为 True 时，如果日志组不存在，它将创建日志组。(假设 boto3 会话使用的 IAM 用户拥有执行此操作所需的权限)

差不多了。希望这篇文章对你有所帮助。编码快乐！