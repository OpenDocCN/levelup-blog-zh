# 快速路由器对象指南—多请求和中间件

> 原文：<https://levelup.gitconnected.com/guide-to-the-express-router-object-multiple-requests-and-middleware-9d5c99b2ade6>

![](img/fb138d24421f575da6ec5fda05ed9515.png)

由 [Toa Heftiba](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Express `router`对象是中间件和路线的集合。它是主应用程序中的一个迷你应用程序。

它只能执行中间件和路由功能，不能独立运行。它本身也像中间件一样，所以我们可以将它与`app.use`一起使用，或者作为另一个路由的`use`方法的参数。

在本文中，我们将看看`router`对象的方法，包括`route`和`use`。

# 方法

## router.route(路径)

我们可以使用`router.route`通过一个函数调用链来处理多种请求。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));router.route('/')
  .all((req, res, next) => {
    next()
  })
  .get((req, res, next) => {
    res.send('foo');
  })
  .put((req, res, next) => {
    next();
  })
  .post((req, res, next) => {
    next();
  })
  .delete((req, res, next) => {
    next();
  })app.use('/foo', router);
app.listen(3000);
```

然后当我们向`/foo`发出 GET 请求时，我们得到`foo`。

我们也可以这样写:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));router.route('/')
  .all((req, res, next) => {
    console.log('all requests');
    next();
  })
  .get((req, res, next) => {
    console.log('get request');
    res.send('foo');
    next();
  })
  .put((req, res, next) => {
    console.log('put request');
    res.send('put');
    next();
  })
  .post((req, res, next) => {
    console.log('post request');
    res.send('post');
    next();
  })
  .delete((req, res, next) => {
    console.log('delete request');
    res.send('delete');
    next();
  })app.use('/foo', router);
app.listen(3000);
```

然后，当对`/foo`发出 GET、POST、PUT 或 DELETE 请求时，我们会得到相应的响应。

中间件的排序基于路由创建的时间，而不是处理程序添加到路由的时间。

![](img/edb351309d44002b0139e284029e88c8.png)

由 [Peter Berko](https://unsplash.com/@peter_berko?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## router . use([路径]、[功能、…]功能)

`router.use`方法允许我们添加一个或多个带有可选挂载路径的中间件功能，默认为`/`。

它与`app.use`类似，只是它应用于`router`对象，而不是整个应用程序。

例如，如果我们有:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));router.use((req, res, next) => {
  console.log('router middleware called');
  next();
})router.get('/', (req, res) => {
  res.send('foo');
})app.get('/', (req, res) => {
  res.send('hi');
})app.use('/foo', router);
app.listen(3000);
```

然后，当我们向`/foo`路径发出 get 请求时，我们得到`router middleware called`，因为我们要通过`router`中间件来做这件事。

另一方面，如果我们向`/`发出 GET 请求，我们不会记录该消息，因为我们没有使用`router`对象来处理该请求。

这意味着将一个中间件附加到`router`对象让我们在一个被处理的路由`router`对象被处理之前做一些事情。

我们还可以将多个中间件链接成逗号分隔的中间件列表、中间件数组或两者。

例如，我们可以写:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));const mw1 = (req, res, next) => {
  console.log('mw1 called');
  next();
}const mw2 = (req, res, next) => {
  console.log('mw2 called');
  next();
}const mw3 = (req, res, next) => {
  console.log('mw3 called');
  next();
}router.use([mw1, mw2], mw3);router.get('/', (req, res) => {
  res.send('foo');
})app.use('/foo', router);
app.listen(3000);
```

然后所有 3 个中间件，`mw1`，`mw2`和`mw3`都运行，所以我们得到:

```
mw1 called
mw2 called
mw3 called
```

如果我们向`/foo`发出 GET 请求，则记录。

我们还可以为中间件指定一个`path`。如果指定了它，那么只处理对指定路径的请求。

例如，如果我们有:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));router.use('/bar', (req, res, next) => {
  console.log('middlware called');
  next();
});router.get('/bar', (req, res) => {
  res.send('bar');
})router.get('/', (req, res) => {
  res.send('foo');
})app.use('/foo', router);
app.listen(3000);
```

然后当一个 GET 请求被发送到`/foo/bar`时，我们的中间件被调用，所以在这种情况下我们得到`middleware called`。

如果我们向`/foo`发出 GET 请求，那么我们的中间件不会被调用。

我们还可以为传递给`use`的`path`传递一个模式或正则表达式。

例如，我们可以写:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));router.use('/ab?c', (req, res, next) => {
  console.log('middlware called');
  next();
});router.get('/ab?c', (req, res) => {
  res.send('bar');
})app.use('/foo', router);
app.listen(3000);
```

然后，当我们向`/foo/abc`发出 GET 请求时，我们将`middleware called`记入日志。我们用`/foo/ac`得到同样的结果。

我们可以如下传入一个正则表达式作为`path`:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
const router = express.Router();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));router.use('/a(bc)?$', (req, res, next) => {
  console.log('middlware called');
  next();
});router.get('/a(bc)?$', (req, res) => {
  res.send('bar');
})app.use('/foo', router);
app.listen(3000);
```

然后，当我们向`/foo/abc`或`/foo/a`发出 GET 请求时，我们会将`middleware called`记入日志。

# 结论

我们可以用`route`方法在一个`router`对象中处理多种请求。它让我们将一系列方法调用链接起来，并为每种请求传递路由处理程序。

使用`use`方法，我们可以传入只适用于由`router`对象处理的路由的中间件。任何其他路由都不会调用中间件。

我们可以向`use`方法传递一个字符串或正则表达式路径，以便只处理对特定路径的请求。