# 设计基于令牌的认证系统流程

> 原文：<https://levelup.gitconnected.com/designing-token-based-authentication-system-flow-868893870e03>

今天我学习了如何使用 JWT 设计一个基于令牌的认证系统，虽然我仍在学习设计一个系统，但我希望这篇文章能帮助你。

![](img/01bdfffeb1b4cafdca32a462fbbcbee0.png)

由[马科斯·迈尔](https://unsplash.com/@mmayyer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是基于令牌的身份验证？

> 基于令牌的身份验证系统背后的一般概念很简单。允许用户输入他们的用户名和密码，以便获得允许他们获取特定资源的令牌，而无需使用他们的用户名和密码。一旦获得他们的令牌，用户就可以向远程站点提供令牌，该令牌提供在一段时间内对特定资源的访问。

至少那是由 [**w3 org**](https://www.w3.org/2001/sw/Europe/events/foaf-galway/papers/fp/token_based_authentication/) 编写的，而 w3 所讲的 token 会以[**JSON Web Token**](https://jwt.io)**【JWT】**等标准格式存储，以帮助系统认证客户端和用户。这里 可以阅读 JWT [**的说明书。**](https://tools.ietf.org/html/rfc7519)

# 用例

我们希望创建一个具有专用身份验证服务的系统，这样，如果我们有许多服务，我们就不必在每个服务上存储客户端凭据。

![](img/e825eec090a99bbbbdbc69344f453395.png)

使用案例说明

认证服务器将生成`access token`和`refresh token`，以便替换要在另一个服务器上认证的用户凭证。

# 认证服务器数据库

为了对使用资源服务器的用户进行身份验证，我们可以像这样存储凭证。

![](img/8d8cc6901929eed365939db3f49947f1.png)

认证服务器数据库

**users** 表将存储我们的用户凭证和他们的 **refresh_token** ，如果要在内存中存储 refresh_token，存储 refresh_token 可能是**可选的**，因为 **refresh_token** 有一个**到期时间，而**不会永远存储**。**

****受众**表将存储我们的用户受众，假设我们有 2 个服务器/资源，但我们只是希望一个特定的用户只能访问 1 个特定的服务器/资源，这样用户就只有 1 个受众。该字段与 [JWT aud 规格](https://tools.ietf.org/html/rfc7519#section-4.1.3)相关。不过这是可选的。如果您使用 **HS256** 对令牌进行签名，此表将有 **secret_key** 用于**验证**由认证服务器共享给观众的令牌。但是，如果您使用 **RS256** ，您将不再需要该字段，因为它使用非对称密钥来签署令牌，**私有密钥** **将由认证服务器持有，**公共密钥**将与受众共享。建议使用 RS256。****

# **认证服务器生成访问令牌**

****JWT** 必须是 URL 友好的令牌，并且具有较短的生命周期，以防止令牌在一定时间后可以重复使用，并避免令牌被盗。默认情况下，它使用 base64 格式。JWT 由三部分组成。每个部分都是 base64 编码的字符串，用点分隔。该令牌将由身份验证服务器生成，并将保存在客户机上，由客户机发送到资源服务器。**

```
**header**.**payload**.**signature**
```

**例如**

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

****割台**部分正在使用[何塞割台](https://tools.ietf.org/html/rfc7519#section-5)。标题最小规格将由`alg`和`type`构成**

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

****alg** 将定义用于验证令牌的签名方法，如**认证服务器数据库**部分所述。这里可以找到常用算法[。](https://tools.ietf.org/html/rfc7518#section-3.1)**

**[**typ**](https://tools.ietf.org/html/rfc7519#section-5.1)**将定义令牌的类型，在本例中为`JWT`。****

******有效载荷**部分是认证服务器要共享给用户的数据段。你可以在这里定义任何你想分享的东西。**有效载荷**有可选的[规格](https://tools.ietf.org/html/rfc7519#section-4.1)如 **aud、iss、exp** 等。****

```
**{
  "id": "4c886feb-d2ce-4f2a-aeb3-55aeab4d970f", // user id
  "email": "[client@server.com](mailto:client@server.com)", // user email
  "iss": "[https://authentication.server.com](https://authentication.server.com)", // issuance URI
  "iat": 1585432621, // issuance at
  "exp": 1589032621, // expired at
  "aud": ["[https://resource.server.com](https://resource.server.com)"], // list of audience
}**
```

****您可以向有效负载添加更多属性。****

******签名**部分取决于你的**头算法**。如果我们使用 **HSA256，**这意味着我们将使用 SHA256 加密 HMAC 的令牌。这一部分由前面两部分组成。****

```
**HMACSHA256(base64(header) + "." + base64(payload), secret_key)**
```

****通过这三个部分，JWT 可以保证没有人可以改变令牌，除非他们知道**密钥**。由于**报头/有效载荷**部分被修改，签名无法验证。****

# ****认证服务器生成刷新令牌****

******刷新令牌**是仅由客户端和认证服务器确认的令牌。一旦访问令牌过期，该令牌将用于生成**访问令牌**。因此刷新令牌比访问令牌具有更长的寿命。如果刷新令牌过期，用户将无法再生成访问令牌，这将强制用户重新登录。您可以将该令牌存储在缓存等具有到期时间的存储中，也可以将其存储在数据库等永久卷中。这个令牌没有特定的格式，您可以根据您的使用情况调整格式，并且您可以通过使用 UUID 来简单地生成刷新令牌。****

# ****登录流程****

****![](img/ed1b5fbc72db647d07d25c27429709b1.png)****

****登录流程****

****很明显，访问令牌必须返回给客户端，但是刷新令牌呢？****

******刷新令牌**将被设置为[仅 HTTP cookie](https://owasp.org/www-community/HttpOnly)，因此客户端/入侵者没有机会修改该值。现在，客户机拥有了访问资源服务器的访问令牌。****

# ****访问资源流****

****![](img/c972693428ced56cd0f5d4022e6920e6.png)****

****访问资源流****

****通过验证 ACCESS_TOKEN，服务器将使用以下步骤开始解码 ACCESS_TOKEN:****

1.  ****按点分割访问令牌****
2.  ****解码第一(报头)部分。检查是 **alg** 和 **type** 。****
3.  ****使用定义的算法，尝试使用共享的**定义的 secret_key** 或**私钥**对令牌进行重新签名，从而生成**签名部分**。****
4.  ****检查生成的**签名部分是否等于第三(签名)部分**。****
5.  ****如果是不同的返回未授权响应。****
6.  ****否则，解码第二(有效载荷)部分，检查 JWT 声明是否存在，如 **iss** 、 **aud** 、 **exp** 等。****
7.  ****如果 JWT 声明不满足条件，则返回一个未经授权的响应****
8.  ****否则，JWT 被授权。****

# ****刷新访问令牌流****

****刷新访问令牌不仅用于在访问令牌过期时生成访问令牌，还可以使用此流程来延长刷新令牌的寿命。因为刷新令牌存储在 HTTP Only cookie 上，所以您不需要发送任何东西来访问这个端点，但是这取决于您如何实现这个流。****

****![](img/b90fd67a1057bbcff918c5e2831994a1.png)****

****刷新访问令牌流****

# ****结论****

****根据您的用例，这个系统流程可能会以不同的方式实现。您可以使用 auth0 或 SSO 来实现身份验证，它也使用基于令牌的身份验证，因此您不需要自己创建身份验证服务器。****

****感谢您的阅读！****

****[](https://jwt.io/) [## JWT。超正析象管(Image Orthicon)

### JSON Web Token (JWT)是一种简洁的、URL 安全的方式，用于表示要在双方之间传输的声明。的…

jwt.io](https://jwt.io/)  [## RFC 7519 - JSON Web 令牌(JWT)

### 互联网工程任务组(IETF) M. Jones 请求评论:7519 微软类别:标准跟踪 J…

tools.ietf.org](https://tools.ietf.org/html/rfc7519) [](https://owasp.org/www-community/HttpOnly) [## HttpOnly

### 本节的目标是介绍、讨论和提供针对 HttpOnly 的特定于语言的缓解技术

owasp.org](https://owasp.org/www-community/HttpOnly)  [## 面向开发人员和分析师的免费数据库设计器

### 快速简单的免费工具，帮助您绘制您的数据库关系图，并使用简单的 DSL 快速流动…

dbdiagram.io](https://dbdiagram.io/) [](https://sequencediagram.org/) [## SequenceDiagram.org-UML 序列图在线工具

### SequenceDiagram.org 是一个创建 UML 序列图的在线工具/软件。成立于 2014 年，宗旨是…

sequencediagram.org](https://sequencediagram.org/) [](https://www.diagrameditor.com/) [## 免费在线图表编辑器

### 创建在线图表的免费编辑器。使用我们的图表编辑器制作流程图，UML 图，ER 图，网络…

www.diagrameditor.com](https://www.diagrameditor.com/)****