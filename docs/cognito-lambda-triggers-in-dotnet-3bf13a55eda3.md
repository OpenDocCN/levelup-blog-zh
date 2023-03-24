# Dotnet 中的 Cognito Lambda 触发器

> 原文：<https://levelup.gitconnected.com/cognito-lambda-triggers-in-dotnet-3bf13a55eda3>

[Amazon Cognito](https://aws.amazon.com/cognito/) 是一个用户注册、登录和访问控制服务，具有许多[功能](https://aws.amazon.com/cognito/details/)。在任何严肃的解决方案中，您几乎肯定需要使用的一个特性是使用 [Lambda 触发器](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html)处理 Cognito 事件的能力。

本文提供了关于如何使用 Dotnet 和 Terraform 构建、部署、配置和处理 Cognito 触发器的一般指南和实现。

![](img/66faedfbb845e93c96913a7df7b29390.png)

Jason Dent 在 [Unsplash](https://unsplash.com/s/photos/vault?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

这个项目的[源代码](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet)包括一个完整的 Terraform 定义，用于创建一个 Cognito 用户池和 Cognito 用户池客户端 ID，但是这些细节在我以前的文章[Amazon cogni to with SMS User verification](/amazon-cognito-with-sms-user-verification-8c0d90121faa)中有更详细的解释。在本文中，重点将放在使用 Dotnet 启动和运行 Lambda 触发器需要什么。

以下触发事件深入探讨文章现已发布:

*   [预注册](https://medium.com/@oliver.schenk/cognito-lambda-triggers-in-dotnet-presignup-4c465c6f81a)

*我正在研究每种触发器类型的实现和示例，敬请关注！更多链接即将推出…*

# 先决条件

要部署基础设施和 Cognito trigger Lambda 函数，您至少需要以下内容:

*   一个 AWS 帐户，具有用于部署资源的管理员级访问凭据
*   [Dotnet 6.0 SDK](https://dotnet.microsoft.com/en-us/download) 带 [Lambda 工具](https://docs.aws.amazon.com/lambda/latest/dg/csharp-package-cli.html)
*   [地形](https://www.terraform.io/downloads)
*   (可选) [aws-vault](https://github.com/99designs/aws-vault)

# 一般提示

Amazon Cognito 文档提供了许多关于触发器的信息。您可以在自己的时间阅读详细的文档，但是在很高的层次上，以下考虑事项对于 Lambda 触发器函数非常重要:

1.  它必须在 **5 秒**内完成并响应。Cognito 将尝试 **3 次重试**，然后超时。
2.  除了自定义发送方 Lambda 触发器，Amazon Cognito 同步调用 Lambda 函数**。**
3.  **它必须附加一个基于资源的策略，即**授予 Congito 服务调用**该函数的权限。**
4.  **如果使用*托管 UI* 功能，它生成并返回的任何错误消息将对用户可见。**
5.  **事件有效载荷由一组适用于所有触发器的**公共参数**和零个或多个触发器特定参数组成。**
6.  **[**触发源**](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html#cognito-user-identity-pools-working-with-aws-lambda-trigger-sources) 参数是一个字符串，用于标识触发事件的目的或类别。**

# **触发事件**

**触发器事件是一个传递给触发器处理程序 Lambda 函数的对象，它提供事件的上下文和请求信息，您可以在逻辑中使用这些信息。**

**所有事件都有一个最小的参数集，称为[公共参数](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html#cognito-user-pools-lambda-trigger-syntax-shared)。这些如下面的代码片段所示。许多触发器会有额外的请求和响应参数。**

## **触发源**

**Cognito 可以配置为调用一个 Lambda 触发器处理函数，用于 Cognito 触发的一个或多个可用事件。换句话说，您可以分配一个函数来处理其中一个触发器，也可以将一个函数分配给多个触发器，以允许该函数处理许多不同的事件。**

**除了有不同的触发器之外，还可能有不止一个调用触发器的潜在原因。这些都有文档记录——尽管文档有点乱。Cognito 服务和文档将此称为 [*触发源*](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html#cognito-user-identity-pools-working-with-aws-lambda-trigger-sources) *。*触发源值可以通过触发事件对象中的`triggerSource`参数来确定。**

**决定采用哪种方法将取决于您的架构，但是大多数时候可以肯定地说，对于每个用户池，您可以将所有的注册、登录和相关的触发代码保存在一个 Lambda 函数中。**

**作为本文的一部分提供的示例项目采用了通过继承为每种类型的触发器源定义强类型处理程序的方法**

## **请求和响应**

**当一个记录在案的 Cognito 事件发生并且您已经为该事件配置了 Lambda 触发器处理程序时，Cognito 将调用您的函数并将一个 Cognito 事件对象传递给您的函数。**

**请求参数包含在 Cognito 事件对象的`request`字段中。根据触发源，您必须使用给定的`request`数据(如果相关)执行自定义代码，并更新`response`字段中的必要参数(如果相关)。然后必须返回整个 Cognito 事件对象。**

**AWS 没有为强类型的 Cognito 事件对象提供 Dotnet 库，因此我们需要查看`triggerSource`字段，并基于此将对象反序列化为我们可以使用的对象。文档很好地描述了基于触发源的每种类型的有效负载的结构。可以在 Dotnet 中使用的主要对象是`JsonElement`，但是我们很快会深入讨论更多的细节。**

**[Github 项目](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet)也包含了每种类型触发源的示例 JSON 负载。**

# **履行**

**可以在项目代码中找到触发器事件对象的 Dotnet 实现。它为每种类型的触发源提供了事件对象和相关处理程序的实现。**

**实现您需要的处理程序取决于您，如下所述。您可以只实现其中一个，也可以实现多个。**

## **处理器工厂**

**Lambda 函数处理程序和`CognitoEventHandlerFactory`的核心目的是在运行时用强类型事件上下文找到并实例化适当的处理程序，并运行处理程序逻辑。**

**要实现 Cognito Lambda 触发器，您需要做的就是找到适当的处理程序类，并在其中覆盖`HandleTriggerEvent`方法，编写您的自定义逻辑，在`TriggerEvent.Response`中设置任何适当的值(如果适用)，最后使用`base.HandletriggerEvent()`返回。**

**这将自动将触发事件对象序列化回一个`JsonElement`并返回它，以便 Lambda 函数可以将其返回给 Cognito。**

**下面是一个`PreSignUp_PreSignUp`触发处理器的例子。**

**如果您的处理程序方法需要`async/await`，您可以覆盖`HandleTriggerEventAsync`方法。**

**如果由于某种原因处理程序不能被加载(也许 AWS 突然发明了一个新的触发源)，后备是`NoActionHandler`。这只是不加修改地返回原始事件对象。**

## **通用参数**

**通用参数作为`CognitoTriggerEventBase`实施提供。注意，这个类不是抽象的或泛型的，因为它用于将 Lambda 事件上下文反序列化为强类型对象。然后在逻辑中使用它，根据与`TriggerSource`的匹配，在运行时找到正确的处理程序。**

## **请求和响应参数**

**一旦基于触发源动态地确定了处理程序的类型，就加载了处理程序的正确实现。每个处理程序用指定的触发事件实现`CognitoTriggerHander<T>`。**

**这个触发事件又是`CognitoTriggerEvent<TRequest, TResponse>`的实现，其中`TRequest`和`TResponse`是每个处理程序类型的强类型请求和响应对象。**

***总之，您现在有了一个处理器，它确切地知道每个触发源的请求和响应的类型。相当酷！***

**事实上，当您实现一个处理程序时，您可以简单地使用`TriggerEvent.Request`访问请求对象，使用`TriggerEvent.Response`访问响应对象，并且所有的参数都是可用的和强类型的。**

# **地形基础设施**

**在基础设施方面，您可以使用云形成或地形。我已经在示例项目中使用了 Terraform。**

*****注意:*** *我尝试使用* [*无服务器*](http://serverless.com) *框架来实现这一点，但是我在部署时遇到了问题。由于一些我从未解决的权限错误，无服务器产生的云结构对我不起作用。***

**你需要做的主要事情是:**

1.  **创建 Lambda zip 包。(请参见部署脚本)**
2.  **定义 KMS 关键块。**
3.  **定义一个 Lambda 功能块。**
4.  **在 Cognito 用户池中定义触发器。**
5.  **为 Lambda 调用和 KMS 键的使用定义权限。**

**在`aws_cognito_user_pool`配置中，您会发现`lambda_config`块，在这里触发器被设置为 are。**

**注意，从技术上讲，KMS 键只在[自定义发送器触发](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-custom-sender-triggers.html)时需要，所以如果你没有实现`custom_email_sender`或`custom_sms_sender`，那么你就不需要 KMS 键。**

**`KEY_ID`环境变量被传递给 Lambda 函数，这样，如果您需要解密自定义发送方触发器的密码，就可以使用它。**

# **部署脚本**

**示例项目中包含一个部署 bash 脚本。它将负责构建、打包和部署到 AWS。要部署此项目，您可以使用以下命令之一，具体取决于您是否使用 aws-vault。**

```
# without aws-vault
# you'll need to have appropriate credentials in your environment
# -----------------
./deploy.sh# with aws-vault
# replace with your own profile name
# -----------------
./deploy.sh -p <aws-vault-profile-name>
```

**还有一个`destroy.sh`脚本可以拆除 AWS 基础设施。**

# **触发器实现**

**请关注此空间中关于如何处理每种类型的触发源的解释和示例的文章。**

**以下触发事件深入探讨文章现已发布:**

*   **[预注册](https://medium.com/@oliver.schenk/cognito-lambda-triggers-in-dotnet-presignup-4c465c6f81a)**

**关注我或订阅以接收这些文章发布时的通知。**

**感谢您的宝贵时间！**

# **分级编码**

**感谢您成为我们社区的一员！在你离开之前:**

*   **👏为故事鼓掌，跟着作者走👉**
*   **📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容**
*   **🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)**

**🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)**