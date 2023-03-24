# Deno:使用 JWT 创建一个 Rest API

> 原文：<https://levelup.gitconnected.com/deno-create-a-rest-api-using-jwt-5141fd5b1066>

自从 V1 诞生以来，Deno 已经成为 Javascript/Typescript/Node 开发人员的时髦词汇*。让我们通过使用 JWT 创建一个安全的 Rest API 来深入了解这项技术。*

![](img/95759578aafb7ffd6b975e0e5e6a073b.png)

【https://deno.land/v1 

*⚠️最好是已经有一些关于节点及其生态系统(Express、Nodemon、Sequelize 等)的基础知识来学习本教程。*

# 🦕什么是德诺？

> Deno 是一个简单、现代和安全的 JavaScript 和 TypeScript 运行时，它使用 V8 并内置于 Rust 中。

已经有很多文章详细介绍了这个主题，所以我就不赘述了。我可以推荐这个[一个](https://blog.bitsrc.io/what-is-deno-and-will-it-replace-nodejs-a13aa1734a74)。

# 🚀介绍

自从其 [V1](https://deno.land/v1) 正式发布以来，deno 已经成为其最近几周的“流行语”(只是为了好玩，这是在谷歌上搜索“Deno”的流行曲线)

![](img/9d14de2947441fa1bd98648940ebbdd5.png)

[谷歌趋势](https://trends.google.com/trends/explore?geo=US&q=deno)

但是用这个“类型脚本和 Javascript 的安全运行时”能做什么呢？

为了更好地理解这个不断发展的项目并给出我的意见，我决定与 JWT 一起创建一个安全的 REST API，并与大家分享我的感受。

我习惯用 Node.js 和 [Express](https://expressjs.com/) 工作。

# 🎯目标

本教程的目标是重新创建一个安全的 REST API，这意味着:

*   设置服务器
*   使用 ORM 和数据库创建模型
*   CRUD 用户
*   实施与 JWT 的安全路线

# 🧰先决条件

为了用 JWT 创建我们的 API Rest secure，我将使用:

*   Deno(我推荐安装它的官方文档:[这里](https://deno.land/#installation))
*   VSCode 和 Deno 支持插件，此处提供

以及下面的包，(我将在整个教程中回到这一点) :

*   [天龙](https://deno.land/x/denon)
*   [橡木](https://deno.land/x/oak)
*   [Djwt](https://deno.land/x/djwt)
*   [Denodb](https://deno.land/x/denodb)
*   Bcrypt

# ⚙️设置

首先，让我们建立文件架构，以保持一个清晰的和“生产就绪”的项目的指导方针。

```
|-- DenoRestJwt
    |-- controllers/
    |   |-- database/
    |   |-- models/
    |-- helpers/
    |-- middlewares/
    |-- routers/
    |-- app.ts
```

如果我们开发的是 Node + Express 应用程序，我会使用 Nodemon 在代码更改后自动重启我的服务器来促进开发。

> [Nodemon](https://www.npmjs.com/package/nodemon) 是一个工具，通过在检测到目录中的文件更改时自动重启节点应用程序，帮助开发基于 node.js 的应用程序。

为了保持同样的“开发舒适度”，我决定使用 [Denon](https://deno.land/x/denon/) ，它与 Deno 同名。

```
deno install --allow-read --allow-run --allow-write -f --unstable [https://deno.land/x/denon/denon.ts](https://deno.land/x/denon/denon.ts)
```

让我们稍微定制一下 Denon 的配置。这在以后会很有用(特别是对于管理环境变量)。

```
// into denon.json
{
  "$schema": "https://deno.land/x/denon/schema.json",
  "env": {},
  "scripts": {
    "start": {
      "cmd": "deno run app.ts"
    }
  }
}
```

我们现在准备在良好的条件下开始编码！要启动 Denon，只需在终端中键入`denon start`:

```
➜ denon start
[denon] v2.0.2
[denon] watching path(s): *.*
[denon] watching extensions: ts,js,json
[denon] starting `deno run app.ts`
Compile file:///deno-crashtest/app.ts
[denon] clean exit - waiting for changes before restart
```

你可以看到我们的服务器正在运行…但它崩溃了！很正常，它在`app.ts`里没有代码可执行。

# 🖋:让我们初始化我们的服务器

我决定使用框架 [Oak](https://deno.land/x/oak) 。

> Oak 是 Deno 的 http 服务器的中间件框架，包括一个路由器中间件。这个中间件框架的灵感来源于 Koa，中间件路由器的灵感来源于@koa/router。

让我们用 Oak 初始化我们的服务器:

现在如果我们用`denon start`运行我们的服务器

```
error: Uncaught PermissionDenied: network access to "0.0.0.0:3001", run again with the --allow-net flag
```

这是 Deno 和 Node 的最大区别之一:Deno 在默认情况下是安全的，不能访问`network`。你必须授权它:

```
// into denon.json
"scripts": {
    "start": {
      // add --allow-net
      "cmd": "deno run --allow-net app.ts"
    }
  }
```

你现在可以从你的浏览器访问(尽管我建议你使用[邮递员](https://www.postman.com/))到`[localhost:3001](http://localhost:3001)`:

```
{
    "message": "It's work !"
}
```

# 👥设置数据库

我将使用 [DenoDB](https://github.com/eveningkid/denodb) 作为 ORM(特别是因为它支持 Sqlite3)。更重要的是，它看起来非常像[续集](https://sequelize.org/)(我习惯使用)。

让我们添加第一个控制器`Database`和 Sqlite3 文件。

```
|-- DenoRestJwt
    |-- controllers/
	|   |-- Database.ts
        |   |-- database/
	|   |   |-- db.sqlite
    |   |-- models/
    |-- app.ts
```

我们的 ORM 已经初始化。你可能注意到我使用了需要进一步许可的`realPathSync`。让我们在我们的`denon.json`中添加`--allow-read`和`--allow-write`:

```
"scripts": {
    "start": {
      "cmd": "deno run --allow-write --allow-read --allow-net app.ts"
    }
  }
```

剩下要做的就是通过我们的 ORM 创建用户模型:

```
|-- DenoRestJwt
    |-- controllers/
    |   |-- models/
    |       |-- User.ts
    |-- app.ts
```

这里没有什么新的东西，所以我不会详述。(ps:我用`nanoid`来管理我的 UUID，我让你读了这篇关于它的非常有趣的文章)

[](https://medium.com/javascript-in-plain-english/you-might-not-need-uuid-v4-for-generating-random-identifiers-89e8a28a7d77) [## 您可能不需要 UUID 版本 4 来生成随机标识符

### 对于每一个决定，一定要理解你为什么要做这个决定，因为你可能有另一个聪明的方法来达到同样的目的…

medium.com](https://medium.com/javascript-in-plain-english/you-might-not-need-uuid-v4-for-generating-random-identifiers-89e8a28a7d77) 

我借此机会添加一个以后会有用的函数:密码哈希。我使用 [Bcrypt](https://deno.land/x/bcrypt) 来做这件事:

最后，让我们将模型链接到 ORM:

很好！现在我们的服务器和数据库已经就绪，是时候初始化帐户创建路径了…

# 📙用户控制器

没有比好的 CRUD 更基本的了:

```
|-- DenoRestJwt
    |-- controllers/
    |   |-- Database.ts
    |   |-- UserController.ts
```

我只是使用 ORM 提供的方法。我们现在只需要管理 JWT 一代。

# 🛣设置路由器

现在是时候创建我们不同的路线并调用我们新编码的控制器了。

```
|-- DenoRestJwt
    |-- routers
        |-- UserRoute.ts
```

我们所要做的就是从控制器中调用我们的逻辑。我使用 [HTTP 方法](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)来清楚地定义我的路线。我还创建了助手来管理我的错误返回。我邀请你直接从项目 [GitHub](https://github.com/Trobyss/deno-api-rest-jwt) 获得它们！我们所要做的就是在应用程序中调用路由器:

# 🛡安全和智威汤逊

现在是时候给这个项目增加一些安全性了！我将使用 [JWT](https://jwt.io/) 来做到这一点。

![](img/aae55f00ccd2b80fd8f38cfe284458a5.png)

[原版](https://auth0.com/learn/json-web-tokens/)

## 1.创建一条安全路线

首先，我们将建立一个安全中间件，它:

*   检查查询中是否存在“授权”标头
*   拿回来
*   检查其有效性
*   返回错误/接受请求并调用私有路由

我会用库 [Djwt](https://deno.land/x/djwt) 。

```
|-- DenoRestJwt
    |-- middlewares/
    |   |-- jwt.ts
```

我们的函数必须接受查询上下文的参数，从消息头中提取令牌，检查其有效性，并采取相应的行动。

注意，我们需要一个密钥来加密我们的令牌。为此我使用了 Deno 环境变量。因此，我们需要对 Denon 配置进行一些更改:添加变量并允许 Deno 检索环境变量。

```
{
  "$schema": "<https://deno.land/x/denon/schema.json>",
  // Add env variable
  "env": {
    "SECRET": "ADRIEN_IS_THE_BEST_AUTHOR_ON_MEDIUM"
  },
  "scripts": {
    "start": {
      // add the permission with --allow-env
      "cmd": "deno run --allow-env --allow-read --allow-net app.ts"
    }
  }
}
```

(ps:如果你想保护你的环境变量，我推荐这个教程)

[](https://medium.com/javascript-in-plain-english/im-now-versioning-my-app-secrets-in-git-here-is-why-you-should-do-the-same-2a72c1a49039) [## 我现在在 Git 中对我的应用程序秘密进行版本控制，这就是为什么你应该做同样的事情。

### 这应该是一个不好的做法，但是当你有很多配置和管理秘密的时候，你可能需要它…

medium.com](https://medium.com/javascript-in-plain-english/im-now-versioning-my-app-secrets-in-git-here-is-why-you-should-do-the-same-2a72c1a49039) 

然后让我们创建我们的私人路线。

```
|-- DenoRestJwt
    |-- routers
        |-- UserRoute.ts
        |-- PrivateRoute.ts
```

在调用我们的路线之前，只需调用我们的方法:

不要忘记将其添加到我们的应用程序中:

如果我们尝试在`/private` 上调用我们的 API，我们会得到很好的响应:

```
{
    "message": "Unauthorized"
}
```

## 2.JWT 一代

现在是时候在用户登录时设置令牌生成了。请记住，我们在控制器中留了一个`// TODO generate JWT`。在完成之前，我们将首先向我们的用户模型添加一个静态方法来生成一个令牌。

让我们在控制器中调用这个方法:

最后，让我们将这个逻辑添加到路由器中:

现在，如果我们尝试连接，我们有:

```
// localhost:3001/login
{
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IlEyY0ZZcUxKWk5Hc0toN0FWV0hzUiIsImV4cCI6MTU5MDg0NDU2MDM5MH0.drQ3ay5_DYuXEOnH2Z0RKbhq9nZElWCMvmypjI4BjIk"
}
```

*(之前别忘了创建账号；))*

让我们将这个令牌添加到我们的`Authorization`头中，并再次调用我们的私有路由:

```
// localhost:3001/private with token in headers
{
    "message": "Connected !"
}
```

太好了！我们的安全 API 已经就位👏

你可以在我的 Github 上找到这个项目:[这里](https://github.com/Trobyss/deno-api-rest-jwt)(我加邮差集合做请求)。

# 💭我对德诺的感觉

我决定与你分享我对 Deno 的感受，它可能会给你第一印象:

*   通过 URL 导入模块在开始时有点反直觉:您总是想创建一个`npm i`或`yarn add`。此外，我们必须运行 deno 来缓存我们的导入，只有这样我们才能访问自动完成。

```
The remote module XXX has not been cached
```

*   我总是在我的 Javascript 项目上使用 TypeScript，所以一开始我一点也没有迷失。相反，我很熟悉它。
*   有趣的一点:权限。我认为这是一件好事，例如 Deno 需要权限才能访问网络。作为开发人员，它迫使我们意识到程序的访问和权利。(更安全)
*   我们一开始有点困惑去哪里找包([https://deno.land/x](https://deno.land/x)→460 包和 NPM →+ 100 万)
*   你永远无法确定一个包是否也能在 Deno 上工作。你总想更接近你所知道的，用 on Node 把它转移到 Deno。不知道是好事还是坏事，还是 javascript…

*感谢*[*@ Aschen*](https://github.com/Aschen)*对 Github 的挑剔反馈*