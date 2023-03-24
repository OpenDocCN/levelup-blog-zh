# Express 应用程序的性能和可靠性最佳实践—代码更改

> 原文：<https://levelup.gitconnected.com/performance-and-reliability-best-practices-for-express-apps-code-changes-21f44007c150>

![](img/21ad915943478a3f7b06401554f5fcd5.png)

照片由[桑嘎日玛罗曼塞利亚](https://unsplash.com/@sxy_selia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我们将 Express 应用程序用于生产用途时，即由外部用户使用时，我们应该考虑性能。

在本文中，我们将了解在生产环境中运行 Express 应用程序时的一些性能和可靠性最佳实践。

# 代码更改使我们的应用程序更快

## 使用 Gzip 压缩

我们可以使用 Gzip 压缩来压缩我们的响应数据，以使我们的用户下载更少的数据。

为此，我们可以使用`compression`中间件来减小响应体的大小。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const compression = require('compression');
const app = express();
app.use(compression());
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.send('foo');
})app.listen(3000);
```

我们只需要补充一点:

```
app.use(compression());
```

启用我们的响应数据的 GZip 压缩。

对于高流量网站，我们应该在反向代理中进行压缩，这样我们就不必使用`compression`中间件。

## 不要使用同步函数

同步函数会占用我们应用程序的执行过程，直到它们返回。一个同步调用可能只持续几毫秒或几微秒。但是如果我们做了很多，比如在一个生产应用程序中，它肯定会增加。

在 Node.js 4.0+或 io.js 2.10+中，我们可以使用`--trace-sync-io`标志在调用同步代码时输出警告和堆栈跟踪。

在将我们的应用程序投入生产之前，我们可以用它来用异步代码替换同步代码。

# 可靠性最佳实践

## 正确记录

出于调试的目的，我们可以使用类似于`debug`模块的东西来记录日志。它还使我们能够使用 DEBUG 环境变量来控制向`console.err`发送什么调试消息。

对于记录应用活动，我们可以使用像 [Winston](https://www.npmjs.com/package/winston) 或 [Bunyan](https://www.npmjs.com/package/bunyan) 这样的记录库。

它们非常相似，但是 Winston 的输出更加人性化。

![](img/4c3f2954cdf1951bd0b603180b3ecf8a.png)

[Yassine Laaroussi](https://unsplash.com/@larrozzi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 正确处理异常

当节点应用程序遇到未捕获的异常时，它会崩溃，因此我们应该通过捕获这些异常来预防应用程序在生产中崩溃。

对于同步代码和`async`函数，我们可以使用`try...catch`。为了从被拒绝的承诺中捕捉错误，我们可以用自己的回调函数调用`catch`方法来处理错误。

例如，我们可以捕获同步代码中的错误，并将错误发送到我们的错误处理程序，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  try {
    throw new Error('error');
  }
  catch (ex) {
    next(ex);
  }
})app.use((err, req, res, next) => {
  res.send('error occurred');
})app.listen(3000);
```

注意，我们必须把:

```
app.use((err, req, res, next) => {
  res.send('error occurred');
})
```

以便在我们的路由中调用`next`时，错误处理程序将被实际调用。调用`next`调用添加的下一个中间件。

`try...catch`也与`async`功能一起工作:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', async (req, res, next) => {
  try {
    await Promise.reject('error');
  }
  catch (ex) {
    next(ex);
  }
})app.use((err, req, res, next) => {
  res.send('error occurred');
})app.listen(3000);
```

当一个承诺被拒绝时，我们可以`catch`它并将其发送给我们的错误处理程序。

对于承诺，我们可以使用自己的回调函数调用`catch`,如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  Promise
    .reject('error')
    .catch(ex => next(ex));
})app.use((err, req, res, next) => {
  res.send('error occurred');
})app.listen(3000);
```

这将把我们拒绝的承诺错误发送给我们的错误处理程序。

我们不应该听`uncaughtException`事件。这将改变遇到异常的流程的默认行为。尽管有异常，这个过程还是会运行，这并不好，因为我们想在应用程序不崩溃的情况下知道错误。

听`uncaughtException`只是把错误扫在地毯下。崩溃并重新启动是从错误中恢复的更好方法。

我们可以如下处理事件发射器发出的错误:

```
const express = require('express');
const bodyParser = require('body-parser');
const EventEmitter = require('events');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));const eventEmitter = new EventEmitter();app.get('/', (req, res, next) => {
  eventEmitter.emit('error', new Error('error!'));
  eventEmitter.on('error', next).pipe(res);
})app.use((err, req, res, next) => {
  res.send('error occurred');
})app.listen(3000);
```

我们应该得到`error occurred`，因为`eventEmitter`的`error`事件触发了对`next`函数的调用。

像 streams 这样的 Node 中的许多东西都扩展了 EventEmitter，所以这可以处理它们发出的错误。

# 结论

为了让我们的应用程序更快，我们可以压缩我们的响应，这样用户就不必下载那么多数据来获得我们的响应。

此外，我们不应该使用同步函数，因为它们会在主执行过程完成之前一直拖住它。

对于伐木，我们应该考虑像[温斯顿](https://www.npmjs.com/package/winston)或[班扬](https://www.npmjs.com/package/bunyan)这样的替代品。

为了正确处理异常，我们可以使用`try...catch`同步代码和`async`函数。

对于承诺，我们可以使用`catch`方法和我们自己传入的回调来处理错误。

对于事件发射器错误，我们可以在发出`error`事件时调用`next`。

在每种情况下，我们都可以调用`next`来调用我们自己的错误处理程序或 Express 的默认错误处理程序。