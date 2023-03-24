# Swift 中的依赖注入容器—第 2 部分

> 原文：<https://levelup.gitconnected.com/dependency-injection-containers-in-swift-part-2-5b24f1c4238a>

## 解析时如何配置和传递参数

![](img/d8e53093433b724557192520a17c6b5f.png)

在上一篇文章[Swift 中的依赖注入容器](/dependency-injection-container-in-swift-b4d7e139338c)中，我们探讨了什么是依赖注入容器(DIC ),以及如何创建一个简单的依赖注入容器。在本文中，我们将探讨如何向 DIC 传递参数。

目前，当从 DIC 解析对象时，我们不传递任何参数，我们只使用默认配置。对于某些情况来说，这可能已经足够好了，但是很多时候我们想要配置被解析的对象。让我们看一个例子，在使用 DIC 时会出现什么问题。

`ServiceThree`取决于`ServiceOneProtocol`和一些参数。这完全没问题，但是如果我们试图用我们的 DIC 注册这个类，我们将不能从调用者那里设置参数。我们来看看为什么。

我们用默认值注册`ServiceThree`,因为注册方法是一个通用方法，不知道参数。这意味着当`ServiceThree`被解析时，它将始终以无法更改的默认值初始化。这显然是一个需要解决的问题*(双关)。*

# 高层规划

有许多方法可以解决这个问题，但是我想提出一个利用泛型能力的解决方案。要记住的要求是

*   **可扩展性** —当添加更多具有不同参数的对象时，DICs 基本方法不应增长。
*   **类型安全** —应在编译时检测到传递的错误参数。
*   **可用性** —易于使用和理解。

让我们回忆一下我们的 DICs 接口

目前我们有一个解析方法，它根据我们注册对象的方式来解析对象。如果我们可以用自定义参数调用 resolve 方法，那不是很好吗？嗯，我们可以利用泛型的力量，花一点时间来思考我们如何才能做到这一点…

从高层次来看，我们希望能够配置被解析的对象。让我们来看看它会是什么样子

请注意新的 resolve 方法，它采用了一个配置参数。一开始可能看起来有点混乱，我们一步一步来讨论。在前面的 resolve 方法中，`Service`类型没有约束，这意味着可以使用任何类或协议。在新方法中，我们添加了一个强制`Service`类型符合`Configurable`协议的约束。

`Configurable`协议的目标是添加一种方法，使对象能够被配置，但不定义配置类型是什么。这是因为不同配置的类可以符合它。让我们来看看`Configurable`协议

`Configurable`协议增加了 configure 方法，采用通用配置。在协议中将类型声明为泛型的方法是使用`associatedtype`。在我们的例子中，`associatedtype`将`Configuration`声明为泛型。符合该协议的服务将定义具体的配置类型。

# 运行中的可配置协议

既然我们已经声明了新的配置方法和协议，让我们重构`ServiceThree`以符合`Configurable`

让我们一步一步地检查这些变化。

1.  `ServiceThreeConfigurable`符合`Configurable`。
2.  我们定义了`Configuration`类型，这个类型将包含我们所有的配置。
3.  将配置移出 init 方法需要我们将参数改为变量。
4.  我们实现了`Configurable`协议。注意配置类型不再是通用的`Configuration`，而是我们在这个类中定义的`ServiceThreeConfiguration`。这是我们定义`Configurable` `associatedtype`为自定义配置的地方，让编译器知道使用哪一个。

现在我们已经重构了`ServiceThree`以符合`Configurable`，让我们看看如何在我们的 DIC 中注册和解析它。

1.  我们像以前一样注册`ServiceThreeConfigurable`。
2.  我们用自定义值创建配置。
3.  我们用创建的配置解决`ServiceThreeConfigurable`。

我们希望我们的解析对象能够用我们的配置来配置，让我们看看如何用我们的 DIC 来实现这一点

1.  我们使用原始的 resolve 方法来解析服务，注意服务将符合`Configurable`，因为它是这个方法中被传递的一个约束。
2.  在初始化之后，我们用我们的定制配置来配置服务。

# 结论

通过在使用它们的类中定义配置类型，而不是包含它们的知识的 DIC，使用通用配置使我们的 DIC 能够向上扩展。

配置类型是在编译时检测的，这样就不容易出错，因为传递错误的配置是不会编译的。这也使得它易于使用和理解。

我希望你喜欢这个教程，如果你有任何见解或改进的想法，请留下评论。

# 我的应用

[www.koalanap.com](https://apps.apple.com/us/app/koala-nap-snore-solution/id1478157024?source=responses-----f5f046350fc9----0-----------------respond_sidebar--------------)