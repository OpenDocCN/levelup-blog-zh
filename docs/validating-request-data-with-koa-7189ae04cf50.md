# 使用 Koa 验证请求数据

> 原文：<https://levelup.gitconnected.com/validating-request-data-with-koa-7189ae04cf50>

![](img/1c3e6920a94c1e37c6e80711270a33bf.png)

照片由[维姆·博伦](https://unsplash.com/@wimpieb?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Koa 是一个小框架，让我们可以创建在 Node.js 平台上运行的后端应用程序。

在本文中，我们将看看如何用 Koa 验证请求数据类型。

# 使用 request.is 方法验证请求数据类型

我们可以用我们正在检查的数据类型的字符串调用`ctx.is`方法，以检查来自`Content-Type`头的值的数据类型是否与我们在参数中拥有的匹配。

它可以采用一种或多种数据类型。例如，我们可以如下使用它:

```
const Koa = require('koa');
const app = new Koa();app.use(async (ctx, next) => {
  if (ctx.is('text/*', 'text/html')){
    ctx.body = 'valid';
  }
  else {
    ctx.body = 'invalid';
  }
});app.listen(3000);
```

在上面的代码中，我们用两个数据类型的字符串调用了`ctx.is`方法，这两个数据类型是`‘text/*’`和`‘text/html’`，以检查传入的请求是否有这些值作为其`Content-Type`头的值。

如果包含这些值中的一个，那么它返回`'valid'`作为响应体。

否则返回`'invalid'`。

# 内容协商

内容协商是一种机制，用于从同一 URL 提供不同的资源表示。我们可以使用用户代理来指定哪个最适合用户。

当向客户机发出请求时，我们可以在请求中使用`Accept`、`Accept-Charset`、`Accept-Encoding`或`Accept-Language`头，让服务器知道客户机接受哪种数据。

有了这些数据，服务器可以返回一个响应，其中包含最适合客户端的数据类型。

例如，我们可以如下使用它:

```
const Koa = require('koa');
const app = new Koa();app.use(async (ctx, next) => {
  if (ctx.accepts('html')){
    ctx.body = 'accepts html';
  }
  else if (ctx.accepts('application/json')){
    ctx.body = 'accepts json';
  }
  else if (ctx.accepts('image/png')){
    ctx.body = 'accepts png';
  }
  else {
    ctx.body = 'accepts everything';
  }
});app.listen(3000);
```

在上面的代码中，我们用 content-type 字符串调用了`ctx.accepts`方法，我们希望在上面列出的那些头中检查该字符串，这样我们就可以返回相应的响应。

然后，当我们向我们的应用程序发出一个请求，其中一个头的值是`application/json`,然后我们得到返回的字符串`'accepts json'`作为响应。

# 检查客户端接受的编码

我们可以使用`ctx.acceptsEncodings`方法来检查客户端接受哪种编码。

它接受一个或多个编码类型字符串作为其参数，或者它可以是一个编码类型字符串数组。

例如，我们可以如下使用它:

```
const Koa = require('koa');
const app = new Koa();app.use(async (ctx, next) => {  
  if (ctx.acceptsEncodings('gzip') === "gzip"){
    ctx.body = 'accepts gzip';
  }
  else {
    ctx.body = 'does not accept gzip';
  }});app.listen(3000);
```

在上面的代码中，我们用参数`'gzip'`调用了`ctx.acceptEncodings`方法。该方法返回最佳匹配的编码。

然后，如果我们用`Accept-Encoding`头`gzip, deflate, br`向我们的应用程序发出请求，我们将从应用程序得到响应`'accepts gzip'`。

如果没有给定参数，则返回所有接受的编码。

# 检查客户端接受的字符集

我们可以调用`acceptsCharsets`来检查客户端接受的字符集。

它还接受一个或多个字符串作为接受的字符集名称，或者一个包含这些字符集名称的字符串数组。

例如，我们可以如下使用它:

```
const Koa = require('koa');
const app = new Koa();app.use(async (ctx, next) => { if (ctx.acceptsCharsets('utf-8') === 'utf-8'){
    ctx.body = 'accepts utf-8';
  }
  else {
    ctx.body = 'does not accept utf-8';
  }});app.listen(3000);
```

上面的代码类似于`acceptEncodings`代码。

如果我们发出一个不包含包含`utf-8`的`Accept-Charset`头的请求，那么我们将得到作为响应返回的`‘does not accept utf-8’`。

否则，无论我们是否使用以`utf-8`为值的`Accept-Charset`头显式地发出请求，我们都将得到`'accepts utf-8'`的返回。

![](img/799e233a12575e1c1baa282d31d6cf29.png)

吉勒·魅兰-蒙奈在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 检查请求的语言

`ctx.acceptsLanguages`让我们根据`Accept-Language`头检查请求的语言。

它类似于上面的另外两个方法，只是带有参数和返回内容。

# 获取任何标题字段

我们可以用`ctx.get`方法获得任何请求头字段。例如，我们可以编写以下代码来实现这一点:

```
const Koa = require('koa');
const app = new Koa();app.use(async (ctx, next) => {
  ctx.body = ctx.get('foo');
});app.listen(3000);
```

在上面的代码中，我们用标题名`'foo'`调用了`get`方法。如果存在，它返回`'foo'`头的值。

然后，当我们用带有值`bar`的`foo`头发出请求时，我们将看到作为响应返回的`'bar'`。

# 结论

我们可以使用`ctx.is`方法来验证发送到服务器的数据类型。

做内容协商，可以用`accepts`的方法。要检查语言、编码或字符集，有一些方法可以做到。

最后，我们可以用`get`方法获得任何请求头的值。