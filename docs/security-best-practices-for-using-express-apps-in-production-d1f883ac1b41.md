# 在生产中使用 Express 应用程序的安全最佳实践

> 原文：<https://levelup.gitconnected.com/security-best-practices-for-using-express-apps-in-production-d1f883ac1b41>

![](img/9163bf6911a92352ba7d9af562bac037.png)

照片由[伯纳德·赫尔曼](https://unsplash.com/@bernardhermant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我们将 Express 应用程序用于生产时，即它由外部用户使用时，我们必须小心安全性，因为它对外部世界是可用的。

在本文中，我们将了解在生产环境中运行 Express 应用程序时的一些安全性最佳实践。

# 不要使用过期版本的 Express

Express 的过时版本不再维护，可能存在未打补丁的安全漏洞，使我们的应用程序面临各种攻击的风险。

# 使用 TLS

如果我们在传输需要保护的数据，我们应该使用 TLS 来保护用户的数据。这意味着我们避免了中间人攻击和数据包嗅探。

即使我们的应用程序不传输安全数据，它仍然让用户更加相信我们的网站是安全的。

我们可以用“让我们加密”获得免费的 TLS 证书。这是一种自动服务，可以生成浏览器信任的新证书，

# 使用头盔

头盔是一系列快速中间件，通过设置 HTTP 头来保护我们的应用程序在网络上存在一些众所周知的安全漏洞。

Helment 附带的中间件包括:

*   `csp` —设置`Content-Security-Policy`头以帮助防止跨站点脚本攻击和其他跨站点注入。
*   `hidePoweredBy` —移除`X-Powered-By`标题
*   `hpkp` —添加公钥锁定报头，防止中间人利用伪造证书进行攻击
*   `hsts` —设置强制 HTTPS 连接到服务器的`Strict-Transport-Security`标头。
*   `ieNoOpen` 0 为 IE 8 或更高版本设置`X-Download-Options`
*   `noCache` —设置`Cache-Control`和`Pragma`头以禁用客户端缓存
*   `noSniff` —设置`X-Content-Type-Options`头以防止浏览器从声明的内容类型中嗅探响应
*   `frameguard` —设置`X-Frame-Options`割台以提供点击劫持保护
*   `xssFilter` —将`X-XSS-Protection`设置为在最新的 web 浏览器中启用跨站点脚本过滤器

至少禁用`x-powered-by`响应头是一个好主意，这样攻击者就不会知道我们的应用是一个 Express 应用，并对其发起特定的攻击。

# 安全使用 Cookies

我们可以使用`express-session`或`cookie-session`中间件来安全地使用 cookies。

`express-session`在服务器上存储会话数据。它只将会话 ID 保存在 cookie 本身。

我们可以设置一个会话存储供生产使用，因为它默认使用内存存储。

将整个 cookie 存储在客户端。我们设置 cookies 的时候不应该超过 4093 字节。

cookie 数据对客户端是可见的，所以秘密数据不应该用`cookie-session`发送给客户端。

# 不要使用默认会话 Cookie 名称

不应使用由 Express 设置的默认会话 cookie 名称，因为它可以识别某个应用程序正在 Express 上运行。

我们可以通过向`session`传递一个对象来做到这一点，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const session = require('express-session');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.set('trust proxy', 1);
app.use(session({
  secret: 'secret',
  name: 'sessionId'
}))app.listen(3000);
```

![](img/298dcfc84e0565cf732ba9c9a6d10bc1.png)

照片由 [Rai Vidanes](https://unsplash.com/@raividanes?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 设置 Cookie 安全选项

为了增强安全性，我们可以设置各种 cookie 选项。以下是可用的选项:

*   `secure` —确保浏览器仅通过 HTTPS 发送 cookie
*   `httpOnly` —确保 cookie 仅通过 HTTP(S)发送，而不是客户端 JavaScript。这有助于我们防止 XSS 袭击
*   `domain` —表示 cookie 的域，可用于与请求 cookie 的 URL 中的服务器的域进行比较。
*   `path` —指示与请求的路径进行比较的路径，然后在请求中发送 cookie
*   `expires` —设置永久 cookies 的到期日期。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const session = require('express-session');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
const expiryDate = new Date(Date.now() + 60 * 60 * 1000)
app.use(session({
  name: 'session',
  secret: 'secret',
  keys: ['foo', 'bar'],
  cookie: {
    secure: true,
    httpOnly: true,
    domain: 'EnormousBeneficialScript--five-nine.repl.co',
    path: '/',
    expires: expiryDate,
  }
}));app.get('/', (req, res) => {
  req.session.foo = 'foo';
  res.send(req.session.foo);
})app.listen(3000);
```

# 防止暴力攻击

我们可以对我们的授权路径进行速率限制，这样攻击者就不能使用暴力攻击来猜测存储在我们应用程序数据库中的凭证。

例如，我们可以通过限制拥有相同用户名和 IP 地址的用户尝试失败的次数来限制 IP 地址。

然后，如果用户尝试失败的次数达到了我们可以容忍的程度，我们就会锁定用户一段时间。

如果一天中失败的尝试超过 100 次，我们就会封锁该 IP 地址。

我们可以使用[灵活限速器](https://github.com/animir/node-rate-limiter-flexible)包对我们的路线进行限速。

# 结论

为了使我们的 Express 应用程序在生产环境中保持安全，我们应该采取一些预防措施。

首先，我们应该使用 TLS 来传输数据，以防止它们被嗅探，并防止中间人攻击。

接下来，我们应该设置标题，防止用户找到关于我们应用的信息，并加强安全通信。

此外，我们应该安全地使用 cookies，用一个秘密对它进行签名，并为它们设置安全选项。

最后，为了防止暴力攻击，我们应该为我们的 API 调用设置一个速率限制，这样攻击者就不能通过重复的登录尝试来猜测我们用户的凭证。