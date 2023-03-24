# 如何使用 Node.js 中的 REST APIs 检查电子邮件地址是否已经存在

> 原文：<https://levelup.gitconnected.com/how-to-check-if-an-email-address-already-exists-with-rest-apis-node-js-68c91aacca97>

本文将关注 REST APIs 的设计。这听起来可能很琐碎，但开发者关于这一点的问题让我相信，有些事情并不像看起来那么清楚。

从我长期的经验来看，我可以说，创建一个 API 基本上不是很难，但是设计一个满足业务目标、遵循 REST 指南、稳定且持久的 API——那就很难了。

所以这是一个仔细研究这个问题的好时机。

![](img/fe1002d08daa77ce3507200b33c921e9.png)

图片来源:stock.adobe.com

REST 指南建议对特定类型的服务器调用使用特定的 HTTP 方法。但是，从技术上讲，可以忽略此策略。然而，对于干净和可理解的实现，我强烈反对这样做。

但是 REST 这个缩写代表什么？

*   REST 代表代表性状态转移。
*   它是一种提供标准的架构风格。
*   RESTful 系统是无状态的。
*   它们将客户机和服务器的关注点分开。

> REST 是一个简单的无状态架构，通常运行在 HTTP 上。正如我以前多次写的那样:首要任务应该是保持事情简单，因此可读和可维护。

如前所述，使用 HTTP 动词是为了能够与 REST 系统的资源进行交互。我想像 GET、POST、PUT、DELETE 这些典型的应该是大多数人都熟悉的。

例如，如果我们想要通过 API 访问资源，我们需要 URL 或端点，并使用 HTTP 动词 GET 来表示我们想要获取指定的资源。

在下面的示例中，我们使用 HTTP 动词 GET 检索电子邮件地址为 someone@testmail.com 的资源用户。属性 email 是资源的主键。

```
$ curl localhost:3000/user/test@test.de -X GET
{"fullName":"Rico Fritzsche","email":"someone@testmail.com","location":"Palma"}
```

结果，我们获得了 JSON 形式的资源。如果请求能够被满足，HTTP 响应状态代码是 *200* 。

如果您想删除这个资源，只需要更改 HTTP 动词。我们必须告诉 REST API 使用 delete 动词删除资源，请看下面的例子。

```
$ curl localhost:3000/user/someone@testmail.com -X DELETE
```

由此我们已经可以看出，REST 约定基本上相当简单。所以无视这些规则是毫无意义的。

这是因为 HTTP 动词是强制性的，并且像 *GetUser* 或 *DeleteUser* 这样的方法名在 REST API 中没有位置。基本上，除了违反规则之外，我们还将指定两次我们想要从服务中得到的东西(一次在名称中，一次作为动词)。那会感觉很奇怪，不是吗？

我想这里已经很清楚了。当 API 应该提供一个允许用户检查的功能时，例如，一个电子邮件地址是否已经存在于系统中，问题就来了。

用户故事可能是这样的。

> *API 必须提供一个端点，通过该端点可以查询指定的电子邮件地址是否已经存在，以防止不正确的条目或请求。*

在许多情况下，API 看起来是这样的，这在逻辑上与 REST 指南相矛盾。

```
$ curl localhost:3000/user/test@testmail.com/check -X GET
```

然而，这导致 GET 被滥用，人工动词被构建到 API 中。

# **正确的做法**

有一种符合 REST 的方法来进行检查。这种方式非常简单。HEAD 方法与 GET 方法相同，只是服务器不能在响应中返回消息体。

```
HEAD api/user/{email}
```

响应应该是“200 正常”或“409 冲突”。因此，消费者只需评估返回的 HTTP 状态代码。

具体来说，这看起来如下。

```
$ curl localhost:3000/user/someone@testmail.com -X HEAD
```

如果响应代码为 200，则指定的电子邮件地址在系统中尚不存在。如果返回 409(冲突)，则该电子邮件地址已经存在。消费者可以根据需要对状态代码做出反应。

# 但是注意！

您永远不应该在公共 API 中这样做，因为这样会造成帐户枚举安全漏洞。

攻击者可以反复查询该端点，找出系统中存在哪些电子邮件地址。
因此，该程序应仅在应用程序中使用，或者具有足够安全的端点。但是，这也适用于允许使用 GET 动词查询资源的电子邮件地址的端点。

我希望这篇文章能为设计更好的 REST APIs 做出贡献。

*干杯！*