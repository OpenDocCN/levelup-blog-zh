# 无服务器功能的请求上下文

> 原文：<https://levelup.gitconnected.com/request-contexts-for-serverless-functions-6ffa0291ddf8>

## 让我们探索 Firebase 函数中请求范围的上下文

![](img/9a48620dbfe84a599845d88812ea3c36.png)

照片由[泰勒维克](https://unsplash.com/@tvick?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我目前是一名软件工程师，从事一个无服务器、事件驱动、多租户、分布式系统的工作，该系统由 TypeScript 构建，由 Google 云平台托管。该项目最近才开始，主要强调工作的吞吐量。然而，现在我们已经达到了产品路线图中的一个关键点，并开始将系统稳定性作为主要关注点，然后再推出新功能。

为此，系统中缺少的第一个也可能是最重要的东西是相关日志。可以想象，拥有一个没有某种形式的日志关联的庞大且不断增长的系统，只会导致头痛和深夜调试会话。因此，我决定承担这项艰巨的任务，即改进系统间的日志关联。

对于所有在幕后使用 Express 的 HTTP 端点来说，这不是问题，简单地用中间件组件拦截所有传入的请求是轻而易举的事情。真正的问题始于 Firebase 的`onCall`和`PubSub`函数。

# **问题**

我遇到的第一个主要问题是，显然没有任何用于`onCall`功能的中间件组件，也没有任何用于`PubSub`功能的中间件组件。我当然不会满足于在函数参数中传递相关 ID，因为这在小型系统中是混乱且不可维护的，更不用说大型且不断增长的系统了。因此，我开始研究如何创建一个请求范围的上下文，并很快将节点的' [async_hooks](https://nodejs.org/api/async_hooks.html) '功能确定为一个可能的救星。

“async_hooks”库本质上允许你用一个内置的 API 来跟踪异步任务。让我们来看看如何创建一个上下文，在这个上下文中，我们可以存储一个关联 ID、关于请求的元数据或者我们可能需要从上述函数的范围内访问的任何其他内容。

![](img/0fa45fa644c86a1bf0696ace5e6c579a.png)

美国宇航局在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 异步挂钩上下文

我们需要做的第一件事是安装必要的依赖项。该库基于“async_hooks”库，将创建一个请求范围的上下文，它可以存储或生成一个关联 ID，以及我们可能需要的关于请求的其他元数据。

虽然有几个库使用了“async_hooks”库，但我还是选择使用下面的，因为它首先是用 TypeScript 构建的，而且是轻量级的。

*注意——我使用的是 Bunyan logger，它是从装饰日志流的 logger 工厂创建的。在本文中，我不会讨论流装饰或装饰器模式。*

```
$ npm i async-hooks-context
```

# “发布订阅”事件

现在我们已经安装了必要的包，我们可以开始实现日志关联了。首先，我们来看看如何为一个`PubSub`函数做这件事。

下面是一个简化的代码片段，它是为了演示的目的而创建的，用来强调如何在请求上下文中设置相关性 ID，以便它在函数的范围内可用。

我们可以看到这里有三个命名的函数，导出的 Firebase onPublish 处理程序、我们的包装器和我们自己的处理程序。当请求触发 onPublish 处理程序时，消息连同我们的处理程序一起被传递给我们的包装器。然后在请求上下文中设置一个相关 ID，方法是提取传入的相关 ID，或者如果没有找到相关 ID，就生成一个。然后，我们简单地用原始消息调用我们的处理程序。

特别是对于这种情况，虽然上面的代码片段中没有演示，但是我也将包装器提取到一个单独的类中作为一个公共静态函数，这允许我重用它并对逻辑进行单元测试。

现在，我们可以从处理程序范围内的任何类访问相关 ID。由于我们在处理程序中创建了日志工厂，这意味着我们可以访问请求的相关 ID，并使用`getCorrelationId()`函数在日志工厂中修饰日志流。

![](img/01cd553146ea1d6b2358d96276d25043.png)

由 [Marvin Meyer](https://unsplash.com/@marvelous?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# “随叫随到”难题

考虑到我们是如何为`PubSub`函数创建请求上下文的，您会想象为`onCall`函数创建请求上下文的过程会是完全相同的？嗯……是的。差不多了。

对于那些不熟悉 Firebase 的`onCall`功能的人，我在下面的参考资料中链接了 Firebase 文档。简而言之，它允许前端应用程序通过网络调用 HTTPS 资源作为函数。

我们的前端不关心我们的后端日志关联，因此，不传递关联 ID。这极大地简化了库的实现。

从上面可以看出，我们不再需要显式包装函数处理程序。如果我们不关心传入请求的数据，就拥有一个关联 ID 而言，那么库将自动为我们生成一个关联 ID，并将其保存到请求上下文中。

同样，我们可以从 logger 工厂中访问上下文，或者从代码库中任何有意义的地方访问上下文，用相关 ID 修饰日志流。

# 存储附加数据

我在上面提到过，您也可以将元数据存储为请求上下文的一部分。我建议不要存储超过绝对必要的内容，但是如果您的用例需要的不仅仅是一个典型的关联 ID，您可以使用`updateRequestContext()`函数以大致相同的方式来完成。这将使用提供的对象创建或更新现有的请求上下文。

请求上下文，包括任何生成的相关 ID，稍后可以用`getRequestContext()`函数轻松地检索到。只要在适当的范围内调用，就不会有问题。

# 其他考虑

在处理分布式系统时要记住的一点是，一个请求在其生命周期中可能会涉及多个服务。因此，我们希望将关联 ID 附加到任何传出的 API 调用或发布的事件上。用关联 ID 包装所有传出的 PubSub 和 HTTP 请求将大大减少调试时间。

下一步是实现集中记录。能够在一个地方看到来自不同系统的所有日志。有了新实现的关联日志记录，识别与单个请求相关的日志将变得轻而易举。

# 最后的话

感谢您阅读这篇文章。我希望你能发现一些发人深省的内容，并带着一些新的想法离开。如果你有任何反馈，请随意写在下面的评论区。我一直在努力学习和提高，如果能听到一些其他的意见甚至批评，那就太好了。我们都是来学习的！

# 资源

[NodeJS async _ hooks](https://nodejs.org/api/async_hooks.html)
NPM async-hooks-context
[PubSub 函数](https://firebase.google.com/docs/functions/pubsub-events)
[onCall 函数](https://firebase.google.com/docs/functions/callable)