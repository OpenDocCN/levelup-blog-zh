# 主 RxJS:批处理消息

> 原文：<https://levelup.gitconnected.com/master-rxjs-batching-messages-bb1e40bdd5ea>

![](img/c40888e1140c012adcb832cbb4951abf.png)

[郭锦恩](https://unsplash.com/@spacexuan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 方案

**您的应用程序连接到外部错误报告服务，使得开发人员可以直接看到任何用户错误。然而，一些错误往往会在短时间内为每个用户抛出许多次，导致类似错误的快速累积和配额的快速耗尽。**

当然，这个批处理系统将适用于任何其他类似的场景，在这些场景中，您基于可能会不断重复的输入来触发事件，比如 mouseEvents 或用户对字段的输入。

假设我们有一个简单的`errorMessageHandler`,它从应用程序的底层获取一个参数`errorMessage: string`。如果我们在服务等中使用 Observables，这是一种非常可能的情况。其中`catchError`管道用于将错误消息转发给`errorMessageHandler`。然后，`errorMessageHandler`使用`Sentry.captureException`将这些消息转发给 Sentry，并根据收到的消息抛出一个错误。我们假设它看起来像这样:

我们的超级简单但不太聪明的 errorMessageHandler

我们通过创建一个名为`messageBatcher$`的`Subject`来解决我们的问题，我们将使用`Subject`的内置`.next()`函数将我们的错误消息转发给它。我们只需更改我们的`errorHandler`以将其发送给`messageBatcher$`，并添加一个订阅，将消息转发给 Sentry。换句话说，行为将暂时保持不变。

向我们的主题转发错误消息

现在，为了将消息批量处理在一起，我们将以一种稍微有创意的方式利用一些操作符。我们将使用`buffer`对消息进行批处理，使用`debounceTime`来确定何时应该转发该批消息。根据下面的解释，生成的代码如下所示:

去抖动为 300 时批处理错误消息

那么到底是怎么回事呢？`buffer`收集传入发射中的所有值，直到提供的可观察发射，然后以数组形式发射这些值。这听起来很合理，但是什么样的可观察性会给出正确的行为呢？

在我的用例中，我希望在 300 毫秒内没有新消息被报告给批处理程序时，消息批次被转发。通过使用`messageBatcher$`本身作为源，并通过`debounceTime(300)`传输它，我最终得到了完全正确的行为。当消息不断被报告时，批处理程序只是简单地等待，直到它冷却下来，一旦 300 毫秒过去了，没有新的错误消息，消息就作为一个单独的异常被转发给 Sentry。

还有一个可以改进的地方。假设在等待期间，报告了几个相同的错误消息。在我们发送给 Sentry 的批处理错误中，我们可能不想要重复的错误。在这种情况下，我们将代码更改如下:

删除重复项的错误消息批处理程序

这个例子是基于我在一个项目中偶然发现的真实问题。在我们的例子中，对于给定的翻译关键字，我们的 CMS 中缺少翻译字符串。每个丢失的键都会抛出一个错误，并且偶尔会在产品 CMS 更新之前发布产品版本。

在这些情况下，我们会看到 Sentry 中报告的错误荒谬地增加，都是“missing translation key: BLABLA”的形式，有来自相同用户的大量重复。有时我们会看到在一个新的生产版本发布后，错误会激增到几万个，很快就花光了 Sentry 的配额。

更改为这种方法后，Sentry 的输出将是“missing translation keys: BLABLA1，BLABLA2，...”的形式，如果出现类似的问题，错误将增加到几百个，有效地减少了 90%以上的报告错误！用管道通向成功！