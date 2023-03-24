# 如何通过 HMAC 保护 Webhook 端点

> 原文：<https://levelup.gitconnected.com/how-to-secure-webhook-endpoints-with-hmac-231ae547fe93>

![](img/2e196e5363eae64a50a3b377c1c29a2e.png)

Webhooks 在 SaaS 集成中无处不在，这是有原因的。它们是一种简单快捷的方式，可以根据系统中数据的变化，通过 HTTP 回调在系统之间传输数据。我的公司帮助 SaaS 团队构建与他们客户的其他应用的本机集成，我们的许多客户使用 webhooks 进行集成。

在过去的几周里，我们帮助了几个需要确保其 webhook 端点安全的客户。在本帖中，我们将描述我们推荐的方法。但首先，让我们打好一些基础。

# webhooks 是如何工作的？

简而言之，源 app 有 webhook，目的 app 有 webhook 端点；基于源应用中发生的一些事件，webhook 向 webhook 端点发送 HTTP 请求。

下面是一个 HTTP 请求体(或有效负载)的简单示例:

```
{
  *"event"*: *"WAREHOUSE_UPDATE"*,
  *"updates"*: [
    {
      *"item"*: *"gadgets"*,
      *"action"*: *"add"*,
      *"quantity"*: 20
    },
    {
      *"item"*: *"widgets"*,
      *"action"*: *"remove"*,
      *"quantity"*: 10
    }
  ]
}
```

但是，如何确保目标应用程序从源应用程序接收到有效数据，而不是从欺骗了 webhook 的坏人那里接收到虚假数据呢？

简而言之，您需要设置 webhook 来为端点提供 HTTP 请求和端点可以用来验证数据的惟一键。但是，在我们进入细节之前，让我们简要地介绍一下散列。

# 哈希是什么？

最简单地说，散列是将一个值(或键)转换成另一个值的过程。即使您以前没有广泛使用过散列法，您也可能知道 MD5、SHA-256 或 RipeMD-128。其中的每一个都是哈希算法(也称为加密哈希函数)的名称。

让我们看看每个算法对经典字符串做了什么:

*   MD5 哈希`Hello World!`到`ed076287532e86365e841e92bfc50d8c`
*   SHA-256 将`Hello World!`散列到`7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069`
*   RipeMD-128 哈希`Hello World!`到`24e23e5c25bc06c8aa43b696c1e11669`

重要的是，算法每次都以相同的方式散列一个值。如果我们不改变我们的字符串(' Hello World！')，得到的哈希值也不会改变。

但是，如果字符串中的*任何内容*发生变化，哈希也会发生变化。例如，让我们将“H”小写，这样我们就有了“hello World！”看看这能做什么:

*   MD5 哈希`hello World!`到`41d0c351efedf7fdb9a5dc8a1ed4b4e3`
*   SHA-256 将`hello World!`哈希到`e4ad0102dc2523443333d808b91a989b71c2439d7362aca6538d49f76baaa5ca`
*   RipeMD-128 哈希`hello World!`到`b5cf338f17d6796ba0312e0d78c70831`

细微的变化，但由此产生的差异是明显的。

尽管散列法不能让我们完全解决我们最初的问题(有人向 webhook 端点发送虚假数据)，但它确实将我们直接引向了 HMAC。

# 什么是 HMAC？

HMAC 或散列消息认证码是一种使用两个密钥而不是一个密钥的认证方法。第一个密钥是 HTTP 请求主体，而第二个密钥是秘密的加密密钥。当您为您的 webhook 实现 HMAC 时，您将使用这两个密钥加上一个算法，如 MD5、SHA-256 或 RipeMD-128，以确保出现在您的 webhook 端点的 HTTP 请求是合法的。

# HMAC 是如何工作的？

在源应用程序通过 webhook 发送 HTTP 请求之前，它使用秘密密钥通过 HMAC 散列有效负载(请求体)。然后，产生的散列被捆绑到 HTTP 请求中作为头部，整个请求(头部和主体)被发送到 webhook 端点。

