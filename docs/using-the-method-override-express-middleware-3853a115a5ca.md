# 使用方法-覆盖 Express 中间件

> 原文：<https://levelup.gitconnected.com/using-the-method-override-express-middleware-3853a115a5ca>

![](img/9f3d759192d7872deb15629eb4aff779.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

中间件允许我们在不支持 HTTP 动词的客户机上使用 PUT 和 DELETE。

在本文中，我们将研究如何使用它来调用这些路由，而不需要对我们的后端进行大的修改。

# 装置

`method-override`中间件以节点包的形式提供。

要安装它，我们可以运行:

```
npm install method-override
```

来安装它。

# 怎么用？

我们可以通过在应用程序中包含中间件来使用它。这需要一些争论。

`methodOverride(getter, options)`函数返回一个新的中间件函数，用一个新值覆盖`req.method`属性。该值将从提供的`getter`中检索。

## 吸气剂

第一个是`getter`，它是一个覆盖请求值的函数。它用于查找请求的被覆盖的请求方法。默认情况下是从`X-Http-Method-Override`请求头中获取。

如果头字段字符串以`X-`开始，那么它被视为头的名称，并且头用于方法覆盖。如果请求多次包含相同的标头，则使用第一个出现的标头。

所有其他字符串都被视为 URL 查询字符串中的一个关键字。

## 选项.方法

这允许指定请求必须是什么方法，以便检查方法覆盖值。默认情况下，只检查 POST 方法。

这里可以指定更多的方法，但是当请求通过缓存时，可能会产生安全问题和奇怪的行为。数组中的值必须是大写的。

`null`可以指定允许的所有方法。

# 例子

## 用标题覆盖

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const methodOverride = require('method-override')
const app = express();app.use(bodyParser.json());
app.use(methodOverride('X-HTTP-Method-Override'))app.get('/', (req, res, next) => {
  res.send('GET request called');
})app.put('/', (req, res, next) => {
  res.send('PUT request called');
})app.listen(3000);
```

然后，当我们向`/`发送一个 POST 请求，并将`X-HTTP-Method-Override`头设置为`PUT`时，我们得到`PUT request called`。

这是因为`methodOverride`中间件寻找`X-HTTP-Method-Override`头并得到值`PUT`，然后它将请求重定向到我们的 PUT 请求处理器。

请注意，我们没有 POST 请求处理程序。在到达任何路由处理程序之前，`methodOverride`会处理好一切。

## 用查询字符串值覆盖

我们还可以通过将代码更改为以下内容，用查询字符串覆盖该方法:

```
const express = require('express');
const bodyParser = require('body-parser');
const methodOverride = require('method-override')
const app = express();app.use(bodyParser.json());
app.use(methodOverride('_method'))app.get('/', (req, res, next) => {
  res.send('GET request called');
})app.put('/', (req, res, next) => {
  res.send('PUT request called');
})app.listen(3000);
```

例如，如果我们向`/?_method=PUT`发出 POST 请求

然后我们得到和以前一样的响应，因为`methodOverride`中间件将我们的 POST 请求重定向到 PUT 请求。

![](img/fcbf1aff728413618bea69e3e8ac4ada.png)

Yannis Papanastasopoulos 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 自定义 Getter

我们可以为`getter`指定一个函数，从我们想要的源获取请求方法。

例如，我们可以从请求主体的`method`字段中获取它，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const methodOverride = require('method-override')
const app = express();
const getter = (req, res) => {
  if (req.body && typeof req.body === 'object' && 'method' in req.body) {
    const method = req.body.method
    delete req.body.method
    return method
  }
}app.use(bodyParser.json());
app.use(methodOverride(getter))app.get('/', (req, res, next) => {
  res.send('GET request called');
})app.put('/', (req, res, next) => {
  res.send('PUT request called');
})app.listen(3000);
```

然后当我们用 body 向`/`发出 POST 请求时:

```
{
  "method": "PUT"
}
```

我们得到了`PUT request called`,因为我们指定的`getter`函数获取了请求主体的`method`字段，分配给另一个常量，从主体中删除了`method`字段，并返回了`method`的值。

# 结论

我们可以使用`method-override`包来重写方法，使请求不同于原始请求。

为了做到这一点，我们用`app.use`或`router.use`方法包含包返回的中间件，然后我们可以设置查找被覆盖方法名称的位置。

然后中间件将使用它的返回值用被覆盖的方法覆盖原始请求的方法。

它可以在请求头、查询字符串或请求体等任何地方查找方法名。