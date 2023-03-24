# 关于使用 Express 路由的更多信息

> 原文：<https://levelup.gitconnected.com/more-about-routing-with-express-9e0ce012bccc>

![](img/6a2d84f861a904edfab3813b46d19921.png)

马特·邓肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

路由是后端应用程序最重要的部分。Express 允许我们轻松地将 URL 路由到我们的路由处理程序代码。

在本文中，我们将了解如何使用 Express 创建不同种类的路线。

# 处理所有请求

我们可以使用`app.all`方法来处理所有请求方法。要使用它，我们可以编写如下内容:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.all('*', (req, res, next) => {
  console.log('route called');
  next();
})app.get('/', (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

当发送任何方法或 URL 时，将调用上面的`app.all`调用。它将记录`'route called'`，然后调用`next`来调用该路线的特定路线处理程序。

# 非字母符号

Express 可以匹配带有非字母符号的路线。例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/foo.bar', (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向`foo.bar`发出 GET 请求时，我们得到`hi`。

# 特殊字符

## 可选字符

如果`?`在 URL 路径中，那么它被认为是可选字符。比如`ab?c`匹配`ac`或者`abc`。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/ab?c', (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

然后当我们向`/ac`或`/abc`发出请求时，我们得到`hi`。

## 一个或多个字符

如果一个 URL 有一个`+`符号，那么它将匹配它前面的一个或多个字符。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/ab+c', (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

然后当向`/abc`、`/abbc`、`/abbbc`等发出请求时，我们会得到`hi`。

## 通配符

URL 中的`*`是通配符。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/ab*c', (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

然后我们在 URL 中的`ab`和`c`之间输入任何内容，并得到`hi`响应。

## 可选字符组

如果一个字符组用括号括起来，并且后面有一个`?`，那么它就是可选的。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/ab(cd)?ef', (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

然后当我们到`/abcdef`或`/abef`时，我们得到`hi`。

![](img/a246c259bca272b4e734b0bc568820e2.png)

Patrick Brinksma 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 正则表达式

我们也可以使用正则表达式作为路由路径。例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get(/\/a/, (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

然后当我们向`/a`发出请求时，我们得到`hi`。

当我们用以`foo`结尾的路径发出任何请求时，为了得到`hi`，我们可以写:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get(/\/.*foo$/, (req, res) => {
  res.send('hi');
})app.listen(3000, () => console.log('server started'));
```

# 路线参数

我们可以通过在参数的键名前写一个冒号来指定参数。

例如，我们可以写:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/:name/:age', (req, res) => {
  res.send(req.params);
})app.listen(3000, () => console.log('server started'));
```

然后我们得到以下响应:

```
{"name":"Mary","age":"10"}
```

`:name`和`:age`被解释为路线占位符。`req.params`将它们用作不带冒号的键。那么无论在占位符的位置设置什么，都将是相应的值。

# 路线处理程序

上面`app.get`的第二个参数中的回调是路由处理程序。通过分别用`app.post`、`app.put`和`app.delete`替换`app.get`，可以对 POST、PUT 和 DELETE 请求进行同样的操作。

我们可以用`next`函数将多个处理程序链接在一起。例如，我们可以写:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/',
  (req, res, next) => {
    console.log('handler 1 called');
    next()
  },
  (req, res) => {
    console.log('handler 2 called');
    res.send();
  })app.listen(3000, () => console.log('server started'));
```

那么我们应该看到:

```
handler 1 called
handler 2 called
```

在`console.log`输出中。

我们调用源自路由处理器参数的`next`函数来调用下一个路由处理器函数。

# 结论

使用 Express 进行路由很简单。我们可以使用字符串或正则表达式来指定路由路径。

字符串路径中的一些字符如`?`、`+`或`*`是带有可选字符或通配符的路径的特殊字符。

通过在占位符名称前使用冒号来指定管线参数的占位符。

我们调用`next`将多个路由处理器链接在一起。