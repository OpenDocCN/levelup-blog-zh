# 使用 Lightship 检查 Express 应用程序

> 原文：<https://levelup.gitconnected.com/using-lightship-to-check-express-apps-3ccbca6fb538>

![](img/4788fdc770d1fedb05980935266e6ad7.png)

迪伦·麦克劳德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Lightship 是一个向我们的 Express 应用程序添加健康、就绪和活性检查的包。

它是一个独立的 HTTP 服务，作为单独运行，允许我们拥有健康/就绪/活动 HTTP 端点，而无需向公众公开它们。

在本文中，我们将看看如何将 Lightship 添加到我们的 Express 应用程序中。

# 正常关机

优雅地关闭我们的应用程序包括向我们的应用程序发送 SIGTERM 信号，通知应用程序它将被杀死。一旦我们的应用程序收到信号，它将停止接受新的请求，完成当前的请求，并清理它使用的资源，如数据库连接和文件锁。

# 健康检查

有两种健康检查。它们包括:

*   活动—确定何时重新启动容器
*   就绪—确定容器何时准备好开始接受流量。

# 添加光船

要添加 Lightship，我们只需运行以下命令来安装 Lightship 包:

```
npm i lightship
```

然后，我们可以在代码中添加以下代码行:

```
const {
  createLightship
} = require('lightship');
const lightship = createLightship();app.listen(3000, () => lightship.signalReady());lightship.registerShutdownHandler(() => {
  server.close();
});lightship.signalReady();
```

我们共同拥有:

```
const express = require('express');
const bodyParser = require('body-parser');
const {
  createLightship
} = require('lightship');
const lightship = createLightship();
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.send('ok');
});app.listen(3000, () => lightship.signalReady());lightship.registerShutdownHandler(() => {
  server.close();
});lightship.signalReady();
```

如果 Lightship 运行在非 Kubernetes 环境中，它将在任何可用的 HTTP 端口上运行其 HTTP 服务。

![](img/71d2f7730ac622308535c3c4aaac701e.png)

[朱莉·诺斯](https://unsplash.com/@ebplace?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

运行后，我们有 3 个端点:

*   `/health`
*   `/live`
*   `/ready`

`/health`端点描述了我们应用程序的当前状态。它可以响应:

*   200 状态码和`SERVER_IS_READY`消息，这意味着它准备好接受新的连接
*   500 状态码和`SERVER_IS_NOT_READY`消息，服务器初始化时返回
*   服务器关闭时的 500 状态代码和`SERVER_IS_SHUTTING_DOWN`消息。

`/live`端点可以响应:

*   200 状态和信息`SERVER_IS_NOT_SHUTTING_DOWN`
*   500 状态和信息`SERVER_IS_SHUTTING_DOWN`

我们可以使用这个端点进行活性探测

`/ready`端点可以响应:

*   200 状态代码和信息`SERVER_IS_READY`
*   500 状态代码和信息`SERVER_IS_NOT_READY`

此端点用于配置就绪探测器。

我们可以根据自己的要求发出不同的信号。例如，我们可以通过调用`lightship.signalNotReady()`将服务器状态更改为`SERVER_IS_NOT_READY`,如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const {
  createLightship
} = require('lightship');
const lightship = createLightship();
const app = express();
const period = 1000;
let runningTotal = 0;
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  runningTotal++; setTimeout(() => {
    runningTotal--; if (runningTotal < 20) {
      lightship.signalReady();
    }
    else {
      lightship.signalNotReady();
    }
  }, period); res.send('ok');
});app.listen(3000, () => lightship.signalReady());lightship.registerShutdownHandler(() => {
  server.close();
});lightship.signalReady();
```

# 结论

我们可以使用 Lightship 向我们的应用程序添加健康检查，以确保它正在运行。

它让我们发送信号来指示我们的应用程序是否正在运行。我们可以通过使用 Lightship 提供的端点来检查我们的应用程序和服务器的状态。

Lightship 在非 Kubernetes 环境中作为单独的服务运行。