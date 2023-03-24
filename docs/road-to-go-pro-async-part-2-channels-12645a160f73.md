# Pro 之路—异步第 2 部分:渠道

> 原文：<https://levelup.gitconnected.com/road-to-go-pro-async-part-2-channels-12645a160f73>

*在我们开始之前，你可以在这个* [*资源库*](https://github.com/songx23/RoadToGoPro) *中找到本教程使用的代码。你可以在这里* *找到 Road to Go Pro* [*的全部内容。如果你错过了最后一个，你可以通过这个*](https://medium.com/@songx/road-to-go-pro-f9d1f8a51fad) [*链接*](/road-to-go-pro-async-part-1-goroutine-waitgroup-e915accab659) *找到它。好吧，我们开始吧。*

在前面的故事中，我们了解了 goroutines 和 Go 中的等待组。它们是异步处理的基础。在这个故事中，我们将学习另一个重要的构建模块，它支持 goroutines 来实现更高级的异步功能。

# 频道

![](img/e064cff06705d63ed654b340299c7d32.png)

约瑟夫·巴里恩托斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

通道是 Go 中的一种特殊数据类型，用于在不同的 goroutines 之间发送和接收值。像切片和贴图一样，通道需要在使用前创建。

```
ch := make(chan string, 2)
```

为了创建一个通道，我们需要使用`make`函数。渠道类型的关键字是`chan`。必须键入频道。它显示了可以发送或接收的数据类型。为了声明通道的类型，我们在`chan`关键字后添加了一个类型。我们需要为通道定义的另一件事是缓冲区大小，尽管它不是`make`函数的强制参数。如果未指定，通道缓冲区大小的默认值为 1。缓冲区大小决定了一个通道在被阻塞之前可以容纳多少个元素。

你还记得《T2》最后一部《T3》中的例子吗？让我们快速回顾一下。我们有一小批数学问题要解决。在示例代码中，我们为批处理中的每个数学问题创建了一个 goroutine。与同步处理相比，这显著提高了处理速度。然而，如果我们将数学问题的数量扩大到几十万，那么创建一个 goroutine 来处理每一个问题可能并不明智。尽管与线程相比，goroutines 相对“便宜”，但它们毕竟不是**免费的**。随着 goroutines 数量的增加，我们最终会遇到资源问题。解决无限伸缩问题的一个更好的方法是使用生产者/消费者模式。

## 生产者/消费者模式

为了解决我们扩大的数学问题，我们可以将过程分成两部分，即生产者端和消费者端。生产者负责吐出带有不同参数的数学问题。消费者负责解决数学问题。通过划分关注的领域，我们现在可以以可控的方式扩展我们的应用程序。由于制作数学题相当容易，一个生产商就足以让消费者忙起来。在消费者方面，我们可以增加消费者的数量来加快这个过程，而不会遇到资源问题。

让我们考虑两个极端。如果我们将消费者的数量设置为 1，那么它将与同步处理相同。如果我们将消费者的数量设定为我们拥有的数学问题的数量，这将等同于过度制造 goroutines。通过控制消费者的数量，我们可以调整我们的应用程序，在时间和资源之间进行权衡。最佳比例介于两者之间。更多关于如何取得良好的平衡稍后。首先，让我们看看如何使用通道实现生产者/消费者模式。

产生逻辑

生产者/消费者模式的关键组件是消息传递通道。我们创建一个`int`类型的通道，因为数学问题的唯一变量是一个整数。假设我们有一百万个变量要传递，我们可以给通道一个大小为 10 的缓冲区。然而，缓冲区大小不会对性能产生太大影响，因为我们的生产逻辑比消费逻辑要快得多。因此，消费者总是走向瓶颈。在生产循环中，我们将变量`i`推入通道。乍一看，这种通道语法似乎不同寻常。我很难记住如何在通道中读写数据。直到我发现了对语法的直观解释。

> 你唯一需要记住的是细长的箭头总是指向左边`<-`。然后根据箭头的位置，可以解读意思。当一个箭头指向一个通道(`channel <- xxx`)时，它看起来像是在把东西推进通道。这是将数据写入通道的语法。另一方面，当箭头指向远离某个频道的方向时(`xxx := <- channel`)，它看起来像是在从频道中取出东西。这是从通道读取数据的语法。

如果我们只是运行生产逻辑，我们会在控制台中看到死锁错误。`fatal error: all goroutines are asleep — deadlock!`。通道缓冲区将很快被生产逻辑填满。一旦一个通道被填满，它就会阻塞。当我们在一个 goroutine(主 goroutine)中运行生产逻辑时，填充的通道将阻止 goroutine 完成。因此，它在大喊“僵局！”。Go 非常聪明，能够检测到这一点，并防止我们的 goroutine 死锁。

好了，我们已经讨论了生产逻辑。现在让我们来看看消费者方面的情况。我们将从消费者结构开始。

消费者(工人)

核心消费者逻辑与上一篇文章中的示例代码相同。我们只是让函数休眠一会儿，以模拟处理复杂数学计算的延迟。除了处理逻辑之外，我们还需要构建一个结构，以便于使用消息通道中的变量。让我们放大`start`功能。该功能是启动消费者的触发器。它开始一个无限循环，并从消息通道获取变量(如果有的话)。您可以注意到上面提到的从通道读取数据的语法。`param, ok := <- w.queue`。从通道读取数据时，Go 返回 2 个值。第一个返回值是频道中的项目。另一个是指示通道是否仍然打开的标志。如果该通道关闭，标志将为假。在这种情况下，我们的消费者应该关闭并停止处理。

我们的消费者结构看起来不错，现在让我们启动消费者并打开闸门。

启动消费者

我们在`main`函数中添加了消费逻辑。需要强调的一点是，为了防止同样的死锁问题发生，我们需要在生产逻辑启动之前启动消费逻辑。否则，生产逻辑将阻止消费逻辑启动。这样一来，是时候运行我们的应用程序了。您应该在控制台中看到生产变量和消费变量的日志。

```
Producing with parameter: 99
Producing with parameter: 100
math problem solved for input: 90.
math problem solved for input: 87.
math problem solved for input: 85.
math problem solved for input: 88.
math problem solved for input: 89.
math problem solved for input: 82.
math problem solved for input: 86.
math problem solved for input: 81.
math problem solved for input: 84.
math problem solved for input: 83.
math problem solved for input: 100.
math problem solved for input: 91.
math problem solved for input: 99.
math problem solved for input: 93.
math problem solved for input: 96.
math problem solved for input: 97.
math problem solved for input: 95.
math problem solved for input: 94.
math problem solved for input: 92.
math problem solved for input: 98.
```

恭喜，我们已经在 Go 中实现了一个非常简单的生产者/消费者模式。

还有一个问题我们没有回答，就是工人数量和处理速度的关系。Go 提供了一个测试代码性能的工具。我们将在下一节中使用下面的生产者/消费者实现来编写基准测试。

完整的生产者/消费者实现

## 基准

![](img/d3983d1b01bd8a7de4123609d9e81436.png)

照片由 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Go 测试包提供[基准测试能力](https://pkg.go.dev/testing#hdr-Benchmarks)。基准测试不使用常规的`testing.T`，而是使用`testing.B`。并且测试名称以`Benchmark`开头，而不是`Test`。它的设置也非常简单。我们需要将我们想要进行基准测试的函数包装在一个“特殊 for 循环”中。Go test tool 多次调用目标函数，并记录其性能和内存使用情况。常见的模式如下所示。

基准测试模式

为了运行基准测试，您可以在`go test`命令后添加`-bench`。如果你想测试内存分配，增加一个标志`-benchmem`。完整的命令如下所示:

```
go test -bench=<./directory/file> -benchmem
```

我们可以利用基准测试，通过对不同数量的消费者工作者运行基准测试来发现消费者数量和整体性能之间的关系。之后，我们可以分析结果，看看哪种组合能产生最好的结果。下面的代码展示了针对 1 到 10000 名工作人员的基准测试。

面向消费者的基准测试

下面列出了这些基准测试的结果。第二列显示我们完成了多少操作(数字越大，性能越好)。第三列显示一个操作花费的时间(数字越小表示性能越好)。很明显，消费者越多，我们的业绩就越好。是这样吗…？

增加工作线程数可以提高性能

如果我们不断增加消费者的数量，你会看到性能开始下降。在关键点之后，并发的开销将超过它带来的好处。

你渴望弄脏你的手吗？这里有一些你可以自己探索的东西。在家做绝对安全。😉

1.  将`-benchmem`添加到测试命令中，并探索工作数量和内存使用量之间的关系。
2.  尝试找到产生最佳性能的最佳工人数量。

但从长远来看，增加更多的工人并不是一个好主意

# 下一步是什么？

![](img/585bd742bc36a1817f270bf4a1cd3e2a.png)

在这个故事中，我们深入探讨了渠道、生产者/消费者模式和基准测试。我计划在这个故事中也包括锁，但是这个故事的内容太长了。我将在下一篇文章中跟进锁和同步包！敬请关注。我希望您会对围绕异步处理的内容感兴趣。

如果你遇到任何问题或者需要帮助，请在下面留下你的评论。随时欢迎反馈。感谢您的阅读！

*如果你想支持我，你可以通过这个推荐链接成为一个中等会员。谢谢你。*

[](https://songx.medium.com/membership) [## 通过我的推荐链接-宋雪加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

songx.medium.com](https://songx.medium.com/membership)