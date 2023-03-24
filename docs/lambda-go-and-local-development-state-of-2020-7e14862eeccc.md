# Lambda、Go 和地方发展——2020 年状况🌖

> 原文：<https://levelup.gitconnected.com/lambda-go-and-local-development-state-of-2020-7e14862eeccc>

![](img/b7f7e24cea36bbdab909f71aa2e20b7b.png)

当地开发人员的感受——照片由 [David Kovalenko](https://unsplash.com/@davidkovalenkoo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

欢迎光临！我最近一直在重新评估我的 AWS Lambda 和 Go local 开发。我学到了很多，也尝试了一些不同的方法，所以我想与更广泛的社区分享。这个故事实际上是从想在本地运行我的 Go Lambda 函数以及 AWS Cognito 等集成开始的。

我的本地开发需求是:

*   它需要`hot reloading`又名**它需要快**。理想情况下，如此之快，你会认为这是常规的本地围棋开发
*   来自`[cognito](https://aws.amazon.com/cognito/)`的 JWT 代币需要工作。这是因为应用程序本身并不解析 JWT 令牌，但是 APIGateway 将一个`[APIGatewayProxyRequest](https://godoc.org/github.com/aws/aws-lambda-go/events#APIGatewayProxyRequest)`对象附加到请求的上下文中。这个`APIGatewayProxyRequest`充满了重要的东西，比如`claims`地图，它告诉我们哪个用户正在请求资源等等。

这从一开始看起来并不太难，但它具有欺骗性。

## 挑战 1

首先，也是最重要的，我们需要可靠地在本地运行我们的 Lambda 代码。我尝试和考虑的主要选项是:

*   [无服务器离线](https://github.com/dherault/serverless-offline)
*   [AWS Sam 本地](https://aws.amazon.com/serverless/sam/)
*   本地运行二进制/ [Lambda Docker 包装器](https://github.com/lambci/docker-lambda)

# 无服务器框架和离线插件

[无服务器](https://serverless.com/)是一个非常可靠的部署工具，我在项目中使用它将我的 Lambda 代码部署到 AWS。我对部署的过程非常满意。但是对本地开发的支持来自于一个插件`[serverless offline](https://github.com/dherault/serverless-offline)`。

**速度**💩💩:插件本身很棒，工作正常，但是它太慢了。我的简单网络服务器需要 20-50 秒来完成一个请求(在一台像样的 2017 年 3.1ghz 16gb macbook pro 上)。似乎每个请求都需要重建一个 docker 容器。不幸的是，我无法找到一种方法来安装卷，如果有一种方法，请让我知道！

![](img/936174a96b8940ceb55de766b2229ebb.png)

无服务器框架，一个长请求

**环境**🔥:它按照预期处理`cognito`标记👍就像真实的 Lambda 环境一样。

**额外工作**🔥:我已经用它来部署我的 lambdas，所以没有额外的 yaml 文件需要设置。

# AWS Sam

[官方 AWS 解决方案](https://aws.amazon.com/serverless/sam/)。大约一年前，当我刚开始我的项目时，我访问过这个网站，想看看它是否符合我所有的条件。我想使用官方的解决方案，但是它没有很好地与`cognito`集成，而且它看起来仍然过于复杂，并且仍然不支持本地的`cognito`👎。

**速度**👍山姆跑得很快。一个请求通常需要大约 3 秒钟。它挂载卷，从而热重装`go`二进制文件。所以我们只需要确保构建我们的二进制文件也一样快。

**环境**👍:作为官方解决方案，我希望它能处理`cognito`令牌。但可惜不是，所以有点屎。

**额外工作**💩:首先我们需要一个额外的`yaml`文件来声明路线。但是当您想要解析那个`cognito`标记时，真正令人头痛的事情就开始了。这意味着需要一些应用程序代码来处理基于下面的`cognito`标记创建正确的上下文。

# 滚动你自己的

海上人的方法，这里有几个选择。您可以使用从 [docker-lambda](https://github.com/lambci/docker-lambda) 提供的 docker 环境。这很棒，但也是引擎盖下使用的相同图像。我看不出这有什么好处。
你也可以尝试完全独立于任何框架/docker 映像来运行你的`go`函数，并省去中间人。这意味着您必须在代码中替换`lambda.Start`，并且在本地运行时还要处理`cognito`令牌。

[Apex 网关](https://github.com/apex/gateway)和 [algnhsa](https://github.com/akrylysov/algnhsa) 都处理替换`lambda.Start`并且你得到使用`net/http`处理程序签名的好处，该签名与[列表](https://godoc.org/github.com/aws/aws-lambda-go/lambda#Start)中的某些内容相对应。

但是您需要处理解析那个`cognito`标记。唉，和`AWS Sam`的情况一样，您必须编写一个中间件函数，用令牌中正确的`claims`手动填充`APIGatewayRequestProxy`。然后将`APIGatewayRequestProxy`重新附加到请求上，并沿着中间件链传递。

快速了解我如何在本地处理访问令牌

尽管处理了问题，但它是裸的，所以速度很快。但是额外的代码和 Lambda 环境的缺乏并不是我们要寻找的信心助推器。
**速度🔥，环境💩，额外工作💩。**

# 重新编译/监视工具

我还没有提到的是我们实际上是如何处理实时重载的。是一种编译语言，所以每次我们做改变时，我们都需要重新编译代码，确保我们运行的是二进制代码。我一直在寻找的工具有:

*   [实现](https://github.com/oxequa/realize)。非常受欢迎的 go task runner，可能是因为它有一个(巧妙的)网络用户界面。我发现配置过于复杂，尽管围绕做了[的工作，但最后`go mod`还是有很多问题。它在 windows 环境下也有](https://github.com/oxequa/realize/issues/217#issuecomment-459198990)[问题](https://github.com/oxequa/realize/issues/19)。
*   [Modd](https://github.com/cortesi/modd) 。一个非常容易使用和灵活的工具。跨平台。
*   [反射](https://github.com/cespare/reflex)。另一个简单易用的工具。可能只适用于 linux 和 mac*

使用这些工具重新编译每一个 go 二进制文件是一个有点棘手的过程。您会遇到大规模的问题，因为不仅需要在处理程序改变时重新编译它们，还需要像 utils /数据库模型这样的支持包。

下面是一个使用 reflex 的例子，它可以在任何 Go 文件改变时重建一切。

使用 reflex 的 makefile 在 github 中打开以查看完整的要点和改进的 buildP！

大约需要 30 秒，来建立我的 20 个函数。

改进后的`make buildP`需要大约 5 秒，来构建大约 20 个相同的函数，但是有明显的 CPU 负载。

另一个使用`modd`的例子，它不太动态，但你可以在速度上弥补。它仅在处理程序发生变化时才重新构建单独的处理程序。或者当任何 util/helper 代码改变时，它重新构建一切。

modd.conf 最终示例—在 github 中打开以查看完整的要点！

我认为这是一个很好的交易。我很乐意看到人们提出的任何其他解决方案，所以请在下面留下评论！

# TLDR；服务摘要

结果出来了

> 看起来在 2020 年，它仍然会是一个工具的混搭。

我不会为了部署而放弃无服务器。他们的插件和对授权者的一流支持意味着我不能离开。但是我将使用 AWS Sam 在本地运行 API，使用一些 hackney 应用程序代码，用一些重新编译脚本进行修补，以不断地根据更改进行重建！

感谢您的阅读，如果您有更好的解决方案，请务必告诉我！