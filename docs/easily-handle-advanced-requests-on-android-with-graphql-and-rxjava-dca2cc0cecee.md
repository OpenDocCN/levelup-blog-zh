# 如何用 GraphQL &RxJava 处理 Android 上的高级请求

> 原文：<https://levelup.gitconnected.com/easily-handle-advanced-requests-on-android-with-graphql-and-rxjava-dca2cc0cecee>

![](img/adceec767bb9c88440a952005d5dff1c.png)

由 [SpaceX](https://unsplash.com/@spacex?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

欢迎阅读我的 GraphQL、Android 和 RxJava 系列的第二部分！如果你还没有看过，一定要看看我的文章[第一部分](/apollo-rxjava-android-graphql-the-right-way-a1f2b10a9ac8)，在那里我回顾了在 Android 上使用 RxJava 和 GraphQL 的基础知识。在本文中，我们将讨论一些更高级的主题，比如处理并行和依赖请求。我们开始吧！

# 设置

对于这里的例子，我们将继续使用我在上一篇文章中设置的 Android 应用程序。在本文中，我们也将继续使用 SpaceX GraphQL API。特别是`rocket`和`launchpads`查询。让我们从为这些端点编写 GraphQL 查询开始。

首先，`rocket`查询:

如果你读过我的前一篇文章，这应该很熟悉，唯一的不同是我们添加的`$id`参数。这用于指定我们想要获取哪个火箭，稍后我们将在 Java 代码中使用它。

现在`launchpads`查询:

这里没什么特别的，只是一些好的图表。

随着我们的 GraphQL 查询的编写，剩下的就是在我们的服务器类中为它们创建方法。

第一，`rocket`:

同样，这与我们之前的设置非常相似。但是，请注意方法定义中的 id 参数。我们用它来设置前面在 GraphQL 查询文件中定义的`$id`参数。

接下来，`launchpads`:

这个就像我们其他的服务器方法一样。我们创建一个`ApolloQueryCall`，用它创建一个查询对象，然后将它转换成一个`Observable`，这样我们就可以施展我们的 Rx 魔力了。

这就是我们的服务器类。你可以参考[回购版本](https://github.com/Ninjaman494/Android-GraphQL-Example/blob/main/app/src/main/java/com/n494/spacex/Server.java)来确保你的设置正确。完成这些后，我们应该可以开始一些高级的请求场景了。

# 场景 1:并行请求

我们的第一个场景非常常见，并行请求。很多时候，我们需要发出多个请求来获取我们需要的数据。在我们的例子中，我们将创建一个页面，列出关于火箭的信息以及它所使用的发射台的列表。因为这些请求是相互独立的，所以我们应该并行执行它们。处理这种情况的最好方法是在开始时执行两个请求，在等待响应时显示一个加载指示器，然后在两个请求完成后显示页面。使用 GraphQL 和 RxJava 的组合，在 Android 上做到这一点变得非常容易。

首先，让我们为我们的`rocket`查询创建一个`Observable`:

请注意，我们保存了这个可观察值，而不是立即对其进行操作。这是因为我们要将它与我们的第二个请求结合起来，这样我们就可以对结合的结果采取行动。不过不要担心，只要我们调用我们的`fetchRocket`方法，请求就会被发出。

然后我们进行我们的`launchpads`查询，并用`zipWith`将两个请求组合起来:

在这里，我们执行我们的`launchpads`查询，并用我们之前查询的`observable`压缩它。第二个参数是描述如何组合两个请求响应的函数。在我们的例子中，我们将首先对发射台进行排序，只保存我们想要显示的火箭使用过的发射台。然后，我们将来自两个请求的数据组合成一个`Pair`。

在管道的这一步中，我们已经将来自请求的响应合并到一个新对象中。我们的`zipWith`函数直到两个响应都解析后才会被调用，所以我们现在所要担心的就是处理合并后的结果。此时，它就像处理一个常规请求一样。我们可以只使用`subscribeWith`:

请注意，类定义使用了`Pair<RocketQuery.Rocket,List<String>>`，这是来自我们的`zipWith`函数的返回类型，这些类型需要匹配才能使我们的管道工作。在`onNext`中，我们使用在`zipWith`中创建的`Pair`来用响应数据填充我们的视图。然后，我们在`onComplete`中关闭加载状态。为了简单起见，我没有处理错误状态，但这可以在`onError`事件处理程序中轻松完成。这一步我们的管道应该完成了，你可以通过检查[这个代码文件](https://github.com/Ninjaman494/Android-GraphQL-Example/blob/main/app/src/main/java/com/n494/spacex/RocketDetailsActivity.java)来仔细检查你的管道。

使用 Apollo Android 和 RxJava，我们能够轻松处理并行请求。这可以很容易地扩展到处理更多的并行请求，只需为每个新请求添加另一个`zipWith`并相应地更新您的`subscribeWith`。

# 场景 2:从属请求

在我们最后的场景中，两个请求是相互独立的，但是如果它们不是呢？有时我们需要发出多个请求，但是第二个请求需要来自第一个请求的数据。在这种情况下，我们不能并行运行请求，而是必须顺序运行它们。

这一次我们仍然要显示关于火箭的信息，但是我们只有火箭的名称，没有它的 id。这意味着我们首先必须向`rockets`端点发出请求(参见[我的上一篇文章](/apollo-rxjava-android-graphql-the-right-way-a1f2b10a9ac8)),从响应中获取 id，然后使用该 id 调用`rocket`。使用 GraphQL 和 RxJava 在 Android 上处理这种情况轻而易举。

首先，我们将查询`rockets`端点，然后使用`concatMap`来处理结果:

这里，我们使用我在上一篇文章中创建的`fetchRockets`方法，但是用`concatMap`来处理它。使用`concatMap`，我们可以处理响应，然后将信息传递到 RxJava 管道的下一步。首先，我们对列表进行排序，找到我们要找的火箭。一旦我们找到正确的火箭，我们使用它的 id 向我们的`rocket`端点发出请求。

注意，我们正在返回由`fetchRocket`创建的`Observable`，这是因为我们将在下一个管道步骤中使用它，如下所示:

这段代码现在应该再熟悉不过了。就像我们的其他例子一样，我们使用`subscribeWith`来处理响应。你可以在这里查看已完成的[管道。有了 RxJava，我们可以轻松处理 Android 上复杂的 GraphQL 请求。](https://github.com/Ninjaman494/Android-GraphQL-Example/blob/main/app/src/main/java/com/n494/spacex/RocketDetailsByNameActivity.java)

# 结论

通过利用 Apollo Android 和 RxJava 库，我们可以毫无困难地处理一些非常复杂的请求场景。请务必查看本文的[配套回购](https://github.com/Ninjaman494/Android-GraphQL-Example)以更深入地了解代码。在我的下一篇文章中，我将讨论如何为我们提出的 GraphQL 请求编写 Android 测试。一定要跟着我，这样才不会错过！

一如既往，感谢阅读并给我一个👏如果你喜欢这篇文章！我喜欢看到我的读者如何使用他们所学的知识，所以请在下面的评论中分享你如何在你自己的 Android 应用程序中使用这些策略。