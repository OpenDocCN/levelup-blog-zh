# 快速应用对象指南—中间件

> 原文：<https://levelup.gitconnected.com/guide-to-the-express-application-object-middleware-241468c3f708>

![](img/0608d927e938ca766bce610579cf38e5.png)

照片由[天当](https://unsplash.com/@thiendanga9?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

快速应用程序的核心部分是应用程序对象。是应用本身。

在本文中，我们将看看`app`对象的方法以及我们可以用它做什么，包括使用`app.use`来设置中间件。

# app . use([路径，]回调[，回调…])

我们可以使用`app.use`方法来安装在应用程序范围内运行的中间件。

它们可以在请求特定路径或所有路径时运行。

它采用以下参数:

*   `path` —可以是表示路径或路径模式的字符串或正则表达式。默认是`/`。
*   `callback` —处理请求的功能。它可以是一个中间件功能、一系列中间件功能、一系列中间件功能或以上所有功能的组合

我们可以提供多个回调，它们的行为就像中间件一样，但是它们调用`next('route')`来跳过剩余的中间件功能。

如果我们不需要调用剩余的中间件，我们可以使用这些来对一些路由施加先决条件，并将控制传递给路由。

由于`router`和`app`实现了中间件接口，我们可以像使用任何其他中间件功能一样使用它们。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use((req, res, next) => {
  console.log(`Request made at ${Date.now()}`)
  next();
})app.get('/', (req, res) => {
  res.send('hi');
})app.listen(3000);
```

然后我们记录每个请求的请求时间。

我们调用`next`来调用路由处理器。

中间件是按顺序运行的，所以它们在代码中的顺序很重要。

例如，如果我们有:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use((req, res, next) => {
  res.send('End with middleware');
})app.get('/', (req, res) => {
  res.send('hi');
})app.listen(3000);
```

然后我们得到`End with middleware`而不是`hi`,因为我们用中间件发送了我们的响应，这是我们首先包含的。

## **错误处理中间件**

我们可以创建自己的中间件来处理错误，而不是使用 Express 的默认错误处理程序。

错误处理程序有 4 个参数，分别是错误对象、请求对象、响应对象和下一个函数。

例如，我们可以定义如下:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  try {
    throw new Error('foo');
  }
  catch (ex) {
    next(ex);
  }
})app.use((err, req, res, next) => {
  res.status(500).send('error');
})app.listen(3000);
```

应该会显示`error`,因为我们在 GET 请求处理程序之后包含了错误处理程序。

## 小路

我们可以用字符串或正则表达式路径或路径模式作为`use`方法的第一个参数。这样，只有当路由匹配 string 或 regex 中指定的路由时，中间件才会被调用。

为了匹配一个常量路径，我们可以写如下:

```
app.use('/abc', (err, req, res, next) => {
  res.status(500).send('error');
})
```

我们可以如下匹配模式。一个`?`将使它前面的字符可选:

```
app.use('/abc?d', (err, req, res, next) => {
  res.status(500).send('error');
})
```

当对`/abcd`或`/abd`的请求出错时，上面的中间件将被调用。

`+`符号意味着它前面的一个或多个字符将被匹配。例如，如果我们有:

```
app.use('/abc+d', (err, req, res, next) => {
  res.status(500).send('error');
})
```

当向`/abccd`、`/abcccd`等发出请求时。有一个错误，上面的中间件将被调用。

`*`是通配符。我们可以如下使用它:

```
app.use('/ab*cd', (err, req, res, next) => {
  res.status(500).send('error');
})
```

然后当我们向`/abcccd`、`/abFoocd`发出请求时，我们得到`error`，等等，如果有错误的话。

为了匹配一组可选字符，我们可以在它后面使用括号和一个问号。

例如，如果我们有:

```
app.use('/a(bcd)?e', (err, req, res, next) => {
  res.status(500).send('error');
})
```

然后，当我们向有错误的`/abcde`或`/ae`发出请求时，我们得到`error`。

![](img/efca0fce9e45e7c857a6f35c3a6e1903.png)

Patrick Reichboth 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 传入中间件

除了一个中间件之外，我们还可以传入不同的中间件组合。

我们可以传入一个`router`对象作为中间件，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));const router = express.Router();
router.get('/', (req, res, next) => {
  res.send();
});app.use(router);app.listen(3000);
```

快速应用程序也是有效的中间件:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));const subApp = express();subApp.get('/', (req, res, next) => {
  res.send();
});app.use(subApp);app.listen(3000);
```

我们还可以传入一系列中间件作为参数:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
const r1 = express.Router();
r1.get('/', (req, res, next) => {
  console.log('r1 called');
  next();
});const r2 = express.Router();
r2.get('/', (req, res, next) => {
  console.log('r2 called');
  next();
});
app.use(r1, r2);app.listen(3000);
```

然后它们将按顺序被调用，所以我们得到:

```
r1 called
r2 called
```

在控制台里。

同样，我们可以传入一组中间件，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
const r1 = express.Router();
r1.get('/', (req, res, next) => {
  console.log('r1 called');
  next();
});const r2 = express.Router();
r2.get('/', (req, res, next) => {
  console.log('r2 called');
  next();
});
app.use([r1, r2]);app.listen(3000);
```

然后我们得到相同的输出，因为它们是按照列出的顺序被调用的。

它们的组合也是有效的:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
const foo = (req, res, next) => {
  console.log('foo called');
  next();
}const r1 = express.Router();
r1.get('/', (req, res, next) => {
  console.log('r1 called');
  next();
});const r2 = express.Router();
r2.get('/', (req, res, next) => {
  console.log('r2 called');
  next();
});const subApp = express();
subApp.get('/', (req, res, next) => {
  console.log('subApp called');
  next();
})app.use(foo, [r1, r2], subApp);app.listen(3000);
```

然后我们得到:

```
foo called
r1 called
r2 called
subApp called
```

从控制台。它们仍然按照列出的顺序被调用。

## 静态文件

我们可以用`express.static`中间件公开静态文件夹。例如，如果我们想在项目文件夹中公开`public`文件夹，我们可以写:

```
app.use(express.static(path.join(__dirname, 'public')));
```

# 结论

`app.use`可用于多种组合的一个或多个中间件，包括单个中间件、中间件阵列、中间件逗号分隔列表或它们的组合。

它们按照被包含的顺序被调用，下一个可以通过调用`next`来调用。

错误处理器也是中间件功能。唯一的区别是它们在请求和响应参数之前有一个`error`参数。

最后，我们可以用`express.static`中间件公开静态文件夹。