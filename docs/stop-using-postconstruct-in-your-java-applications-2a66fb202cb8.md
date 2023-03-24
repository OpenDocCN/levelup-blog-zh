# 停止在 Java 应用程序中使用@PostConstruct

> 原文：<https://levelup.gitconnected.com/stop-using-postconstruct-in-your-java-applications-2a66fb202cb8>

> 这个故事是我之前的一篇文章[停止使用 Setters](/stop-using-setters-785670f4bf8) 的续篇。如果你还没看过，你应该去看看。

如果你是 Java 开发人员，就没必要解释 Spring 框架的概念。我们很清楚`@Component`、`@Autowired`和许多其他有用的注释。也许你更喜欢 Jakarta EE 栈，那么你的选择是`@ManagedBean`、`@Inject`等等。在这两种情况下，有一样东西看起来很方便，但却使您的代码更难维护，也更脆弱。`@PostConstruct`注解。在这篇文章中，我将试图说服你，你应该永远忘记它。

![](img/492a07dbfb4ecae15e3960a25ff726f9.png)

图片由[卡洛斯·林肯](https://pixabay.com/users/LincolnGroup-44708/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=167535)从[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=167535)获得

`@PostConstruct`的目的是什么？通常，我们用它来推迟一些必须在对象实例化后执行的事件。例如，假设我们的应用程序需要一个接受用户请求并将其添加到队列中的服务。我们该如何设计`QueueService`？这里有一个选择。

常见@PostConstruct 用法的示例

首先，`QueueService`检查用户是否拥有所需的权限。如果是这样，它将新请求添加到`NativeQueue`。但是队列需要在开始时被初始化。我们不能把初始化放在`addQueueEvent`里面，因为它应该只被调用一次。使用`@PostConstruct`块似乎是一个好方法。但是如果你仔细观察，你会发现它和 setter 并没有太大的不同。在这两种情况下，我们都创建了尚未准备好使用的东西。然后经过一些特殊的处理，一个物体就完整了。遗憾的是，这不是唯一的问题。让我们逐一讨论。

## 没有办法为`QueueService`编写一个合适的单元测试

可以看到，`initializeQueue`方法是`private`。Spring(以及 Jakarta EE)对此并不关心，因为框架使用[反射 API](https://www.geeksforgeeks.org/reflection-in-java/) 来执行带注释的方法。但我们确实关心它。用单元测试覆盖这个方法是不可能的，因为事实上从外部作用域它是不可访问的，并且在类内部没有任何东西调用它。

怎么修？最简单的方法是改变方法的范围。我们可以宣布`initializeQueue`为`package-private`甚至`public`之一。但是同样，它允许其他组件随时调用它。这可能会导致令人不快的后果(比如意外的队列刷新)。

有人可能会说，相反，我们可以使用反射 API 从测试中调用方法。这意味着该功能仍然对外界隐藏。也许我会写另一个故事，但现在，我只想说:

> 请不要在你的测试中使用`Reflection API`！它只会给你带来可维护性的地狱。

最近，我和我的同事就将`@PostConstruct`放在`private`方法上进行了协商。他告诉我这绝对没问题。如果我们想验证这个类，我们必须在测试中启动 Spring 上下文。我完全同意集成测试应该和单元测试一样存在。但是我认为单元测试在任何情况下都是必要的。一个类必须总是可以独立于系统进行验证。集成测试应该扩展，而不是取代单元测试。

## 该类变得更加脆弱，可重用性更低

假设我们需要在系统启动时向队列添加一些默认请求。这是可以做到的。

应用程序启动时队列完成

有趣的是，这段代码不是确定性的。它会发挥作用，但只是偶尔为之。不能保证`@PostConstruct`会在`QueueFulfillingService`实例化之前执行。因此，有时应用程序会因意外异常而崩溃。

能修好吗？嗯，算是吧。我们可以用`BeanFactoryPostProcessor`和`PriorityOrdered`接口代替`@PostConstruct`。第一个定义了应该在对象实例化后执行的动作。第二个接口告诉 Spring 组件初始化的顺序。虽然它解决了这个问题，但它会使我们的代码变得冗长，并且与 Spring 生态系统过于耦合。这不是处理这种简单案件的最佳方式。

## NativeQuery 初始化失败意味着应用程序崩溃

`QueueService`是必须的特性吗？没必要。但是如果`NativeQuery.init()`失败了，就会碾压整个应用。有人可能会说，这正是我们希望从系统中得到的。如果出了问题，最好尽早识别错误。我怀疑这种说法。原因如下。

我几乎每天都使用 Gmail。如果你也这样做，你知道你可以通过将鼠标指针放在电子邮件的发件人姓名上来检查联系信息。您知道这个操作会调用另一个 HTTP 请求吗？您可以通过打开开发控制台来注意到它。我认为这个特性并不重要。如果服务器端有问题，看不到弹出窗口是绝对没问题的。但是如果在应用程序启动时验证这个特性呢？任何错误都意味着整个系统的停止。我认为价格太高了。

据我所知，这个功能是在单独的微服务中实现的。在这种情况下，启动失败不会杀死整个电子邮件集群，但我的观点是，所有需要的初始化应该只在需要时调用，而不是提前调用。

# 解决办法

最明显的解决方案是将所有必需的步骤放在构造函数中。尽管它解决了前两个问题(单元测试和可重用性)，但它对启动失败没有任何作用。我们需要的是只在需要的时候调用一次的惰性初始化。这里是方法。

具有惰性 NativeQueue 初始化的 QueueService

`CachedResultSupplier`是`Supplier`界面的装饰者。它只在第一次`get`调用时计算给定的λ。进一步的调用返回缓存的值。

CachedResultSupplier

事实上，`NativeQueue`实例化和初始化只在第一次调用`addQueueEvent`时执行。如果有东西坏了，我们可以正确地通知用户。因为现在我们意识到了可能出现的错误，并且能够以适当的方式处理它们。此外，我们可以添加高级日志记录和审计，以便更有效地识别错误。

# 结论

我希望我说服了你，使用`@PostConstruct`是一种不好的做法。不仅如此，即使没有 Spring 或 Jakarta EE 特性，它也可以很容易地被替换。如果您有任何问题或建议，请在下面留下您的评论。感谢阅读！