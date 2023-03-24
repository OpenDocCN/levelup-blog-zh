# 协程、改进和处理响应的好方法

> 原文：<https://levelup.gitconnected.com/coroutines-retrofit-and-a-nice-way-to-handle-responses-769e013ee6ef>

![](img/aa29bac4befc36dc464e1916c9a22981.png)

马里奥·卡鲁索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我们开始之前，我假设读者已经了解了 [Kotlin](https://kotlinlang.org/) 、[协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)、[react vex](https://github.com/ReactiveX/RxKotlin)和 [Android 应用架构](https://developer.android.com/jetpack/docs/guide#overview)的知识和基本概念。

# 一次性异步调用

随着协程程序变得越来越流行，我决定更新我的*ViewModel-Repository-data source*通信逻辑，并将其从 ReactiveX 改为协程程序。Rx 没有任何问题，实际上，Kotlin 有数据流- checkout [Flow 和 Channel](https://medium.com/@elizarov/kotlin-flows-and-coroutines-256260fb3bdb) -但是我完全同意 [Daniel Lew](https://medium.com/u/fa019abb3ca6?source=post_page-----769e013ee6ef--------------------------------) 在他的 [Grokking Coroutines](https://youtu.be/Axq8LdQqQGQ?t=2677) 演讲中所说的:

> “…并发与数据流没有任何关系。”

我们中有多少人实现过这样的场景:使用*“一次性提取操作”*来返回数据、完成或抛出错误:

基于流事件的体系结构

我们真正想要的是一个异步任务，它将获取数据，可能执行一些操作，并最终在准备就绪时通知我们。我们不需要一个[单个的](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html)或[可完成的](http://reactivex.io/RxJava/javadoc/io/reactivex/Completable.html)来做这件事。

考虑到我已经在几个项目中成功地实现了这个方法，我决定写一篇关于它的文章。

# 异步架构

首先，让我们画出我们的建筑。我们将有一个**基础服务**负责处理网络呼叫、成功和错误。 **ChildServices** 将扩展 *BaseService* 。我们的**存储库**将与 *ChildServices* 进行通信，并管理所有信息，然后将信息返回给 **ViewModel** ，并将**响应**映射到 ViewModel 的世界。

异步架构的基础服务

*Api* 和*儿童服务*非常简单:

异步架构的 Api 和子服务

**注意:** *ApiService* 它是一个抽象层，用来更好地将 API 逻辑与 *BaseService 隔离开来。*

现在有趣的部分，**储存库**。在我们的 *BaseService* 中，我们将返回一个带有成功或错误的**结果< T >** :

因此，这也是我们将在存储库中返回的内容:

对于拼图的最后一块，**视图模型**(子):

就是这样！

您可能已经注意到，我的存储库调用被包装在一个`safeCall`函数中。存在于 **BaseViewModel 中的简单函数:**

它的目的是通过`parseError(e)`返回成功调用的数据或处理错误(可以显示 Toast、Snackbar 等)。

但是，在某些情况下，我们可能需要向 ViewModel 返回更详细的信息，或者提供更好的错误处理 UX 体验，例如，根据类型显示错误动画。要做到这一点，让我们倒回去一点，再次关注我们的存储库(这就是为什么我把它叫做*有趣的部分*)。

> "现在有趣的部分，**储存库."**

```
is Result.Success -> result.data
is Result.Error -> throw result.exception
```

让我们用更优雅的方法更新这段代码。为此，让我们把它看作是一个*“用例”*响应*。*一个`sealed class`是一个很好的候选，因为它可以表示*状态*并保存特定*状态*的数据

接下来，我们将 api 调用更新为:

最后，我们的视图模型:

通过这种方式，代码更容易阅读，因为它记录了自己，并且变得更容易维护和测试。

有趣的是，[弗洛里纳·蒙特内斯库](https://medium.com/u/d5885adb1ddf?source=post_page-----769e013ee6ef--------------------------------)发表了一篇文章 [*用一个类*](https://medium.com/androiddevelopers/sealed-with-a-class-a906f28ab7b5) 密封，谈论如何实现这种行为。我很高兴地注意到我也在使用她的方法。

# 结论

通过这种重构，我消除了对额外库的需要，在这种情况下是 RxKotlin，但最重要的是——因为有人可能会说我也可以使用流和通道而不导入 RxKotlin——我们使用了正确的工具来完成这项工作:

> 用协程程序实现的异步编程模式，用于执行“一次性调用”*。*

关于 Android 应用架构，我们的服务和存储库通过一个`sealed class Result`进行通信。然后，在将它返回到 ViewModel 之前，如果需要的话，存储库会将它再次映射到另一个代表*“用例”的`sealed class`中。*这种方式返回了详细的响应，我们也受益于更清晰的代码。

希望你觉得这篇文章有用，感谢阅读。

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)