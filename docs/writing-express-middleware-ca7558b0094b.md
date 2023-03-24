# 编写快速中间件

> 原文：<https://levelup.gitconnected.com/writing-express-middleware-ca7558b0094b>

![](img/45dc186a07a8807c6ca5427c37f367ab.png)

由[托阿·海夫蒂巴](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

中间件函数是访问`request`和`response`对象的函数，以及调用下一个中间件的`next`函数。

在本文中，我们将看看 Express 中间件做什么，以及我们如何编写它们。

# 中间件的特点

中间件功能可以运行任何代码，对请求和响应对象进行更改，结束请求-响应循环，并调用堆栈中的下一个中间件。

中间件可能看起来像下面的代码:

```
app.get('/', (req, res, next) => {
  next();
});
```

上面的代码有一个请求方法，中间件将在这个方法中被调用，这个方法就是`app.get`中的`get`。

然后我们有了`'/'`，它是路线路径。

最后，我们传入的中间件函数的前两个参数分别是请求和响应对象，以及我们调用来运行下一个中间件的`next`函数。

# 例子

例如，当我们向`/`路由发出请求时，我们可以编写一个中间件来记录一些输出。

我们可以这样写:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res, next) => {
  console.log('middleware called');
  next();
});app.get('/', (req, res) => {
  res.send();
})app.listen(3000, () => console.log('server started'));
```

`next`将为`/`路线调用我们的路线处理程序。

我们应该从`console.log`输出中得到`middleware called`和一个空响应。

为了加载所有路由的中间件，我们可以使用`app.use`而不是`app.METHOD`，其中`METHOD`是小写的请求方法。

例如，我们可以编写一个应用程序范围的中间件，如下所示:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.use((req, res, next) => {
  console.log('middleware called');
  next();
});app.get('/', (req, res) => {
  res.send();
})app.listen(3000, () => console.log('server started'));
```

我们应该得到和以前一样的东西，但是如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.use((req, res, next) => {
  console.log('middleware called');
  next();
});app.get('/', (req, res) => {
  res.send();
})app.post('/foo', (req, res) => {
  res.send('foo');
})app.listen(3000, () => console.log('server started'));
```

当我们向`/`发出 get 请求并向`/foo`发出 POST 请求时，我们得到`middleware called`。

# 修改请求和响应对象

我们可以为请求和响应对象附加新的属性并设置值。

例如，我们可以写:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.use((req, res, next) => {
  req.requestTime = Date.now();
  next();
});app.get('/', (req, res) => {
  res.json(req.requestTime);
})app.post('/foo', (req, res) => {
  res.json(req.requestTime);
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向`/`发出一个 get 请求并向`/foo`发出一个 POST 请求时，我们得到请求发出的时间戳。

同样，我们可以对响应对象做类似的事情:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.use((req, res, next) => {
  res.responseTime = Date.now();
  next();
});app.get('/', (req, res) => {
  res.json(res.responseTime);
})app.post('/foo', (req, res) => {
  res.json(res.responseTime);
})app.listen(3000, () => console.log('server started'));
```

当我们向`/`发出 get 请求并向`/foo`发出 POST 请求时，我们也将获得响应的时间戳。

![](img/3b8caf30a3cb4196cde4b5df9b3b9e38.png)

照片由 [Hieu Vu Minh](https://unsplash.com/@spoony?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 可配置中间件

我们可以创建一个具有可选参数的函数，并返回一个中间件函数来创建一个可配置的中间件函数。

例如，我们可以这样写:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))const configurableMiddleware = (options) => {
  return (req, res, next) => {
    req.options = options;
    next();
  }
}app.use(configurableMiddleware({ date: new Date(), foo: 'bar' }));app.get('/', (req, res) => {
  res.json(req.options);
})app.listen(3000, () => console.log('server started'));
```

`configurableMiddleware`函数将一个选项对象作为参数，然后返回一个中间件函数，将`req.options`属性设置为`options`参数。

然后，当我们向`/`路线发出请求时，我们得到:

```
{"date":"2019-12-23T22:37:04.927Z","foo":"bar"}
```

作为回应。

# 结论

我们可以使用快速中间件功能在路由处理器或另一个中间件运行之前运行代码。

要创建一个中间件函数，我们只需创建一个函数，将请求和响应对象作为前两个参数，将下一个函数作为第三个参数。

我们可以通过添加新的属性来修改请求和响应对象，并为它们设置一个值。

然后我们调用`next`来调用下一个中间件或者路由处理器。

我们可以通过创建一个接受选项参数并返回中间件函数的函数来创建可配置的中间件。