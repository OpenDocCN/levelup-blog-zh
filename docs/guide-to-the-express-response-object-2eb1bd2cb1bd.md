# 快速响应对象指南

> 原文：<https://levelup.gitconnected.com/guide-to-the-express-response-object-2eb1bd2cb1bd>

![](img/df7c32b96aeb2a33500731e4050c16cb.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Express response 对象允许我们向客户端发送响应。

可以发送各种响应，比如字符串、JSON 和文件。此外，除了主体之外，我们还可以向客户端发送各种标头和状态代码。

在本文中，我们将研究响应对象的各种属性。

# 响应对象

response 对象有一些有用的方法让我们返回各种类型的响应。

# 性能

## res.app

`app`属性有应用程序的实例。它与`req.app`属性相同。

## 资源标题发送

`headersSent`属性表明应用程序是否发送了响应的 HTTP 报头。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.get('/', (req, res) => {
  console.dir(res.headersSent)
  res.send('foo');
  console.dir(res.headersSent);
})app.listen(3000);
```

在响应发出之前，`res.headersSent`是`false`。一旦响应被发送，`res.headersSent`变为`true`。

## res.locals

`res.locals`属性是一个对象，它对只限于请求的局部变量做出响应。

它对于保存路线数据很有用。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use((req, res, next) => {
  res.locals.foo = 'foo';
  next()
})app.get('/', (req, res) => {
  res.send(res.locals.foo);
})app.listen(3000);
```

然后我们显示`foo`，因为我们将`res.locals.foo`设置为`'foo'`，然后调用路由处理器。

# 方法

响应对象有各种方法。

## res.append(字段[，值])

`append`方法让我们将响应头附加到我们的响应上。

例如，如果我们有以下代码:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.append('Link', ['<[http://localhost/](http://localhost/)>', '<[http://localhost:3000/](http://localhost:3000/)>'])
  res.append('Set-Cookie', 'foo=bar; Path=/; HttpOnly')
  res.append('Warning', 'Alert')
  res.send();
})app.listen(3000, () => console.log('server started'));
```

然后，当我们转到 Postman 时，当我们查看数据时，应该会在响应的 Headers 选项卡中看到相同的数据。

注意，我们必须运行`res.send()`来实际发送响应。

## RES . attachment([文件名])

`res.attachment`让我们为响应添加一个文件。它不发送响应。

例如，我们可以如下使用它:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.attachment('../public/foo.txt');
  res.send();
})app.listen(3000, () => console.log('server started'));
```

然后，如果我们在`public`文件夹中有一个`foo.txt`，那么如果我们请求路由，该文件将被下载。

请注意，我们再次使用`res.send()`来实际发送响应。

![](img/4d0dbb6fa7c8207c1768a8b53557980b.png)

照片由[梅姆](https://unsplash.com/@picoftasty?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## res.cookie(名称，值[，选项])

让我们给响应添加一个 cookie。

在发送 cookies 之前，我们可以在`options`对象中设置各种选项。它们如下:

*   `domain`—cookie 的域名字符串。默认为 app 的域名。
*   `encode` —用于 cookie 值编码的函数。默认为`encodeURIComponent`
*   `expires` —饼干在 GMT 的到期日。如果没有或者设置为 0，则创建一个会话 cookie
*   `httpOnly` —一个布尔标志，指示 cookie 是否只能由 web 服务器访问
*   `maxAge` —用于设置相对于当前时间的到期时间的数字，单位为毫秒
*   `path`—cookie 路径的字符串。默认为`/`
*   `secure` — boolean 标记仅用于 HTTPS 的 cookie
*   `signed` —布尔值，表示是否应对 cookie 进行签名
*   `sameSite`—`SameSite`Set-Cooke 属性的布尔值或字符串值

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.cookie('name', 'foo', { domain: 'repl.it', path: '/', secure: true })
  res.send();
})app.listen(3000, () => console.log('server started'));
```

然后，我们向客户端发送一个名为`foo`的 cookie。我们可以在右上角的 Cookies 链接下签到 Postman。

我们可以在一个响应中发送多个 cookies，如下所示:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res
    .cookie('name', 'foo', { domain: 'repl.it', path: '/', secure: true })
    .cookie('age', 10)
    .send();
})
app.listen(3000, () => console.log('server started'));
```

我们可以设置`encode`选项来选择编码 cookie 的函数。例如，我们可以如下使用它:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res
    .cookie('name', 'foo', { encode: String })
    .cookie('age', 10, { encode: Number })
    .send();
})
app.listen(3000, () => console.log('server started'));
```

我们也可以如下设置`httpOnly`标志:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res
    .cookie('httpOnly', true, { httpOnly: true })
    .send();
})
app.listen(3000, () => console.log('server started'));
```

我们可以在像 Postman 这样的 HTTP 客户端中看到结果，响应中的 cookie 列在 cookie 部分。

## res.clearCookie(名称[，选项])

`clearCookie`用给定的`name`清除 cookie。

例如，我们可以如下使用它:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res
    .clearCookie('httpOnly', { path: '/' })
    .send();
})
app.listen(3000, () => console.log('server started'));
```

然后，如果设置了`httpOnly` cookie，它将在向`/`发出请求后被清除。我们可以在 cookies 选项卡中通过 Postman 检查这一点。

# 结论

我们可以使用响应对象的属性和方法向客户端发送各种信息。

我们可以用它用`cookies`方法设置 cookies，用`clearCookies`方法清除它们。

`cookies`方法采用了通常可用于 cookies 的各种选项。

我们可以使用`append`向响应添加各种响应头。

`attachment`方法可以用来发送文件响应。