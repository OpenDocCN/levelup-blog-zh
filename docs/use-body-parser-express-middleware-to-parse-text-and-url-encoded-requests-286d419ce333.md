# 使用 body-parser Express 中间件来解析文本和 URL 编码的请求

> 原文：<https://levelup.gitconnected.com/use-body-parser-express-middleware-to-parse-text-and-url-encoded-requests-286d419ce333>

![](img/6fcd4f3130237b4ebc9637980f362246.png)

照片由[乔丹·惠特](https://unsplash.com/@jwwhitt?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

默认情况下，Express 4.x 或更高版本没有提供任何解析请求体的功能。因此，我们需要添加一些东西来做到这一点。

在本文中，我们将研究如何使用`body-parser`中间件来处理文本和 URL 编码的请求体。

# 解析文本正文

我们可以用`text`方法解析文本请求体。支持`gzip`和`deflate`编码的自动膨胀。

解析后的字符串将被设置为`req.body`的值。

它采用一个可选的`option`对象，该对象具有以下属性:

*   `defaultCharset` —如果没有在`Content-Type`标题中指定，则指定文本内容的默认字符集。默认为`utf-8`。
*   `inflate` —当设置为`true`时，压缩的请求体将膨胀。否则，他们会被拒绝。
*   `limit` —控制最大请求正文大小。如果是数字，那就用字节来衡量。如果它是一个字符串，那么它可以被解析成多个字节。
*   `type` —这用于确定它将解析什么媒体类型。它可以是字符串、字符串数组或函数。如果不是函数，那么就直接传入`type-is`库。否则，如果调用函数的数据类型返回真值，则解析请求
*   `verify` —这是一个带有签名`(req, res, buf, encoding)`的函数，其中`buf`是原始请求体的缓冲对象。解析可以通过在函数中抛出一个错误来中止。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const options = {
  inflate: true,
  limit: 1000,
  defaultCharset: 'utf-8'
};
app.use(bodyParser.text(options));app.post('/', (req, res) => {
  res.send(req.body);
});app.listen(3000);
```

然后当我们用主体`foo`向`/`发出 POST 请求时，我们得到`foo`的返回。

![](img/524a5ede24ec58725e6f080d8b46402b.png)

照片由 [Ellicia](https://unsplash.com/@shuxin00?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 解析 URL 编码的请求正文

我们可以使用`urlencoded`方法来解析 URL 编码体。支持`gzip`和`deflate`编码的自动膨胀。

解析后的请求体将被设置为`req.body`的值。该对象将包含键值对，当`extended`设置为`false`或其他值时，该值可以是字符串或数组。

它采用一个可选的`option`对象，该对象具有以下属性:

*   `extended`—`extended`选项允许我们选择在`false`时使用`querystring`库解析 URL 编码的数据，或者在设置为`true`时使用`qs`库。`extended`语法允许我们对丰富的对象和数组进行编码，允许使用 URL 编码的类似 JSON 的体验。默认值为`true`，但使用默认值已被否决。
*   `inflate` —当设置为`true`时，压缩的请求体将膨胀。否则，他们会被拒绝。
*   `limit` —控制最大请求正文大小。如果是数字，那就用字节来衡量。如果它是一个字符串，那么它可以被解析成多个字节。
*   `parameterLimit` —让我们控制 URL 编码数据中允许的最大数量。如果超过给定值，将返回 413 响应代码。默认值为 1000。
*   `type` —这用于确定它将解析什么媒体类型。它可以是字符串、字符串数组或函数。如果不是函数，那么就直接传入`type-is`库。否则，如果调用函数的数据类型返回真值，则解析请求
*   `verify` —这是一个带有签名`(req, res, buf, encoding)`的函数，其中`buf`是原始请求体的缓冲对象。解析可以通过在函数中抛出一个错误来中止。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const options = {
  inflate: true,
  limit: 1000,
  extended: true
};
app.use(bodyParser.urlencoded(options));app.post('/', (req, res) => {
  res.send(req.body);
});app.listen(3000);
```

然后，当我们用正文`name=Mary&age=10`向`/`路由发送一个 URL 编码的 POST 正文时，我们得到:

```
{"name":"Mary","age":"10"}
```

我们可以通过发送以下命令来发送数组:

```
name=Mary&age=10&favoriteFood=apple&favoriteFood=orange
```

然后我们回来了:

```
{"name":"Mary","age":"10","favoriteFood":["apple","orange"]}
```

作为回应。这里假设`extends`是`true`。

如果我们将`parameterLimit`设置为 1，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const options = {
  inflate: true,
  limit: 1000,
  extended: true,
  parameterLimit: 1,
};
app.use(bodyParser.urlencoded(options));app.post('/', (req, res) => {
  res.send(req.body);
});app.listen(3000);
```

然后我们得到一个 413 错误。

# 结论

使用`text`方法，`body-parser`可以解析文本请求体。我们将通过`req.body`获得一个带有已解析主体的字符串。

为了解析 URL 编码的请求体，我们可以使用`urlencoded`方法。它可以解析数组和对象。URL 编码的主体作为查询字符串发送，我们可以多次发送具有相同键的查询，使其被解析为数组。