收到 HTTP 请求后，目标应用程序使用密钥对请求体进行哈希处理，然后将结果与报头中提供的哈希进行比较。如果值匹配，目标应用程序知道数据是合法的，并处理它。如果值不匹配，目标应用程序会拒绝数据并执行为该场景编写的任何代码——可能会创建日志条目或发送通知。

如果有人试图欺骗有效载荷，他们将无法生成有效的散列，因为他们没有密钥。门关上了。

让我们想象一下，你有一个电子商务平台连接到你的应用程序。你的应用程序会定期向平台的 webhook 端点发送有效负载，以创建订单和退款。使用 HMAC 可以确保不会有随机(或不那么随机)的人向电子商务平台发送虚假订单或退款。

但是，你会说，难道没有人可以捕获一个 HTTP 请求，并对报头中的散列进行逆向工程，从而找出秘密吗？简而言之:不是。[哈希是单向函数](https://mathworld.wolfram.com/One-WayFunction.html)。要破解一个具有足够复杂秘密的散列，我们需要的计算能力和时间比我们任何人都要多。

# 依赖 HMAC 作为 webhook 端点的应用

一些知名应用目前使用 HMAC 来保护其 webhook 端点:

*   [Slack](https://api.slack.com/authentication/verifying-requests-from-slack) :在你创建 Slack app 的时候提供一个*签名秘密*。当它发送一个 webhook 有效负载时，它使用 SHA256 将有效负载和 webhook 的时间戳与该秘密进行散列。webhook 请求将结果散列作为名为`X-Slack-Signature`的头。
*   [Dropbox](https://www.dropbox.com/developers/reference/webhooks#notifications) :当你创建一个 Dropbox 应用时，会生成一个*应用秘密*，并使用该秘密生成 webhook HMAC 哈希，并使用 OAuth 2.0 认证用户。它使用 SHA256 散列 webhook 有效负载，并将散列作为名为`X-Dropbox-Signature`的头发送。
*   [Shopify](https://shopify.dev/apps/webhooks/configuration/https#step-5-verify-the-webhook) :创建一个 *API 密匙*并用这个密匙和 SHA256 散列它的有效负载。它将散列作为名为`X-Shopify-Hmac-SHA256`的报头发送。

# HMAC 有广泛的语言支持

你可以使用任何现代语言来计算 HMAC 散列。以下是一些具有 HMAC 功能的流行语言的链接:

*   [节点 JS](https://nodejs.org/api/crypto.html)
*   [Python](https://docs.python.org/3/library/hmac.html)
*   [PHP](https://www.php.net/manual/en/function.hash-hmac.php)
*   [。NET C#](https://docs.microsoft.com/en-us/dotnet/api/system.security.cryptography.hmac?view=net-6.0)

# HMAC 代码示例

最后，如果没有代码，这一切会变成什么样？下面是如何使用内置加密模块在 NodeJS 中进行设置的示例:

```
const crypto = require(*"crypto"*);const SECRET_KEY = *"secret-FA782CF7-060E-484E-B3DC-055CF2C9ED99"*;const payload = JSON.stringify({
  *event*: *"REFUND_REQUEST"*,
  *user*: *"realcustomer@notabaddie.com"*,
  *amount*: *"50.25"*,
});const hash = crypto
  .createHmac(*"sha256"*, SECRET_KEY)
  .update(payload, *"utf-8"*)
  .digest(*"hex"*);console.log(hash); *// Prints d12f95e3f98240cff00b2743160455fdf70cb8d431db2981a9af8414fc4ad5f8*
```

使用 HMAC 的相应 HTTP 请求可能如下所示:

```
curl https://my.webhook.endpoint.com/callback \
  --request POST \
  --header *"x-hmac-hash: d12f95e3f98240cff00b2743160455fdf70cb8d431db2981a9af8414fc4ad5f8"* \
  --data *'{"event":"REFUND_REQUEST","user":"realcustomer@notabaddie.com","amount":"50.25"}'*
```

即使一个坏演员拦截了你的 HTTP 请求，他们也不能向他们自己的电子邮件地址发出 100 万美元的退款请求，因为没有密钥他们不能正确地签署请求。

# 结论

使用 HMAC 并不要求你学习一门新的语言或者对加密有深入的了解，但是它可以保护你通过 webhooks 传输的数据的完整性。