# 使用 Axum 框架和自定义错误模型处理 Rest APIs 中的错误

> 原文：<https://levelup.gitconnected.com/error-handling-in-rest-apis-using-axum-framework-and-custom-error-model-d91d0343c7df>

![](img/e82000f933d34ec33a3b50f1bbdd3fc9.png)

本文讨论了使用 Axum 框架和在 Rust 中编写 REST APIs 时的错误处理策略。虽然 Axum 是一个写得非常好的框架，并且有很好的范例，但是作为程序员，我们有责任在框架内优雅而有意义地处理错误。

我们将研究两种不同的方法，并在此基础上构建一个错误处理模型，每个人都可以使用该模型来共享和编写高质量的处理程序，并处理好错误。

# 通用 API 错误处理

Axum 提供了一种非常简单的错误处理方式。需要理解的一个原则是，如果出现错误，应用程序不会崩溃。所有错误必须是`Infallible` 。

*简单来说，所有的错误都是可以通过 HTTP 渲染的响应。*

就像我们将一个 JSON 对象表示为一个响应一样，我们可以将我们的错误建模为可以提供给客户的对象——带有正确的响应代码。在这篇文章中，我们将从两个不同的角度来看待这个问题。带有字符串错误的简单状态响应。为了简单起见，我们将从文本响应开始，它可以由 AXUM 内置的`StatusCode`错误呈现器来呈现。

# 项目设置

让我们为这个演示创建一个新的应用程序。

```
cargo new axum-error-handling-demo
```

添加以下依赖项，开始编写我们的 API。

我们将添加一个简单的处理程序，它需要一个查询参数，并在我们的响应中返回相同的参数。这就像一个添加了查询参数值的 hello world。下面是我们的应用程序定义，它在`/hello`呈现这个端点

# 调用我们的基本 API

有了这个设置，我们可以运行我们的服务器(`cargo run`，并在我们的本地机器上测试它。

让我们尝试使用下面的端点来调用我们的 API。

```
[http://localhost:3000/hello?name=test](http://localhost:3000/hello?name=test)
```

我们会看到有我们名字的回复。

现在，尝试在不传递查询参数的情况下调用这个 API。我们看到现在有了以下响应。

```
[http://localhost:3000/hello](http://localhost:3000/hello)
```

我们看到的反应是

```
Failed to deserialize query string: missing field `name`
```

为什么我们会看到这个？在一个正确的实现规范中，我们应该能够看到一个验证错误消息，说明这个字段是必需的。

但是**上面的消息暴露了应用程序实现的细节，这根本不是一个好的实践。**

# 解决办法

要解决这个问题，我们可以使用一个简单的方法。我们可以让我们的结构接受一个`Option<String>`，而不是一个字符串参数。这样，如果没有值，我们就不会得到包含内部细节的错误消息。

下一步是*对输入执行验证，以确保我们可以向客户发送定制的错误响应。*

请在下面找到更新的结构:

现在尝试在没有查询参数的情况下调用同一个端点。您应该会看到一条很好的错误消息，告诉您可能出了什么问题。

```
http://localhost:3000/hello 
Required Parameter name is missing within the request.
```

# Json 错误

到目前为止，我们已经看到了如何在 Axum 框架中将错误建模为简单的字符串。

由于大多数 REST APIs 都是作为 JSON APIs 编写的，所以让我们看看如何将错误建模为 JSON 格式的响应。

为了将错误建模为 JSON，我们需要定义我们的应用程序错误模型。我们将创建一个新的`ApiError`对象，它将作为根错误。在其中，我们将存储 http 状态代码，并添加一些助手方法来初始化错误。我们还使用了一个库`thiserror`,帮助我们在 Rust 中轻松创建自定义错误。

# 将错误转换为响应

正如我们在第一部分中看到的，错误只是对 API 中某些错误的一种响应方式。它不应该使我们的程序崩溃，但应该为客户提供一个有意义的状态，说明问题可能是什么。当使用自定义结构时，我们有责任将它们转换成正确的响应。

作为一个框架，axum 使得处理错误变得非常简单。如果调用成功，您可以把它想象成另一个 JSON 响应。此外，我们将确保调整 Http 响应代码，以便客户端可以采取必要的措施。

为了确保 axum 能够将`ApiError`理解为错误响应类型，我们将尝试为我们的结构实现一个特征`IntoResponse`。这告诉 axum 这是一个有效的错误类型。该错误中的任何有效负载都将使用 Json 序列化转换为 HttpResponse。

下面是我们的结构`ApiError`的实现。

您可以看到，我们解析了与错误相关的状态代码，并将我们的结构序列化为响应。

通过这种方式，我们的用户现在可以以一种良好的 json 格式预览所有的错误。让我们实现一个处理程序并尝试一下。注意我们的 API 的方法签名是如何变化的。

# 测试 API

我们可以用一个有效的查询参数重复前面的调用，我们应该会看到一个新的响应和正确的结果。

```
[http://localhost:3000/hello?name=test](http://localhost:3000/hello?name=test){ "greeting": "hello", "name": "test" }
```

现在让我们在没有查询参数的情况下尝试一下。

```
[http://localhost:3000/hello?name=test](http://localhost:3000/hello?name=test){
    "status_code": 400,
    "errors": [
        "Required Parameter name is missing within the request."
    ]
}
```

# 结论

你在这篇文章中看到的是一个非常简单，但功能强大的技术，用于 Rust 中 Axum 框架的错误处理。我们注意到，我们的处理程序定义本身已经简化，我们的`ApiError`结构确保它知道如何向用户发送响应。最初设置这个项目可能很麻烦，但是一旦设置好，应用程序中的所有错误处理无疑都是标准化的。这个 ApiError 结构甚至可以在一个板条箱中分开，并且可以在多个 Axum 项目中用于错误处理。

*原载于 2022 年 10 月 10 日*[*【https://shanmukhsista.com】*](https://shanmukhsista.com/posts/technology/apis/error-handling-in-rest-apis-using-axum-framework-and-custom-error-model-rest-apis/)*。*