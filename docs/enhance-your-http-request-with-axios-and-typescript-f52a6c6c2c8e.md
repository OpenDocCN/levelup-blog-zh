# 使用 Axios 和 TypeScript 增强您的 HTTP 请求

> 原文：<https://levelup.gitconnected.com/enhance-your-http-request-with-axios-and-typescript-f52a6c6c2c8e>

## 像专家一样使用 OOP 制作 HTTP-request

![](img/fd1ea600be8436bec09fa285419e7b90.png)

由[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

你好，世界！让我分享一些建议，告诉你如何让你的日常生活变得更有力量。

让我们介绍一些基础知识，这样我们就知道我们在同一页上。在 JavaScript/TypeScript 中，无论我们谈论的是客户端还是服务器端，我们都可以发出 HTTP 请求。

对于客户端，你可以使用老派的`XMLHttpRequest`(请不要这么做)，或者基于`Promises`的现代`fetch`。

对于服务器端，Node.js 模块`https`总是为您提供。

然而，我们大多数人都使用图书馆，其中最受欢迎的是`[axios](https://www.npmjs.com/package/axios)`。

# Axios 实例

实现更清晰代码的第一步是一个`axios`的实例。这有助于我们遵循 DRY 原则，因为我们使用了一个`baseUrl`，在多个请求之间共享公共头，并附加了一个`Authorization`头。

让事情变得更好永远都不晚，或者至少试着去做。

# HTTP 客户端

在最开始，我们需要一些基础`http-client`，它将被扩展以创建所需的`API`。

它将是一个抽象类(防止创建这个基类的实例)，它的构造函数将创建一个`axios-instance`。在我们的例子中，我们从构造函数中获取基本 URL。`axios.create`的结果将被保存到该类的受保护变量中(仅对其继承者可见)。

在 99%的情况下，我必须从响应中析构`data`。所以，让我们创建一个`interceptor`:

有很多新代码。我们来讨论一下上面是怎么回事。

在构造函数中，我们运行私有方法`_initializeResponseInterceptor`，它将一个`interceptor`添加到我们的响应中。

这个`interceptor`需要两次回调:`_handleResponse`和`_handleError`。

`_handleError`非常清楚——它只是接受一个错误并转发它。

来自响应的`_handleResponse`析构`data`。

因为我们使用 TypeScript，所以我们必须帮助它理解正在发生的事情。这就是为什么我们在文件的顶部声明模块`axios`。这样，当我们将响应作为纯粹的`data`使用时，TypeScript 不会抱怨。

# 真实世界的例子

是时候测试我们的解决方案了。我们有一些很棒的 API，必须发出 GET 和 POST 请求。我们创建了一个`HttpClient`的实例，并在`super`中传递 URL，描述我们的请求:

此外，我们可以创建一个受保护的`MainApi`版本，在这里我们执行所有带有`Authorization`头的敏感请求:

从上面的代码中可以看出，我们添加了一个新方法`_initializeRequestInterceptor`。使用这种方法，每个请求的头中都有`Authorization`键。

# 摘要

我希望你能找到新的有用的东西。当然，所有这些东西都可以用函数和纯`axios.create`来代替，但从我的角度来看，它会帮助你编写更干净的代码，并用一个`API`将所有工作集中在一个地方。

[在这里](/use-case-of-singleton-with-axios-and-typescript-da564e76296)你可以找到当前文章的 [**第二部分**](/use-case-of-singleton-with-axios-and-typescript-da564e76296) 。