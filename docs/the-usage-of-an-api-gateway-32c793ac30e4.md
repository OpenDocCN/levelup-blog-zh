# API 网关的使用

> 原文：<https://levelup.gitconnected.com/the-usage-of-an-api-gateway-32c793ac30e4>

![](img/e6a90549cba7f4648da1b0eb38e33514.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

API 最简单的形式就是接收一个请求，然后发回一个响应。然而，在现实生活中，当您管理一堆 API 时，事情通常会变得复杂，您可能需要在请求到达后端 API 服务之前执行一些操作，即身份验证和授权。

**API 网关**是你在到达 API 服务器之前碰到的一个中间件。它充当客户端(可以是任何前端应用程序)和后端服务之间的反向代理。API 网关服务的服务商相当多，包括 [AWS(亚马逊 Web 服务)](https://docs.aws.amazon.com/apigateway/)、[微软 Azure](https://docs.microsoft.com/en-us/azure/architecture/microservices/design/gateway) 、[谷歌云平台](https://cloud.google.com/api-gateway)。

以下是 API Gateway 提供的一些使用案例和优势:

1)认证和授权服务

*   您可以使用 API 网关来管理和控制对某些端点的访问。网关也可以用于白名单或拒绝您的后端服务的一些特定的源 IP 地址。这是一个用 AWS API 网关实现认证和授权的例子: [API 网关资源策略示例](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-resource-policies-examples.html)。

2)你的 API 的速率限制

*   API 网关还可以用来防止 DDoS(拒绝服务)攻击，这是一种恶意操作，可以用来中断网络流量并导致客户端服务不可用。为了阻止这种情况，您可以在 API 网关的策略中限制每秒可以接受的来自相同源 IP 地址的请求数量。您还可以了解如何使用 Oracle API Gateway 实现这一目的:[限制对 API Gateway 后端的请求数量](https://docs.oracle.com/en-us/iaas/Content/APIGateway/Tasks/apigatewaylimitingbackendaccess.htm)

3)分析、记录和报告

*   API Gateway 有助于跟踪和监控客户端和后端服务之间的所有流量，因为这是唯一的入口点。这对于在发生多点故障时调试和解决问题至关重要。下面的例子演示了如何用 Google Cloud API Gateway 监控你的 API:[监控你的 API](https://cloud.google.com/api-gateway/docs/monitoring) 。

4)连接到其他服务

*   一个常见的用例是，在将 API 服务货币化时，您可能希望连接到一个计费服务。AWS 正在提供 API Gateway 的强大功能来控制第三方对 API 的使用，甚至将 API 打包到不同的付费层。您可以查看这篇关于 AWS API Gateway 如何帮助您实现 API 服务货币化的文章:[使用 API Gateway](https://aws.amazon.com/blogs/compute/monetize-your-apis-in-aws-marketplace-using-api-gateway/) 在 AWS Marketplace 中实现 API 货币化。

简而言之，API Gateway 是一个帮助您构建健壮且可伸缩的 API 的工具。它有助于管理复杂的系统，并为它们提供了单一的入口点。

请关注我关于编程、开发人员生产力和更多主题的未来文章！干杯。

[](https://dylanoh.medium.com/) [## 迪伦哦-中等

### 阅读 Dylan Oh 在媒体上的文章。软件工程师@ OpenBet 新加坡。写关于:软件开发…

dylanoh.medium.com](https://dylanoh.medium.com/)