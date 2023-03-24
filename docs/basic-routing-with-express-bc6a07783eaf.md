# 快速基本路由

> 原文：<https://levelup.gitconnected.com/basic-routing-with-express-bc6a07783eaf>

![](img/c672c876932c5c5f90e89747bb564766.png)

Justin Lawrence 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

路由是后端应用程序最重要的部分。Express 允许我们轻松地将 URL 路由到我们的路由处理程序代码。

在本文中，我们将了解如何使用 Express 创建基本路线。

# 基本路由

路由是 Express 应用程序响应来自 URL 或路径以及特定 HTTP 请求方法(如 GET 或 POST)的客户端请求的地方。

Express 中的每条路线都可以有一个以上的处理函数，当路线匹配时执行这些函数。

路由的一般定义采用以下格式:

```
app.METHOD(PATH, HANDLER);
```

`app`是快递 app 实例。

`METHOD`是小写的 HTTP 请求方法。可能的方法包括 GET、POST、PUT 和 DELETE。

`PATH`是以小路为路线。`HANDLER`是路径匹配时运行的处理函数。

例如，我们可以写:

```
app.get('/', (req, res) => {
  res.send('Hello World!')
})
```

在屏幕上显示`'Hello World!'`。

如果我们希望我们的应用程序接受 POST 请求，我们可以如下使用`app.post`:

```
app.post('/', (req, res) => {
  res.send('Received POST request');
})
```

我们可以使用像 Postman 这样的 HTTP 客户端，通过向运行我们的应用程序的 URL 发送 POST 请求来测试这一点。那么我们应该得到:

```
Received POST request
```

在响应正文中。

同样，我们可以对 PUT 和 DELETE 请求做同样的事情，如下所示:

```
app.put('/', (req, res) => {
  res.send('Got a PUT request at /user')
})app.delete('/', (req, res) => {
  res.send('Got a DELETE request')
})
```

注意，在每个路由处理器中，我们有一个`req`和`res`参数。`req`具有请求对象，该对象具有 URL、头和其他字段。

`res`对象让我们将响应返回给客户端。

# 请求对象

我们在上面的路由处理程序中的`req`参数是`req`对象。

它有一些属性，我们可以使用这些属性来获取客户端发出的请求的相关数据。下面列出了比较重要的几个。

## req.baseUrl

`req.baseUrl`属性保存已挂载的路由器实例的基本 URL。

例如，如果我们有:

```
const express = require('express');const app = express();
const greet = express.Router();
greet.get('/', (req, res) => {
  console.log(req.baseUrl);
  res.send('Hello World');
})app.use('/greet', greet);app.listen(3000, () => console.log('server started'));
```

然后我们从`console.log`得到`/greet`。

## 请求体

`req.body`有请求体。我们可以用`express.json()`解析 JSON 主体，用`express.urlencoded()`解析 URL 编码的请求。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.post('/', (req, res) => {
  res.json(req.body)
})app.listen(3000, () => console.log('server started'));
```

然后，当我们用 JSON 主体发出 POST 请求时，我们得到的是我们在请求中发送的内容。

## req.cookies

我们可以获得带有`req.cookies`属性的请求所发送的 cookies。

## 请求主机名

我们可以用`req.hostname`从 HTTP 头中获取主机名。

当信任代理设置未评估为`false`时，Express 将从`X-Forwarded-Host`报头字段获取值。报头可以由客户端或代理设置。

如果有一个以上的`X-Forwarded-Host`标题，那么将使用第一个。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.json(req.hostname)
})app.listen(3000, () => console.log('server started'));
```

然后，如果没有`X-Forwarded-Host`标头，并且信任代理的评估结果不是`false`，我们将获得应用所在的域名。

## 请求. ip

我们可以用这个属性获取发出请求的 IP 地址。

## 请求方法

属性有请求的请求方法，比如 GET、POST、PUT 或 DELETE。

## 请求参数

`params`属性有来自 URL 的请求参数。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/:name/:age', (req, res) => {
  res.json(req.params)
})app.listen(3000, () => console.log('server started'));
```

然后，当我们将`/john/1`作为 URL 的参数部分传入时，我们得到:

```
{
    "name": "john",
    "age": "1"
}
```

作为上面路线的回应。

## req.query

属性从解析成对象的请求 URL 中获取查询字符串。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.json(req.query)
})app.listen(3000, () => console.log('server started'));
```

然后，当我们将`?name=john&age=1`附加到主机名的末尾时，我们得到:

```
{
    "name": "john",
    "age": "1"
}
```

从回应来看。

![](img/b0a178f8f2bf8cc9f418eead49c0df36.png)

照片由 [Fezbot2000](https://unsplash.com/@fezbot2000?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 响应对象

response 对象有一些有用的方法让我们返回各种类型的响应。

## 资源追加

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

## 资源附件

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

## res.cookie

`res.cookie`让我们向响应添加一个 cookie。

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

## res .下载

`res.download`向服务器发送文件响应。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.download('./public/foo.txt');
})app.listen(3000, () => console.log('server started'));
```

然后，当一个请求被发送到这个路径时，我们就会下载一个文件。

## 研究报告

`res.json`让我们向客户端发送一个 JSON 响应。该参数可以是任何 JSON 类型，包括对象、数组、字符串、布尔值、数字或 null。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.json({ message: 'hi' });
})app.listen(3000, () => console.log('server started'));
```

然后我们得到:

```
{"message":"hi"}
```

作为回应。

## 资源重定向

我们可以用它重定向到另一个传入字符串的 URL。例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.redirect('[http://medium.com'](http://medium.com'));
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向上面的路线发出请求时，我们将看到[http://medium.com](http://medium.com)的内容。

## 资源状态

`res.status`让我们发送状态代码响应。我们可以通过在调用`status`之后调用`end`、`send`或`sendFile`方法来使用它。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.status(403).end();
})app.listen(3000, () => console.log('server started'));
```

然后我们得到一个 403 响应。

# 结论

使用 Express 添加路线很简单。我们只需要告诉它要监听的 URL 和方法，以及处理与之匹配的请求的路由处理器。

我们可以用请求对象获得查询字符串和 URL 参数。

然后，我们可以根据自己的偏好用响应对象发送状态、文本或文件。