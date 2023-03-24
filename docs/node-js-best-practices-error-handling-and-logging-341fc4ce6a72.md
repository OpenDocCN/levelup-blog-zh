# Node.js 最佳实践—错误处理和日志记录

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-error-handling-and-logging-341fc4ce6a72>

![](img/a7d82f27e86d7535f07b2fbb9205925f.png)

照片由[蜜牙](https://unsplash.com/@honeyfangs?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Node.js 是用 JavaScript 编写应用程序的流行运行时。为了使维护它们变得更容易，我们必须为人们设定一些准则来遵循。

在本文中，我们将研究如何记录 API 并优雅地退出进程。

# 使用 GraphQL 记录 API 错误

我们可以使用 GraphQL 库来构建我们的 API。这为我们提供了一个查询数据的沙盒。它还提供了强类型，只返回我们想要的。

它还提供了模式和注释，这样我们就可以在不添加更多文档的情况下记录我们的 API。

为了创建一个 GraphQL API，我们可以使用`graphql`和`express-graphql`包来创建一个 GraphQL API。我们通过运行以下命令来安装它:

```
npm i express-graphql graphql
```

然后，我们可以通过编写以下代码来创建一个简单的 GraphQL API:

```
const express = require('express');
const bodyParser = require('body-parser');
const graphqlHTTP = require('express-graphql');
const { buildSchema } = require('graphql');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));const schema = buildSchema(`
  type Query {
    quoteOfTheDay: String
  }
`);const root = {
  quoteOfTheDay: () => {
    return 'hello';
  },
};app.use('/graphql', graphqlHTTP({
  schema: schema,
  rootValue: root,
  graphiql: true,
}));app.listen(3000, () => console.log('server started'));
```

然后我们应该可以去`/graphql`测试我们的`quotwOfTheDay`查询。由于强键入，自动完成应该是可用的。

# 当出现未知错误时，优雅地退出进程

当未知错误发生时，我们应该退出进程，因为我们不知道它失败的原因。一种常见的做法是使用流程管理工具(如 Forever 或 PM2)重启流程。

在错误的状态下继续运行应用程序是一个坏主意。

# 使用成熟的日志记录器来增加错误可见性

为了更容易发现错误，我们可以使用类似 Winston、Bunyan、Log4js 或 Pino 的日志程序来记录应用程序中发生的活动。例如，我们可以使用 Winston 和`express-winston`包将日志添加到 Express 应用程序中，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const winston = require('winston');
const expressWinston = require('express-winston');const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use(expressWinston.logger({
  transports: [
    new winston.transports.Console()
  ],
  format: winston.format.combine(
    winston.format.colorize(),
    winston.format.json()
  ),
  meta: true,
  msg: "HTTP {{req.method}} {{req.url}}",
  expressFormat: true,
  colorize: false,
  ignoreRoute: function (req, res) { return false; }
}));app.get('/', (req, res, next) => {
  res.send('hello')
});app.get('/foo', (req, res, next) => {
  try {
    throw new Error('error')
    res.send('hello')
  } catch (err) {
    next(err)
  }
});app.use((err, req, res, next) => {
  res.send('error occurred')
})
app.listen(3000, () => console.log('server started'));
```

我们只是通过使用`expressWinston.logger`功能将记录器直接添加到我们的应用程序中。

# 使用我们最喜欢的测试框架测试错误流

依靠手工测试既慢又容易出错。因此，我们应该在我们的应用程序中添加自动化测试。它让我们通过运行几秒钟内运行的代码来检查正面和错误的场景。

有许多测试框架，如 Mocha、Chai 和 Jest，可以为我们做到这一点。

# 使用 APM 产品发现错误和停机时间

我们可以使用停机时间和性能监控产品来监控我们的应用程序的状态。

对于 Express 应用程序，我们可以添加`express-status-monitor`包来查看我们的应用程序的状态，包括 CPU 和内存使用情况、响应时间、每秒请求数等等。

我们只需运行以下命令来安装软件包:

```
npm i express-status-monitor
```

然后我们可以通过编写以下代码来使用它:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();
app.use(require('express-status-monitor')());
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  res.send('hello')
});app.listen(3000, () => console.log('server started'));
```

我们只是用`app.use(require(‘express-status-monitor’)());`把这个包直接放到我们的应用程序中。

然后，当我们转到`/status`页面时，我们将看到列出的所有性能和运行状况指标。

![](img/e89acd76a349cc7666f67167aefcf559.png)

照片由 [v2osk](https://unsplash.com/@v2osk?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 捕捉未处理的承诺拒绝和错误事件

未处理的承诺拒绝应该被捕获。因此，我们应该总是为常规承诺添加一个`catch`回调，或者为被拒绝的承诺添加`catch`阻塞。

我们还应该订阅`process.unhandledRejection`来处理错误事件。例如，如果我们试图访问一个不存在的文件并且失败了，我们应该写一些东西:

```
const fs = require('fs')
const stream = fs.createReadStream('does-not-exist')process.on('unhandledRejection', (reason, promise) => {
  console.log(`Unhandled Rejection at: ${reason.stack || reason}`)
})
```

然后，我们处理在不使应用程序崩溃的情况下试图访问一个找不到的文件所引发的错误。

为了捕捉拒绝承诺的错误，我们写:

```
Promise.reject('error')
.catch(err=> console.log(err))
```

或者:

```
(async()=>{
  try {
    await Promise.reject('error')
  }
  catch(ex){
    console.log(ex);
  }
})();
```

# 结论

我们应该捕捉代码中的错误并优雅地处理它们。此外，我们应该用记录器记录我们应用程序中的活动，并用监控工具观察我们应用程序的健康和性能。我们应用程序的文档也非常重要。