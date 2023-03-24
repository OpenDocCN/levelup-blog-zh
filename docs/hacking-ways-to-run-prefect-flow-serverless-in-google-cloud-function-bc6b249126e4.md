# 在 Google Cloud 函数中无服务器运行完美流的黑客方法

> 原文：<https://levelup.gitconnected.com/hacking-ways-to-run-prefect-flow-serverless-in-google-cloud-function-bc6b249126e4>

![](img/ababad08ccf86a94dd7e0421a1836f49.png)

完美的 T4 框架是一个优秀的工作流管理工具，它使得数据管道更加容易。

> *“自动化数据的最简单方法！”*

但是，如果你喜欢在无服务器的环境中运行它，或多或少会有些复杂，尤其是[谷歌云功能](https://cloud.google.com/functions)，因为它有最大超时 540 秒的限制。

这通常意味着如果你的工作流需要运行更长时间，你将不得不将其分割成多个部分，或者在不同的平台上运行，如[云运行](https://cloud.google.com/run)或[应用引擎](https://cloud.google.com/appengine)。这里有一个与此问题相关的 StackOverflow [线程](https://stackoverflow.com/questions/61403167/best-practice-to-run-prefect-flow-serverless-in-google-cloud#comment124276675_61403167)。

今天我就列举两种解决问题的黑客方式。

1.  使用谷歌云存储自动持久化任务状态
2.  将云函数以前的执行结果发布到它的后续执行中。

# 缓存和保存数据

默认情况下，提督核心将所有数据、结果、*和*缓存状态存储在运行流的 Python 进程的内存中。但是，如果配置了必要的挂钩，它们可以被持久化并从外部位置检索。

提督有一个“检查点”的概念，它确保每次任务成功运行时，它的返回值根据任务的`[result](https://docs.prefect.io/core/concepts/results.html)`对象和`[target](https://docs.prefect.io/core/idioms/targets.html)`中的配置写入持久存储。

```
[@task](http://twitter.com/task)(result=LocalResult(dir="~/.prefect"), target="task.txt") 
def func_task():
    return 99
```

检查点配置需要通过下面的代码全局打开。

```
import osos.environ["PREFECT__FLOWS__CHECKPOINTING"] = "true"
```

完整的代码示例如下所示。在这里，我们通过使用`GCSResult`来读写谷歌云桶

# 发布执行结果

另一种黑客解决方案是将一个 google cloud 函数的先前执行结果发布到它的后续执行中。这里，我们假设任务之间没有数据输入和输出依赖。

任务和流程定义如下。

需要做一些修改才能实现。

*   更改任务的自定义状态处理程序
*   发布前手动更改任务状态
*   编码/解码任务状态

让我们在下面的段落中逐一解释。

第一，我们知道`flow.run`函数在所有任务进入完成状态后结束，无论是成功还是失败。然而，我们不希望所有的任务都在 google cloud 函数的一次调用中运行，因为总运行时间可能会超过 540 秒。

因此使用了任务的自定义状态处理器。每当一个任务完成，我们就向完美框架发出一个`ENDRUN`信号。然后它会将剩余任务的状态设置为`Cancelled`。

第二，为了使状态为取消的任务下次能够正确执行，我们必须手动将其状态更改为`pending`。

第三，有两个必不可少的功能:`encoding_data`和`decode_data`。前者将任务状态序列化以准备发布，后者将任务状态反序列化为`flow`对象。

最后但同样重要的是，编排连接了上面的所有部分。

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，可以考虑注册[成为](https://jerryan.medium.com/membership)中的一员。你还可以无限制地访问媒体上的每个故事。