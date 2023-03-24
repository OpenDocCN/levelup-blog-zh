# 快速请求对象指南—方法

> 原文：<https://levelup.gitconnected.com/guide-to-the-express-request-object-methods-207a749dc676>

![](img/2b9a40161e436d5890a7412850f6c51d.png)

布鲁克·拉克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

request 对象让我们在中间件和路由处理程序中获得来自客户端的请求信息。

在本文中，我们将详细研究 Express 的请求对象方法的属性，包括获取头和查询字符串。

# 方法

请求对象有各种方法。

## 请求接受(类型)

`accepts`方法检查`Accept`请求头中与头一起发送的值。它接受一个带有数据类型值的字符串，我们希望检查它是否与`Accept`请求头一起发送。

它返回最佳匹配，或者如果没有匹配，则返回`false`。

如果返回`false`，那么应该返回 406 响应代码，因为我们的路由不接受该数据类型。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.get('/', (req, res) => {
  res.send(req.accepts('html'));
})app.listen(3000);
```

然后，当我们发送带有包含`html`的值的`Accept`头时，它将返回`html`。

否则，`false`将被返回。

其他例子包括:

```
req.accepts('text/html')
req.accepts(['json', 'text'])
req.accepts('application/json')
req.accepts('image/png')
req.accepts('png')
req.accepts(['html', 'json'])
```

## req.acceptsCharsets(charset [，…])，req.acceptsEncodings(encoding [，…])，req.acceptsLanguages(lang [，…])

方法返回指定字符集的第一个被接受的字符集。字符集来自请求的`Accept-Charset` HTTP 头字段。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.get('/', (req, res) => {
  res.send(req.acceptsCharsets('utf8'));
})app.listen(3000);
```

然后，如果设置了字符集，我们就取回在`Accept-Charset` HTTP 头中的字符集。

`acceptsEncodings`返回基于`Accepts-Encoding` HTTP 头的编码；并且`acceptsLanguages`返回基于`Accepts-Language` HTTP 头的编码。

## req.get(字段)

我们可以用`get`方法获得头字段值。它接受标头的密钥，并且不区分大小写。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.get('/', (req, res) => {
  res.send(req.get('content-type'));
})app.listen(3000);
```

然后，当我们向`/`路由发出请求，并将`Content-Type`头的值设置为`text/cmd`时，我们返回`text/cmd`。

## 请求类型

如果与传入的`type`匹配，`is`方法返回`Content-Type`头的值。如果请求没有正文，则返回`null`。否则，`false`就返回了。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.get('/', (req, res) => {
  res.send(req.is('html'));
})app.listen(3000);
```

然后我们得到`null`，因为它是一个 get 请求，没有请求体。

另一方面，如果我们有:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.post('/', (req, res) => {
  res.send(req.is('html'));
})app.listen(3000);
```

当我们用设置为`text/html`的`Content-Type`头向`/`路由发出 POST 请求时，我们返回`html`。

如果`Content-Type`头被设置为没有`html`的值，那么我们返回`false`。

![](img/5f3c285998dad216ea8af3816eef2d2e.png)

照片由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## req.param(名称[，默认值])

`req.param`用给定的键从查询字符串的搜索参数中获取值。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.post('/', (req, res) => {
  res.send(req.param('name', 'Joe'));
})app.listen(3000);
```

上面的代码使用`name`键获取搜索参数值。默认值设置为`'Joe'`。

然后当我们向`/?name=jane`发出请求时，我们得到`jane`。否则，如果没有指定`name`搜索参数，那么我们得到`Joe`。

`req.param`已被弃用，因此我们应根据情况使用`req.params`、`req.body`或`req.query`。

查询字符串、URL 参数和正文值的查找按以下顺序执行:

*   `req.params`
*   `req.body`
*   `req.query`

## req.range(大小[，选项])

`range`方法解析`Range`头。`size`参数具有资源的最大大小。`options`参数是一个具有以下属性的对象:

*   `combine` —一个布尔值，它指定是否应该合并重叠和相邻的范围。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.get('/', (req, res) => {
  const range = req.range(20000);
  if (range && range.type === 'bytes') {
    res.json(range);
  }
  else {
    res.send();
  }
})app.listen(3000);
```

然后，当我们向`/`发出 GET 请求，并将`Range`头设置为`bytes=200–1000, 2000–6576, 19000-`时，我们得到:

```
[
    {
        "start": 200,
        "end": 1000
    },
    {
        "start": 2000,
        "end": 6576
    },
    {
        "start": 19000,
        "end": 19999
    }
]
```

由于我们将`size`参数指定为 20000，所以最后的`end`值是 19999。

如果我们没有在值中指定`bytes`，我们什么也得不到。

# 结论

请求对象有一些方法来解析查询字符串并获得各种头，如`Range`、`Accepts`和`Content-Type`头。

我们可以用`range`得到`Range`割台，用`accepts`得到`Accepts`，用`is`得到`Content-Type`。

`get`方法可以获得任何请求头字段的值。