# 承载令牌认证和授权

> 原文：<https://levelup.gitconnected.com/bearer-token-authentication-and-authorization-6e4d16890833>

## 编程；编排

![](img/d55bb86910ff0dac2568bdeaf33e419a.png)

詹姆斯·哈里逊在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我有机会将无记名令牌认证集成到我们的系统中。当我开始从事这项工作时，我做了一些研究来了解基本概念和工作流程。

我发现理解所有必要的细节很有挑战性，因为信息分散在互联网上。

这个故事在一个地方记录了我所有的研究，以方便我的读者。

# 基本概念

![](img/0af395f83c1f5c30ff71ed448be869d8.png)

图 1 —访问和刷新令牌

## 访问令牌

访问令牌是用于访问受保护资源的承载令牌(参见图 1)。然而，客户端通过提供有效的凭证来获得访问令牌。

出于安全原因，访问令牌是短暂的。我们使用 15 分钟的访问令牌生命周期。

## 刷新令牌

刷新令牌不是承载令牌，而是简单的随机字符串。这意味着刷新令牌不用于访问受保护的资源。

当访问令牌到期时，使用刷新令牌来获得新的访问令牌。上面的图 1 说明了这个概念。

通常，刷新令牌是长期存在的。我们使用 1 周的寿命作为刷新令牌。

# 基本工作流程

我按照这个指南[1]进行基本的工作流程。

![](img/d56b9c6bfa33e385421667e73d440a4e.png)

图 2 —不记名令牌认证和授权工作流

1.  客户端通过提供有效凭证来请求访问令牌。
2.  Auth server 验证凭证并发布访问令牌&刷新令牌。
3.  客户端通过提供访问令牌来请求访问受保护的资源。
4.  资源服务器根据有效的访问令牌为请求提供服务。
5.  步骤(3)和(4)需要有效的访问令牌。如果客户端的访问令牌过期，则跳到步骤(7)；否则，它会发送一个请求来访问另一个受保护的资源。
6.  由于访问令牌已过期，资源服务器禁止访问受保护的资源，并返回无效令牌错误。
7.  客户端通过提交刷新令牌来请求新的访问令牌。
8.  认证服务器验证刷新令牌并发布新的访问令牌&新的刷新令牌。

# 访问和刷新令牌的表示

如前所述，刷新令牌只是一个随机字符串。然而，访问令牌具有特定的表示。根据 RFC [1]

> 基于资源服务器安全要求，访问令牌可以具有不同的格式、结构和使用方法(例如，加密属性)。

我使用 JSON Web Token (JWT) [2]格式作为访问令牌。根据本条[2]

> 一个格式良好的 JWT 由三个串联的 Base64url 编码的字符串组成，用点(.)

```
Example JSON Web Token (JWT) generated online via [https://jwt.io/](https://jwt.io/) eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

你可以在这里阅读更多关于这种格式的内容[2]。

# 为什么是 JWT 格式？

这种 JWT 格式允许我们在不查找数据库的情况下验证访问令牌。这意味着我们不必为了验证的目的而在 DB 中存储访问令牌。这就是这种格式的妙处。

# 支持 JWT 实施的图书馆

你可能会发现很多支持 JWT 实现的库，但是如果你使用 Scala，我推荐你使用这个库。原因？

*   良好的文档
*   积极贡献(提交:~850，版本:9.x.x)

# 刷新令牌的安全性

由于刷新令牌是长期存在的，并且用于获得访问令牌，因此保证它们的安全是至关重要的。为此，刷新令牌被轮换[4]。

因此，每次客户端交换刷新令牌以获得新的访问令牌时，都会返回一个新的刷新令牌(参见图 1)。这意味着旧的刷新令牌不再有效，并且不能用于检索新的访问令牌。

由于刷新令牌不断交换和失效，这降低了对我们的受保护资源进行未授权访问的威胁。

此外，这些刷新令牌存储在数据库中，以便可以随时撤销(如果需要)。

# 资源

[1][https://datatracker.ietf.org/doc/html/rfc6749](https://datatracker.ietf.org/doc/html/rfc6749#section-1.2)

[2][https://auth 0 . com/docs/secure/tokens/JSON-we b-tokens/JSON-we b-token-structure](https://auth0.com/docs/secure/tokens/json-web-tokens/json-web-token-structure)

[3]https://jwt-scala.github.io/jwt-scala/jwt-core-jwt.html

[4][https://auth 0 . com/blog/Refresh-tokens-what-to-use-them/# Refresh-Token-Rotation](https://auth0.com/blog/refresh-tokens-what-are-they-and-when-to-use-them/#Refresh-Token-Rotation)

如果你喜欢这篇文章，你可能也会喜欢下面的文章。

*   [使用 Spilo 的 Postgres 备份管理](https://awstip.com/postgres-backup-management-using-spilo-20a66befb88a)
*   [我的 3 款改变游戏规则的软件开发工具](https://medium.com/@anasanjaria/3-game-changing-software-development-tools-for-me-68ffb13e5076)

```
**Want to connect?**

[Facebook](https://www.facebook.com/anas.anjaria.kh) | [LinkedIn](https://www.linkedin.com/in/anasanjaria/) | [Twitter](https://twitter.com/anasanjaria)
```

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)