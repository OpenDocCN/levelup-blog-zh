# Golang 中的上下文！

> 原文：<https://levelup.gitconnected.com/context-in-golang-98908f042a57>

## 让我们得到一些

## 利用 go 中最有用的包之一

![](img/cd2c0a53402d90b2e174daef8cc2910d.png)

照片由[皮埃特罗·詹](https://unsplash.com/@pietrozj?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

golang 中的应用程序使用上下文来控制和管理可靠应用程序的非常重要的方面，例如[并发编程](/goroutines-and-channels-concurrent-programming-in-go-9f9f8495c34d)中的取消和数据共享。这听起来可能微不足道，但实际上并非如此。golang 中上下文的入口点是`context`包。它非常有用，可能是整个语言中最通用的软件包之一。如果你还没有遇到任何与上下文相关的东西，你可能很快就会遇到了(或者你只是没有太注意它)。上下文的使用如此广泛，以至于许多其他的包都依赖于它，并且假设你也会这样做。这无疑是 golang 生态系统中的一个关键组成部分。

这是 https://golang.org/pkg/context/的官方文档。它真的很棒，充满了实际的例子。为了扩展这些，让我们看看我在实际应用中遇到的事情。

## 有价值的上下文

上下文最常见的用途之一是共享数据，或者使用请求范围的值。当您有多个函数并且想要在它们之间共享数据时，您可以使用上下文来实现。最简单的方法就是使用`context.WithValue`功能。该函数基于父上下文创建一个新的上下文，并向给定的键添加一个值。您可以将内部实现想象成上下文中包含一个`map`，这样您就可以通过键添加和检索值。这非常强大，因为它允许您在上下文中存储任何类型的数据。下面是一个使用上下文添加和检索数据的示例。

通过上下文添加和读取值

上下文包背后设计的一个重要方面是所有东西都返回一个新的`context.Context`结构。这意味着您必须记住处理操作返回的值，并且可能用新的上下文覆盖旧的上下文。这是不变性中的一个关键设计。如果你想了解更多关于 golang 中的**不变性**你可以阅读[这篇我写的关于它的文章](/immutability-in-golang-7a13199060bb)。

使用这种技术，您可以将`context.Context`传递给并发函数，只要您正确地管理您传递的上下文，这是在这些并发函数之间共享作用域值的好方法(这意味着每个上下文将在它的作用域中保留它们自己的值)。这正是`net/http`包在处理 HTTP 请求时所做的。为了详细说明这一点，让我们看看下一个例子。

## 中间件

请求范围值的一个很好的例子和用例是在 web 请求处理程序中使用中间件。类型`http.Request`包含一个可以在整个 HTTP 管道中携带作用域值的上下文。常见的代码是将中间件添加到 HTTP 管道中，并将中间件的结果添加到`http.Request`上下文中。这是一个非常有用的技术，因为您可以依赖于您知道在您的管道中已经在后期发生的事情。这也使您能够使用通用代码来处理 HTTP 请求，同时考虑您想要共享数据的范围(而不是共享全局变量上的数据)。这里有一个利用请求上下文的中间件的例子。

将 guid 添加到请求上下文中的中间件

如果你想更好地理解中间件，或者只是查看这种技术的更多用例，请查看本文。

## 上下文取消

golang 中上下文的另一个非常有用的特性是取消相关的事物。当您想要传播您的取消时，这是非常重要的。当你收到一个取消信号时，传播它是一个好习惯。假设你有一个函数，你启动了几十个 goroutines。该主函数在继续之前等待所有 goroutines 完成或取消信号。如果您收到取消信号，您可能希望将它传播到所有的 goroutines，这样就不会浪费计算资源。如果您在所有 goroutines 中共享相同的上下文，您可以轻松做到这一点。

要创建一个带取消的上下文，你只需要调用函数`context.WithCancel(ctx)`将你的上下文作为参数传递。这将返回一个新的上下文和一个取消函数。要取消该上下文，您只需调用 cancel 函数。

这里有一个带有上下文取消的[对冲请求](https://medium.com/swlh/hedged-requests-tackling-tail-latency-9cea0a05f577)实现的例子。简单回顾一下[对冲请求](https://medium.com/swlh/hedged-requests-tackling-tail-latency-9cea0a05f577):我们向外部服务发出一个请求，如果响应没有在我们定义的超时时间内返回，我们发出第二个请求。当响应返回时，所有其他请求都被取消。

每个请求都在单独的 goroutine 中触发。上下文被传递给所有被触发的请求。对上下文所做的唯一一件事就是将它传播到 HTTP 客户端。这允许在调用`cancel`函数时优雅地取消请求和底层连接。对于接受一个`context.Context`作为参数的函数来说，这是一种非常常见的模式，它们要么主动地作用于上下文(比如检查它是否被取消)，要么将它传递给一个底层函数来处理它(在本例中是通过`http.Request`接收上下文的`Do`函数)。

## 上下文超时

超时是进行外部请求的一种常见模式，比如查询数据库或通过 HTTP 或 gRPC 从另一个服务获取数据(两者都支持上下文)。使用上下文包处理这些场景非常容易。您所要做的就是调用函数`context.WithTimeout(ctx, time)`，传递您的上下文和实际超时。像这样:

```
ctx, cancel := context.WithTimeout(context.Background(), 100*time.Millisecond)
```

如果您想手动触发取消功能，您仍然可以接收到它。它的工作方式与普通的上下文取消相同。

> 当 cancel 函数可用时，最好总是推迟它，以避免上下文泄漏。

这种情况下的行为非常简单。如果超时，上下文将被取消。在 HTTP 调用的情况下，它的工作方式与上面的例子基本相同。

## 协调 goroutines

在协调 goroutines 时，上下文也非常重要。实现这一点的常见模式是使用`errgroup`包。由于这是一个大而基本的主题，我决定将它摘录到自己的文章中。你可以在这里阅读:

[](/coordinating-goroutines-errgroup-c78bb5d80232) [## 协调 goro routines-err group

### 协调不同的路线以实现单一目标

levelup.gitconnected.com](/coordinating-goroutines-errgroup-c78bb5d80232) 

## gRPC

上下文是 golang 中 gRPC 实现的基础。它既用于共享数据(所谓的[元数据](https://github.com/grpc/grpc-go/blob/master/Documentation/grpc-metadata.md))也用于控制流量，比如取消一个流或请求。这是我的 [github 库](https://github.com/RicardoLinck/grpc-go)中的两个例子。

**元数据:**

服务器实现接收元数据

客户端实现发送元数据

**取消:**

处理上下文取消的服务器实现

使用超时上下文取消的客户端实现

## 开放式遥测

OpenTelemetry 还严重依赖于所谓的**上下文传播**的上下文。这是一种捆绑不同系统中发生的请求的方法。实现这一点的方法是`Inject`将信息扩展到您将要发送的上下文中，作为您正在使用的协议(例如 HTTP 或 gRPC)的一部分。在另一个服务上，您需要`Extract`脱离上下文的 span 信息。我在两篇文章中写了关于 OpenTelemetry 的内容，你可以在这里找到第一部分的[和第二部分的](https://medium.com/swlh/distributed-tracing-with-opentelemetry-part-1-6719df95a364)[和](/distributed-tracing-with-opentelemetry-part-2-cc5a9a8aa88c)。在那里，您可以找到关于 OpenTelemetry 的更多信息以及使用 gRPC 和 HTTP 的示例。

## 最后的想法

上下文被用作 golang 基本特征的一部分。因此，了解并知道如何与他们合作是非常重要的。`context`包提供了一个非常简单和轻量级的 API 来与这个关键组件交互。关于`context.Context`另一个需要强调的重要事情是它可以用于多种用途。我们在本文中讨论了很多场景，在其中一些场景中，单个上下文可以用来控制和传递取消信号以及传递作用域值。这使得上下文成为创建可靠和简单代码的非常重要和强大的工具。