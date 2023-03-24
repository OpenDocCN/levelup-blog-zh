# 更多关于 Go 通道、并行性和并发性的信息

> 原文：<https://levelup.gitconnected.com/as-a-follow-up-of-basic-parallel-computing-in-go-i-wanted-to-build-a-more-complex-example-where-13978bf84608>

![](img/ebf3909a79eb951ac4a87b21ce9da0b5.png)

作为 Go 中[基本并行计算的后续，我想构建一个更复杂的例子，在这个例子中，我们在 Go 并发编程中使用了更高级的技术。](https://medium.com/@anicolaspp/basic-parallel-computing-in-go-fda50894241c?source=your_stories_page-------------------------------------)

让我们首先创建一个函数`ProduceInts`，它在一段时间`t`内将随机数据生成到一个通道中。时间`t`过去后，它关闭通道，表示不再产生数据。注意`ProduceInts`没有阻塞。

现在，我们可以创建两个函数来处理产生的数据(int)。

在下面的代码片段中，我们添加了两个新函数，`CountInts`和`Odds`。第一个只是创建一个`map`来跟踪一个特定的数字已经生成了多少次。第二个函数检查生成的数字是否是奇数，如果检查通过，则打印它。

请注意，这两个新函数都是从一个通道中读取，它们不会阻塞，它们彼此独立运行，并且它们自己的许多实例可能会同时运行(这对可伸缩性很重要)。

现在，我们可以在 Go 中使用我们在[基本并行计算中创建的`Splitter`将来自原始通道(流)的值路由到两个不同的通道，以便`Odds`和`CountInts`可以读取。](https://medium.com/@anicolaspp/basic-parallel-computing-in-go-fda50894241c?source=your_stories_page-------------------------------------)

在`router`包中，我们添加了`Splitter`的实现以及一些功能，这些功能将允许我们组成通道并控制数据如何流动。

最后，我们需要把所有事情都联系在一起。

在`main`包中，我们基本上开始生成随机整数，但只持续一定的时间(20 秒)。

然后，我们使用`router`包来传输`ProduceInts`和`Splitter`的输出，这又增加了两个输出，这两个输出将作为`CountInts`和`Odds`的输入。

最后，我们通过做`router.Run()`来开始`Splitter`。由于不想让程序在所有项目处理完之前结束，我们等到`Splitter`不再运行。

正如我们所看到的，在 Go 中同步流和管道是非常容易的，我们只需要把通道看作队列，我们可以在任何时候从独立的进程中读写。

***快乐编码。***