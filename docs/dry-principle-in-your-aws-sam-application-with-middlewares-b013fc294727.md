# 你的 AWS SAM 应用中的 DRY 原则与中间件

> 原文：<https://levelup.gitconnected.com/dry-principle-in-your-aws-sam-application-with-middlewares-b013fc294727>

## 让您的 lambda 架构更上一层楼

![](img/145fe490bc34d248faeb5ca3899a42ef.png)

来自 [Pexels](https://www.pexels.com/photo/female-software-engineer-coding-on-computer-3861972/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[this is 工程](https://www.pexels.com/@thisisengineering?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)摄影

AWS Lambda 在无服务器领域非常受欢迎。能够编写代码并且不用担心任何基础设施真的很棒。

AWS SAM(无服务器应用程序模型)是一个只用 lambda 编写完整后端应用程序的好方法。这个想法是，你只需编写函数并在代码中定义基础设施。

但是这造成了大量的逻辑重复，因为每个函数都是单独编写的。但是我们能改善我们的建筑吗？让我们来了解一下！

# Lambda 的问题

这个世界上没有什么是完美的。和 lambda 没有什么不同。当我们使用像 express 这样的后端 nodejs 框架时，我们可以在一个地方处理一些常见的任务。喜欢

*   传入请求验证
*   错误处理
*   记录

但是在 aws lambda 中，我们没有这个特权。我们使用的每个 lambda 都具有以下结构

λ. ts

这是重复和无聊的。不如我们找到一种方法来处理这个问题，并改善我们的 lambda 架构？

# Lambda 中间件来了

中间件是 nodejs 程序员非常熟悉的一个概念。基本上，它可以拦截每一个进来的请求。这对于解决共同的问题是完美的。

[lambda-middleware](https://dbartholomae.github.io/lambda-middleware/) 是一个中间件集合，可以与您现有的 lambda 架构一起使用。

# 使用单一中间件

错误处理是任何应用程序的关键部分。我们可以利用已经编写好的错误处理中间件。

首先，安装依赖项。

```
npm i @lambda-middleware/errorHandler
```

然后像下面这样使用这个中间件

错误处理程序演示

使用这个中间件使您不必在应用程序中使用任何类型的 try/catch 块。这是巨大的！

# 使用多个中间件

现在，正如你所看到的，只使用一个中间件，我们就改进了代码。假设我们想要引入请求验证。

通常在 AWS Lambda 中，请求体出现在`event.body`中。我们通常解析请求，然后手动检查我们想要的参数是否已经到达，如下所示

但是，如果我们可以做同样的事情，但是以一种更加声明性的方式呢？

让我们首先创建一个新的请求类

在这个类中，我们使用著名的[类验证器](https://github.com/typestack/class-validator)来修饰我们的请求参数。这样，我们将如何使用我们的请求对象和所有东西就更清楚了。

然后安装以下中间件依赖项

```
npm i @lambda-middleware/class-validator
```

然后在 lambda 中使用它，如下所示

类验证器演示程序

# 使用多个中间件

现在我们已经看到了如何通过引入中间件来改进我们的代码。如果我们想同时使用 2 个中间件呢？

嗯，有另一个名为 [compose](https://dbartholomae.github.io/lambda-middleware/packages/compose/) 的包就是为了做这个。这个想法是我们得到一个名为`composeHandler`的函数，并用它来聚合多个 lambda 中间件。

这就是我们如何使用多个中间件。

# 进一步改善这一点！

现在让我们假设你正在编写数百个 lambda 函数。从代码中，我们仍然可以看到我们在每个中间件内部调用相同的`compose`函数。

我们能减少吗？让我们首先创建一个将创建 lambda 函数的效用函数。

createLambdaHandler.ts

在这个函数中，我们传递两个东西。第一个参数是由`class-validator`使用的 lambda 的请求主体的类型，第二个参数是 lambda 的实际业务逻辑。

然后我们调用`executeBusinessLogic`内部的业务逻辑来调用实际的逻辑并返回响应。

现在我们可以使用这个函数来创建任何我们想要的 lambda，而不需要复制代码。

应用程序

# 最后的想法

老实说，发现我们可以使用中间件改变了我的游戏。它将代码质量提高到了另一个水平。

你可以用这个概念做很多事情。例如，您也可以编写自己的中间件。因为归根结底它们只是函数。所以去吧！

今天到此为止。祝您愉快！:D

**通过** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) 联系我