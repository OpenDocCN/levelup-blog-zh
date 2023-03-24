# 使用 jsonwebtoken 节点包创建 JSON Web 令牌

> 原文：<https://levelup.gitconnected.com/using-the-jsonwebtoken-node-package-to-create-json-web-tokens-e7941ff66e4e>

![](img/d92e898195672232e25555d437cebe40.png)

由 [Eric Prouzet](https://unsplash.com/@eprouzet?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着单页面应用程序和纯 API 后端的使用，JSON web token(jwt)已经成为向我们的应用程序添加身份验证功能的一种流行方式。

在本文中，我们将看看如何使用`jsonwebtoken`包来创建 JSON web 令牌。

# 装置

`jsonwebtoken`是节点包。我们可以通过运行以下命令来安装它:

```
npm install jsonwebtoken
```

# 使用

我们可以使用`sign`方法创建一个新的 JSON web 令牌。可以按以下格式调用:

```
jwt.sign(payload, secretOrPrivateKey, [options, callback])
```

`payload`是我们希望在令牌中保存的任何数据。

`secretOrPrivateKey`是我们用来签署令牌的秘密密钥。它是一个字符串、bugger 或对象，包含 HMAC 算法的秘密或 ESA 和 ECDSA 的 PEM 编码私钥。

如果是带有密码短语的私钥，则可以使用对象。在这种情况下，我们必须在`options`对象中设置`algorithm`属性。

`options`是一个接受多种选项的对象。可以设置以下选项:

*   `algorithm` —默认为`HS256`
*   `expiresIn` —到期前的秒数或时间跨度类似`'2 days'`的字符串或任何可被 [zeit/ms](https://github.com/zeit/ms) 接受的内容。
*   `notBefore` —用 [zeit/ms](https://github.com/zeit/ms) 描述可接受的时间跨度的秒数或字符串。确定开始接受 JWT 进行处理的时间
*   `audience` —标识 JWT 的目标收件人
*   `issuer` —标识发布 JWT 的主体
*   `jwtid` —令牌区分大小写的唯一标识符，即使在不同的发行者之间也是如此
*   `subject`—JWT 的主题
*   `noTimestamp` —如果是`true`，则`iat`字段不会包含在 JWT 中
*   `header` —用对象设置 JWT 的页眉
*   `mutatePayload`—`sign`函数将直接修改有效载荷对象，如果`true`。

`callback`是一个可选的回调函数，当这个方法被异步调用时，它会获取令牌或任何错误。

# 例子

我们可以如下使用它来与默认的 HMAC SHA256 算法同步签发令牌:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();app.post('/auth', (req, res) => {
  const token = jwt.sign({ foo: 'bar' }, 'secret');
  res.send(token);
})app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们创建了一个`auth`端点，它通过使用`sign`方法同步发布一个令牌，带有有效负载和秘密，但没有回调。

为了与 RSA SHA256 算法一起使用，我们从文件中读取私钥，如下所示:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const fs = require('fs');
const app = express();app.post('/auth', (req, res) => {
  const privateKey = fs.readFileSync('private.key');
  const token = jwt.sign({ foo: 'bar' }, privateKey, { algorithm: 'RS256' });
  res.send(token);
})app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们用`readFileSync`读取私钥，然后将它传递给第二个参数，而不是秘密。

我们可以在线或使用 OpenSSL 程序生成一个 RSA private。

要异步创建令牌，我们可以编写:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const fs = require('fs');
const app = express();app.post('/auth', (req, res) => {
  const privateKey = fs.readFileSync('private.key');
  jwt.sign({ foo: 'bar' }, privateKey, { algorithm: 'RS256' },  (err, token) => {
    res.send(token);
  });})app.listen(3000, () => console.log('server started'));
```

这个例子和上一个例子的唯一区别是我们传入了一个回调函数，而不是直接获取返回的令牌。

我们可以通过更改`iat`时间戳来回溯，即发布日期:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();app.post('/auth', (req, res) => {
  const token = jwt.sign({ foo: 'bar', iat: Math.floor(Date.now() / 1000) - 30 }, 'secret');
  res.send(token);
})app.listen(3000, () => console.log('server started'));
```

在上面的例子中，我们将令牌回溯 30 秒，因为我们将`iat`设置为`Math.floor(Date.now() / 1000) — 30`。

![](img/528d31123ca4086aa9abe0415ea71f0b.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 令牌过期(过期声明)

我们还可以通过如下设置`exp`字段来更改令牌到期时间:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();app.post('/auth', (req, res) => {
  const token = jwt.sign({
    exp: Math.floor(Date.now() / 1000) + (2 * 60 * 60),
    data: 'foobar'
  }, 'secret');
  res.send(token);
})app.listen(3000, () => console.log('server started'));
```

然后，我们得到一个令牌，它在发布后 2 小时过期，因为我们将`Math.floor(Date.now() / 1000) + (2 * 60 * 60)`作为`exp`值。

一种更简单的方法是这样写:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();app.post('/auth', (req, res) => {
  const token = jwt.sign({
    data: 'foobar'
  }, 'secret', { expiresIn: 2 * 60 * 60 });
  res.send(token);
})app.listen(3000, () => console.log('server started'));
```

或者:

```
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();app.post('/auth', (req, res) => {
  const token = jwt.sign({
    data: 'foobar'
  }, 'secret', { expiresIn: '2h' });
  res.send(token);
})app.listen(3000, () => console.log('server started'));
```

# 结论

我们可以使用`sign`方法创建一个 JSON web 令牌。它采用各种选项，如受众、发行者、到期日、ID、加密算法、使令牌仅在一段时间后可用等。

它支持 HMAC 和 RSA 算法来存储秘密，或者我们可以创建自己的秘密字符串来签署我们的 JWT。