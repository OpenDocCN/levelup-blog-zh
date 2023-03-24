# Node.js 基础—调试应用程序

> 原文：<https://levelup.gitconnected.com/node-js-basics-debugging-apps-2ef6be5e6b8a>

![](img/9f260a556bf6f6357b261ac64fc152d1.png)

[Zan](https://unsplash.com/@zanilic?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我们将了解如何开始使用 Node.js 创建程序。

# 与摩根一起伐木

为了让我们更容易地调试 Node.js 应用程序，我们可以使用`morgan`包在我们的应用程序中添加一个日志记录器。

要安装它，我们运行:

```
npm i morgan
```

然后我们可以通过写来使用它:

```
const Morgan = require('morgan'),
  Router = require('router'),
  http = require('http');
router = new Router();
router.use(Morgan('tiny'));http.createServer(function(request, response) {
  router(request, response, function(error) {
    if (!error) {
      response.writeHead(404);
    } else {      
      console.log(error.message, error.stack);
      response.writeHead(400);
    }
    response.end('\n');
  });
}).listen(8000);
```

我们通过使用`Morgan('tiny')`中间件调用`router.use`方法，将`Morgan`中间件添加到我们的`router`中。

现在我们看到登录我们的应用程序。

现在我们可以去掉`console.log`并写下:

```
const Morgan = require('morgan'),
  Router = require('router'),
  http = require('http');
router = new Router();
router.use(Morgan('tiny'));http.createServer(function(request, response) {
  router(request, response, function(error) {
    let info = process.versions;
    info = JSON.stringify(info);
    response.writeHead(200, {
      'Content-Type': 'application/json'
    });
    response.end(info); });
}).listen(8000);
```

仍然可以伐木。

或者，我们可以使用`bunyan`包来进行日志记录。

要安装它，我们运行:

```
npm i bunyan
```

然后我们可以通过写来使用它:

```
const Bunyan = require('bunyan');  
const logger = Bunyan.createLogger({
  name: 'example'
});
logger.info('Hello logging');
```

我们需要`bunyan`包。然后我们用`createLogger`方法创建我们的记录器。

`name`是记录器的名称。

然后我们使用`logger.info`方法来记录我们想要的任何东西。

我们可以通过使用各种方法用其他日志记录级别来记录项目。

例如，我们可以写:

```
const Bunyan = require('bunyan');
const logger = Bunyan.createLogger({
  name: 'example'
});
logger.info('Hello logging');
logger.trace('Trace');
logger.debug('Debug');
logger.info('Info');
logger.warn('Warn');
logger.error('Error');
logger.fatal('Fatal');
```

然后，我们获取记录了各种属性`level`值的对象，让我们知道日志项的严重性级别。

我们也可以写:

```
const Bunyan = require('bunyan');
const logger = Bunyan.createLogger({
  name: 'example',
  level: Bunyan.TRACE});
logger.info('Hello logging');
logger.trace('Trace');
logger.debug('Debug');
logger.info('Info');
logger.warn('Warn');
logger.error('Error');
logger.fatal('Fatal');
```

来设置我们调用`createLogger`时的电平。

使用 Bunyan，我们还可以将记录的数据写入文件。

为此，我们写道:

```
const Bunyan = require('bunyan');
const logger = Bunyan.createLogger({
  name: 'example',
  streams: [{
      level: Bunyan.INFO,
      path: './log.log'
    },
    {
      level: Bunyan.INFO,
      stream: process.stdout
    }
  ]
});
logger.info('Hello logging');
logger.trace('Trace');
logger.debug('Debug');
logger.info('Info');
logger.warn('Warn');
logger.error('Error');
logger.fatal('Fatal');
```

我们添加了`streams`属性来添加对象，以指定日志数据的位置。

`streams`数组的第一个条目具有`path`属性来指定要写入的位置。

第二个对象指定我们写入 stdout，这意味着我们在屏幕上打印项目。

# 结论

我们可以用各种记录器添加日志。

我们可以使用 Morgan 或 Bunyan 为 Node.js 应用程序添加日志功能。