# Node.js 最佳实践—生产和安全

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-production-and-security-bef27571cc97>

![](img/8b743a27073ef2673950a372b010187d.png)

国际国王教会在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量应用程序。为了使维护它们变得更容易，我们必须为人们设定一些准则来遵循。

在本文中，我们将研究节点应用程序的部署和安全相关的最佳实践。

# 设置 NODE _ ENV =生产

我们应该为生产环境设置`NODE_ENV`到`production`，为开发设置`development`。将`NODE_ENV`设置为`production`会激活与生产相关的优化。

忽略这一属性可能会显著降低性能。例如，在没有将`NODE_ENV`设置为`production`的情况下使用 Express 会使其变慢 3 倍。

在 Express 中，如果`NODE_ENV`是`production`，它将缓存视图模板和从 CSS 扩展生成的 CSS，并且它还将生成不太详细的错误消息，以防止它们被公开。

# 设计自动化、原子化和零停机部署

部署需要自动化，这样一旦自动化部署管道建立起来，我们就不必担心它了。这样，我们就不必担心遇到人为错误导致的问题。手动部署也比自动部署慢得多。

更好的是，我们可以用 Docker 创建自动化的沙盒环境，这样每个应用程序都可以在自己的环境中运行。这解决了许多运行时、包和环境变量冲突的问题。

否则，我们每次都必须手动部署，这可能会导致问题。此外，我们必须观察它，以确保它已经完成，所以人们不太可能部署应用程序，因为这是一个如此痛苦和容易出错的过程。

# 使用 Node.js 的 LTS 版本

Node 的长期服务(LTS)版本比非 LTS Node 版本受支持的时间更长。他们不断收到重要的错误修复、安全更新和性能改进。非 LTS 版本仅在其更新版本发布后的几个月内受支持。

因此，为了避免一直升级 Node，我们应该使用 LTS 版本。

# 拥抱 Linter 安全规则

像`eslint-plugin-security`这样的 ESLint 插件会检查我们代码中的安全问题，这样我们就可以尽早修复它们。这有助于捕捉安全弱点，如`eval`、调用子进程或导入带有用户输入字符串的模块。

有了它，我们可以在提交或推送代码之前进行检查。这样，我们团队中的每个人都会始终遵循安全最佳实践。

# 使用中间件限制并发请求

拒绝服务(DOS)攻击非常流行，也很容易实施。因此，我们应该使用负载平衡、云防火墙、反向代理或应用程序包等服务来实现速率限制。

为了在我们的应用端点实现速率限制，我们可以使用像`rate-limiter-flexible`或`express-rate-limit`这样的包。

例如，我们可以使用下面的`express-rate-limit`为我们的 Express 应用程序中的所有端点添加速率限制:

```
const express = require('express');
const bodyParser = require('body-parser');
const rateLimit = require("express-rate-limit");const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
});app.use(limiter);app.get('/', (req, res, next) => {
  res.send('hello');
});app.listen(3000, () => console.log('server started'));
module.exports = app;
```

正如我们所看到的，我们只需添加一个简单的中间件来防止对我们的应用程序的拒绝服务攻击。因此，对于任何规模的应用程序，我们都应该从一开始就这样做。

![](img/82160d77cff27717f1146154868eacc7.png)

照片由[mar liese stree land](https://unsplash.com/@marliesebrandsma?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 从配置文件中提取机密或使用包对其进行加密

配置文件通常包含大多数人不应该看到的秘密数据。因此，我们不应该将它们存储在源代码中。相反，我们必须确保像 Vault、Docker secrets 这样的秘密管理系统，或者使用环境变量。

我们应该有预提交或推送挂钩来防止意外地将秘密提交给代码。源代码管理可能会被错误地公开，这将秘密暴露给其他不应该看到它们的人。

例如，我们可以使用`dotenv`包来存储节点应用程序的环境变量。为了使用它，我们写:

```
const express = require('express');
const bodyParser = require('body-parser');
require('dotenv').config();const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.send(process.env.DB_HOST);
});app.listen(3000, () => console.log('server started'));
```

鉴于我们的`.env`文件具有:

```
DB_HOST=foo
```

有一次我们跑了:

```
require('dotenv').config();
```

然后`process.env.DB_HOST`返回`'foo'`，所以如果我们转到`/`，我们会看到`foo`显示在屏幕上。

# 结论

我们应该确保`NODE_ENV`被设置为`production`，这样优化就可以应用于像 Express 这样的包。

任何部署都应该是自动化的。另外，Node.js 应该是 LTS 版本。此外，我们应该检查代码中的安全漏洞，并对 API 端点进行速率限制，以防止拒绝服务攻击。

最后，我们应该在代码中保留秘密。