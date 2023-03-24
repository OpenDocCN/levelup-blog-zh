# 戈朗实用 DDD:领域事件

> 原文：<https://levelup.gitconnected.com/practical-ddd-in-golang-domain-event-de02ad492989>

## 领域驱动设计

## 《围棋》中关于 DDD 的故事继续呈现了一个描述现实世界中发生的事件的构建模块——领域事件。

![](img/35fe9921a4fa6096b5a5d0ad8155154b.png)

Anthony DELANOIX 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

在许多情况下，实体是领域驱动设计中描述事物的最佳方式。与值对象一起，它们可以提供我们的[问题域](https://www.definitions.net/definition/problem+domain)的最接近的图片。

有时候，呈现问题域的最佳方式是使用其中发生的事件。事实上，在我的案例中，我越来越多地尝试识别事件，然后识别与它们相关的实体。

虽然 [Eric Evans](https://twitter.com/ericevans0?lang=en) 在他的[书](https://www.amazon.de/-/en/Eric-J-Evans/dp/0321125215)的第一版中没有涉及领域事件模式，但是今天，在不使用事件的情况下完成领域层是具有挑战性的。

领域事件模式在我们的代码中代表了这样的事件。我们用它来描述现实世界中与我们的业务逻辑相关的任何事件。今天，商业世界中的一切都与一些事件有关。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)
```

# 它可以是任何东西

域事件可以是任何东西，但是它们需要满足一些规则。第一个是——它们是不可变的。为了支持这个特性，我总是在事件结构中使用私有字段，尽管我并不喜欢 Go 中的私有字段和 getters。至少，事件没有太多的 getters。

一个特定的事件只能发生一次。这意味着我们只能一次创建一个具有某种身份的`Order`实体，所以我们的代码只能触发一次描述该`Order`创建的事件。

对于那个`Order`来说，任何其他事件都是不同类型的事件。任何其他创建事件都是另一个`Order`的事件。

每个事件实际上都描述了已经发生的事情。它代表过去。这意味着我们在已经创建了`Order`的时候触发了`OrderCreated`事件，而不是在此之前。

简单事件

上面的代码示例展示了简单的域事件。这段代码是在 Go 中实现它们的数十亿个解决方案之一。在某些情况下，比如这里的`GeneralError`,我只使用了简单的字符串。

但是有时候，我有复杂的物体。或者，我必须用一些更具体的接口来扩展主事件接口，以添加额外的方法，比如用`OrderEvent`。

作为接口的域事件不需要实现任何方法。它可以是你想要的任何东西。如上所述，有时我使用字符串，但任何东西都足够好。为了一般化，时不时我还是声明一下`Event`接口。

# 老朋友

作为一种模式，域事件并不提供一些新的结构，但它是观察者模式的另一种表现形式。观察者模式认为发布者、订阅者是主角，当然还有事件。

域事件遵循相同的逻辑。订阅者或事件处理程序是一个结构，它应该响应它订阅的特定域事件。发布器是一种一旦发生某个事件就通知所有事件处理程序的结构。

发布者是触发任何事件的入口点。它包含所有事件处理程序，并为任何域服务、工厂或其他想要发布某个事件的对象提供了一个简单的接口。

EventHandler 和 EventPublisher 的示例

上面的代码片段展示了域事件模式的其余部分。接口`EventHandler`代表了应该对某个事件做出反应的任何结构。它只有一个将事件作为参数的`Notify`方法。

结构`EventPublisher`更加复杂。它提供了通用的`Notify`方法，负责通知所有订阅了那个`Event`的`EventHandlers`。另一个功能`Subscribe`增加了任何`EventHandler`订阅任何`Event`的可能性。

`EventPublisher`结构可以不那么复杂。相反，为了让`EventHandler`有机会通过使用地图来订阅某个特定的`Event`，它只能处理一个简单的`EventHandlers`数组，并通知所有的`Event`。

一般来说，我们应该在我们的领域层同步发布领域事件。但是有时，出于某种原因，我想异步地触发它们，为此，我使用了 [Goroutine](https://tour.golang.org/concurrency/1) 。

异步发布事件

上面的例子展示了异步发布事件的一种变体。为了提供这两种方法，我经常在`Event`接口中定义一个方法，该方法应该在以后告诉我是否应该同步触发`Event`。

# 创造

我最大的困境是在哪里举办活动才是合适的。老实说，到处都是我做的。我唯一的规则是有状态对象不能通知`EventPublisher`。

实体、值对象等集合(我们将在下一篇文章中讨论)是有状态对象。从这个角度来看，它们不应该包含`EventPublisher`,并且对我来说，将它作为参数提供给它们的方法总是一个丑陋的代码。

此外，我不使用有状态对象作为`EventHandlers`。如果我需要在特定事件发生时对某个实体做些什么，我会创建一个包含存储库的`EvenHandler`。然后，存储库可以提供一个应该被修改的实体。

尽管如此，在聚合的某个方法中创建事件对象是没问题的。有时，我在实体的方法中创建它们，并作为结果返回它们。然后，我使用无状态结构如域服务或工厂来通知`EventPublisher`。

事件的创建

在上面的例子中，`Order`聚合提供了一种更新递送地址的方法。该方法结果可以是一个`Event`。这意味着`Order`可以创造一些`Events`，但仅此而已。

另一方面，`OrderService`既可以创建`Events`又可以发布它们。它还可以在更新递送地址时触发从`Order`接收的`Events`。这是可能的，因为它包含`EventPublisher`。

# 其他层上的事件

我们可以监听其他层上的领域事件，比如应用程序、演示或基础设施。我们还可以定义专门用于这些层的独立事件。在这些情况下，我们不谈论领域事件。

一个简单的例子是应用层上的事件。在我们创建一个`Order`之后，在大多数情况下，我们应该发送一个`Email`给客户。尽管看起来像是一条业务规则，但发送电子邮件始终是特定于应用程序的。

在下面的例子中，有一个带有`EmailEvent`的简单代码。正如您可能猜到的，`Email`可以处于许多不同的状态，从一种状态切换到另一种状态总是在一些`Events`期间执行。

应用程序事件示例

有时我们想要在我们的[有界上下文](https://martinfowler.com/bliki/BoundedContext.html)之外触发域事件。这些域事件对于我们的有界上下文来说是内部事件，但是对于其他上下文来说是外部事件。

虽然这更像是一个[战略领域驱动设计](https://vaadin.com/learn/tutorials/ddd/strategic_domain_driven_design)的主题，但我将在这里触及这一部分。为了在我们的微服务之外发布事件，我们可能会使用一些消息服务，比如 [SQS](https://aws.amazon.com/sqs/) 。

向外部世界发布内部事件

在上面的代码片段中，基础设施层有一个简单的结构`EventSQSHandler`，每当有事件发生时，它就向 SQS 队列发送一条消息。它只发布事件名称，没有任何具体的细节。

当向外界发布内部事件时，我们也可以监听外部事件并将它们映射到内部事件。为此，我总是在基础设施层提供一些服务来监听来自外部的消息。

监听外部事件

上面的例子显示了基础设施层内部的`SQSService`。该服务监听 SQS 消息，如果可以映射到内部消息，则将它们映射到内部消息`Events`。

我没有过多地使用这种方法，但在某些情况下，这是值得的。比如如果很多微服务要对`Order`的创建做出反应。或者当`Customer`注册时。

# 结论

领域事件是我们领域逻辑中不可避免的结构。今天，商业世界中的一切都与特定的事件绑定在一起，所以用事件描述我们的领域模型是一个很好的实践。

域事件模式只是观察者模式的实现。它可以在许多对象中创建，但应该从无状态对象中触发。其他层也可以使用域事件或它们自己的事件。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)
```

# 有用的资源:

*   [https://martinfowler.com/](https://martinfowler.com/)
*   [https://www.domainlanguage.com/](https://www.domainlanguage.com/)