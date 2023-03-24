# Golang web 应用程序的中间件

> 原文：<https://levelup.gitconnected.com/middlewares-for-golang-web-apps-8742e28eef6e>

## 创建处理请求的管道

![](img/162f2724c198e66423167a1b5f7f2c15.png)

照片由[雷米·卢多·吉林](https://unsplash.com/@gieling?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

当使用 web 应用程序时，如 web API、网站或任何公开 web 服务器并处理请求的应用程序，最常见的行为是以不同的方式处理每个 URL。返回不同的网页，将数据保存在存储器中，以 JSON 的形式获取和返回数据，等等。

然而，一些任务需要被执行，而不管你以一种通用的方式接收到的请求。复制和粘贴代码或者调用一个单独的函数，虽然是一种选择，但不是解决这个问题的最佳方式。这种方法的一个问题是代码重复，另一个问题是人为错误。也许一些开发人员忘记添加对为给定 URL 收集指标的函数的调用，或者记录错误。那以后会引起一些头痛。大时代。作为解决这个问题的另一种方法，我们可以使用*中间件。*

使用中间件的主要区别在于，您创建了一段通用代码，并将其注册到 HTTP 执行管道中。这样，您不必担心在您创建的每个处理程序中调用通用代码，也不会有代码重复。听起来很有希望，不是吗？

这个概念并不是 **golang** ，**ASP.NET**使用完全相同的概念。因此，学习这项技术不仅对 go 开发人员有用，而且对任何使用 web 应用程序的人都有用。

## 了解 HTTP 执行管道

在 golang 中，HTTP 请求处理逻辑像管道一样工作。在最基本的场景中，您将为每个想要处理的 URL 创建一个函数。在这些情况下，至少会发生两件事:

1.  将从请求中提取 url，并与您注册处理的所有 url 模式进行匹配；
2.  将执行与匹配模式相关联的功能；

代码摘自我的[对冲请求](https://medium.com/swlh/hedged-requests-tackling-tail-latency-9cea0a05f577)文章

这里有一个简单的例子，这个服务器收到的每个请求将执行上面的步骤 1，当请求上的 URL 是`/ishealthy`时，函数(在这个例子中是匿名的)将被执行(步骤 2)。

对于一个简单的服务器来说，这很好，但是在现实世界的应用程序中，甚至在执行函数之前，就需要发生一些普通的事情。您可能希望添加到所有请求(或一组请求)中的一般内容有:授权、日志记录、指标收集、HTTP 头操作等等。实现这一点的一种方法是使用中间件。包 [gorrila mux](https://github.com/gorilla/mux) 非常明确地使用了这种技术。这就是为什么我要用它来实现这些例子。但是，如果您愿意，也可以只使用带有`http.Handler`函数的标准包来手动创建执行管道。

## 中间件

中间件和整个 HTTP 执行管道背后的模式是创建一个接收下一个处理程序作为参数的`http.Handler`。就像装饰图案一样。因此，`MiddlewareFunc`的签名是`type MiddlewareFunc func(http.Handler) http.Handler`。这意味着在您的中间件内部，您不需要知道下一个要执行的是哪个处理程序，您只需要在完成工作后执行它。这是一个非常好的关注点的抽象和隔离。注册你想要使用的中间件的方法是从路由器调用函数`Use`。

```
router := mux.NewRouter()
router.Use(middleware)
```

注册中间件的顺序就是它们将要执行的顺序(在到达 URL 模式的处理程序之前)。每个中间件的代码通常由一个闭包组成，这个闭包对作为参数传递给它的`http.ResponseWriter`和`http.Request`做一些事情，然后调用管道上的下一个处理程序。这里有一个简单中间件的例子。

`loggingMiddleware`函数是中间件实现。正如我们所看到的，这个中间件记录了所请求的 URL(第 25 行)，执行下一个处理程序(第 26 行)，并记录请求在此之后已经完成。这就是这种技术如此强大的原因，您可以完全控制想要执行什么以及何时执行。在这种情况下，管道上所有剩余的处理程序将在到达第 27 行之前执行。中间件注册在`main`函数上(第 12 行)。如果我们在本地执行这段代码并请求`/ishealthy` URL，我们会在控制台上看到如下内容:

```
2020/05/26 14:56:29 Url requested: /ishealthy
2020/05/26 14:56:29 Returning 200 - Healthy
2020/05/26 14:56:29 Request finished
```

中间件功能的签名是非常有限的，在真实的场景中，你可能需要注入一些东西来使它更有用，为此，我写了一篇文章来解释一个非常简单却非常强大的技术，用于 go 中的[依赖注入。](/dependency-injection-in-go-using-receiver-functions-d76b7e541ecd)

## 子路由器的中间件

gorila mux 的一个非常好的特性是能够基于路径前缀创建子路由器，并向它们注册中间件。这意味着，如果您愿意，您可以拥有稍微不同的 HTTP 执行管道。同时，你也可以共享一些通用的中间件。

对于这个例子，我们添加了两个新的 URL`/sub/a`和`/sub/b`，每个都有自己的处理函数。为了处理这些 URL，我们添加了一个子路由器(第 16 行),并且我们创建了另一个叫做`subMiddleware`的中间件，你可以在第 41 行找到它。这个中间件只为子路由器注册(第 17 行)。这意味着只有由子路由器处理的 URL 将执行`subMiddleware`。然而，由于`loggingMiddleware`是在主路由器上注册的，子路由器将继承该配置。让我们调用这些 URL 来看看不同之处。

调用`/ishealthy`在控制台上打印以下内容:

```
2020/05/26 16:31:33 Url requested: /ishealthy
2020/05/26 16:31:33 Returning 200 - Healthy
2020/05/26 16:31:33 Request finished
```

这里没有什么新东西，这个 URL 的 HTTP 执行没有改变。意思是 HTTP 执行管道看起来像`loggingMiddleware` - > `handleIsHelthy`。

调用`/sub/a`打印如下:

```
2020/05/26 16:32:42 Url requested: /sub/a
**2020/05/26 16:32:42 Another middleware**
2020/05/26 16:32:42 Returning 200 - Healthy a
2020/05/26 16:32:42 Request finished
```

这次我们可以看到`subMiddleware`是在`loggingMiddleware`之后被调用的。意思是 HTTP 执行管道看起来像`loggingMiddleware`->-`subMiddleware`->-`handleIsHealthyA`。

最后调用`/sub/b`打印以下内容:

```
2020/05/26 16:32:50 Url requested: /sub/b
**2020/05/26 16:32:50 Another middleware**
2020/05/26 16:32:50 Returning 200 - Healthy b
2020/05/26 16:32:50 Request finished
```

与之前的行为相同，本例中的管道是`loggingMiddleware`->-`subMiddleware`->-`handleIsHealthyB`。

我们使用中间件创建了两个不同的执行管道。例如，在我们的 web 应用程序中有私有和公共部分的情况下，这是很常见的。为了解决这个问题，我们可以有一个子路由器，它封装了处理授权的逻辑，所有这些都非常明确和独立，并且只应用于我们应用程序的私有部分。我希望你会发现这种技术对你的下一个项目有用！