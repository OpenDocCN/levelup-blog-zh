# 浏览器和节点的 Javascript 服务包装器

> 原文：<https://levelup.gitconnected.com/collective-service-wrapper-for-browsers-and-nodejs-aac68251c583>

![](img/ac560cb8d328038af0f1cd7c5bfda596.png)

[伊莎贝拉和 Zsa Fischer](https://unsplash.com/@twinsfisch?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有时，您需要从共享管道传递您的服务函数，并对所有这些函数调用一些操作。或者，您可能希望将所有服务添加到支持并行和挂起任务的队列中。

我上面提到的需求是常见的，尤其是当你使用一个 http-请求客户端，比如 [axios](https://github.com/axios/axios) 、 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 或 [superagent](https://github.com/visionmedia/superagent) 。大量的抓取操作可能会同时发生，如果您想检查所有这些操作的共同情况，这可能会非常令人沮丧。

在这些情况下，可以使用 [**JS 服务包装器**](https://github.com/behnamazimi/js-service-wrapper) 。一个基于承诺的服务包装器，支持队列，在浏览器和节点环境下工作。

# JS 服务包装器的完整示例

这将是上面示例的控制台结果:

有三个包装的服务。它们中的每一个在队列中都有其唯一的 id，但是 ID 是从队列中的实际位置索引开始的。正如您在上面看到的，所有的服务都依次添加到队列中。当添加第一个服务时，它会触发，因为它也是队列中的第一个。

第二个服务将在第一个服务之后添加到队列中，但是它不是并行的，所以它必须等到轮到它。在第二个、第三个之后，唯一的并行服务应该添加到队列中。因为它是并行的，所以当它被添加到队列中时会立即触发。添加完所有服务后，第一个服务将被解析并从队列中删除，第二个挂起的服务将被触发。完成后，每个服务都将从队列中删除。

# 入门指南

首先，您需要在项目中导入`js-service-wrapper`。

`ServiceWrapper`是我们效用的主要对象。你需要初始化它，在你的项目中初始化一次就足够了。

您在`ServiceWrapper`上设置的客户端是初始化中最重要的部分。在`ClientHandler`里面会调用它，它会返回一个承诺。这里有一个包装的例子。这段代码将调用`axios`作为客户端，因为我们在`init`上发送它作为客户端值。

我们有机会在不同阶段中断上述正常流程。我们用钩子来做这个。让我们给服务包装器设置一些钩子。

这两个钩子将调用所有的包装器。第一个将得到结果并返回其`data`属性，第二个只是在客户端承诺成功并记录后接收结果。

有六个预定义的挂钩，您可以将它们设置到`ServiceWrapper`或每个`ClientHandler`上，以影响服务。

1.  `HOOKS.BEFORE_FIRE`在客户端服务调用之前调用，但是这个钩子不是异步的，fire 不会等待这个。
2.  `HOOKS.BEFORE_RESOLVE`当服务客户端承诺正在解析时调用，它返回的值将作为解析参数发送。
3.  `HOOKS.BEFORE_REJECT`当服务客户端 promise 拒绝时调用，其返回值将作为拒绝参数发送。
4.  `HOOKS.AFTER_SUCCESS`正好在解析之前调用，这也不是异步的。
5.  `HOOKS.AFTER_FAIL`正好在拒绝之前调用，这也不是异步的。
6.  `HOOKS.UPDATE_REQUEST_CONFIG`使用这个钩子，你可以在触发之前更新请求配置。

此外，您可以为每个`ClientHandler`设置特殊的`client`和`hooks`:

如果您在初始化时激活队列，那么您可以指定队列中每个服务的行为，并确定您的服务应该与其他服务并行还是挂起。为此，您应该将选项传递给`fire`方法的
，并将`parallel`属性的值设置为`true`或`false`。下面是一个并行和待定服务定义的示例。

# 技巧

*   要使用`fetch`作为您的客户端函数，您需要像`fetch.bind(window)`一样发送它的绑定版本。
*   您传递给`fire`方法的`fireOptions`可以作为第二个参数在钩子方法中访问。
*   包装器本身是 promise base，但是`client`函数不一定是 promise。

# 结论

当您希望以一种通用的方式同时控制许多服务时，这个包装器会非常有用。我在之前的两个项目中使用了这种代码结构，并尽可能地让它动态化。您可以将它与许多著名的 HTTP 请求处理程序一起使用，也可以定义您的客户端。您可以根据需要启用或禁用队列。

图书馆是新的，它可能没有涵盖所有的东西，或者可能有一些问题。评论你的想法并[为项目](https://github.com/behnamazimi/js-service-wrapper)做出贡献。我会很高兴的。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)