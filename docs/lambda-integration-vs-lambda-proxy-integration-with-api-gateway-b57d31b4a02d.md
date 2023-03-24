# Lambda 集成和 Lambda 代理与 API 网关的集成

> 原文：<https://levelup.gitconnected.com/lambda-integration-vs-lambda-proxy-integration-with-api-gateway-b57d31b4a02d>

## 以及如何使用 AWS CDK 来完成

![](img/f7b438cf21800b00059ce76682a25892.png)

照片由[布鲁斯·马斯](https://unsplash.com/ja/@brucemars?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/confusion-technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

API gateway 是向世界开放 Lambda 的最流行和最强大的方式。

但是有两种方法可以将您的 Lambda 与 API 网关集成。

1.  λ代理集成
2.  Lambda 集成或自定义集成

Lambda 代理集成和 Lambda 非代理集成之间的区别可能会让人混淆。

在这篇文章中，我们将会看到如何使用 AWS CDK 来配置这两者，以及它们之间的区别。

# **什么是** Lambda **代理集成？**

这是在 Lambda 函数和 API 网关之间创建连接的推荐方式。

*   请求按原样传递给 lambda 函数。
*   lambda 函数的响应按原样传递给客户端。

图表可以是这样的，

`client`->-`API Gateway (no transformation)`->-`Lambda Function`

这是要了解的主要内容。API 网关不对传入的请求或传出的响应进行任何转换或修改。

这意味着以正确的格式发送响应和状态代码是您(lambda 函数)的工作。

具体格式是

proxyresponse.json

所以你的 lambda 函数必须返回这种类型的响应。此外，您可以用以下格式发送错误。

错误-代理-响应. json

所以在代理集成中，你的 lambda 负责一切。API 网关不干涉这些。

## 代理集成的优势

*   这很容易设置。所以你可以做快速原型。
*   轻松控制您的状态代码和自定义标题。

## 代理集成的缺点

*   生成文档很难。
*   一切都在代码中，所以它与 API 网关紧密耦合。

# 什么是 Lambda 自定义集成？

在非代理集成中，您可以设置 API 网关，以便它对请求和响应进行一些转换。

`client`->-`API Gateway (some transformation)`->-`Lambda Function`

这带来了很多机会。举个例子，

*   在将请求传递给 lambda 函数之前，可以进行许多转换。也许添加一些特殊的标题或额外的信息。
*   在将响应传递回客户端之前，您可以进行许多转换。例如，您可以隐藏从 lambda 函数返回的一些字段。

API 网关使用 VTL (Velocity 模板语言)来转换请求和响应。

在这种类型的集成中，您的 lambda 不一定要返回任何特定的响应格式。

## Lambda 定制集成的优势

*   这个很厉害。你完全可以控制。
*   lambda 代码与 API 网关分离。
*   文档生成变得非常容易。

## Lambda 自定义集成的缺点

*   正确设置非常耗时。
*   你需要知道 VTL 语。

# 哪个更好？

AWS 建议对 Lambda 函数使用 **Lambda 代理集成**。还有，这是最快的做事方式，感觉很本土。

所以在大多数情况下，你应该使用 **Lambda 代理集成。**

然而，如果你有强烈的需求，你可以选择 **Lambda 定制集成。**一个这样的例子可能是生成文档的需求。

现在，让我们看看如何使用 AWS CDK 来完成这两项工作。

## AWS 代理与 AWS CDK 的集成

下面是使用代理集成将 lambda 函数与 API 网关连接起来的代码片段。

代理集成. ts

使用自定义集成的 AWS Lambda 集成

今天到此为止。祝您愉快！完整的代码可以在[这里](https://github.com/Mohammad-Faisal/cdk-v2-template-project/blob/master/src/constructs/SecureRestApi.ts)找到

```
You can reach out to me via [**LinkedIN**](https://www.linkedin.com/in/56faisal/) or my [**Personal Website**](https://www.mohammadfaisal.dev/blog)
```

## 资源:

*   [无代理集成](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-custom-integrations.html)
*   [代理集成](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html)