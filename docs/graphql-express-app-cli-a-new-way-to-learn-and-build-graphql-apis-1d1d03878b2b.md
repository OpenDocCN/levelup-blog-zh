# GraphQL-Express-App CLI —学习和构建 GraphQL APIs 的新方法

> 原文：<https://levelup.gitconnected.com/graphql-express-app-cli-a-new-way-to-learn-and-build-graphql-apis-1d1d03878b2b>

![](img/5a9aff259bc7af74c75419175ceaf623.png)

照片由[王思然·哈德森](https://unsplash.com/@hudsoncrafted?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

> 嘿，伙计们，如果你是 OSS(开源软件)开发的新手，并且想开始做开源贡献，那么这里有一个适合你的组织，在这里你可以启动你的项目，也可以为以前启动的项目做贡献！
> 
> 如果你想在 QuBytes-OSS 开始一个项目，请在 Twitter 上 Ping 我！
> 
> 组织链接:【https://github.com/QuBytes-OSS 

我一直在用 GraphQL 学习和构建一些个人项目。在这个过程中，我需要一些新项目的样板文件。

它激励我开始做一些事情，加快开始一个新项目的进程。任何人都可以轻松地用 MongoDB 数据库构建 GraphQL-Express 服务器。

因此，我构建了一个 graphql-express-app CLI 工具，任何人都可以使用它来启动他们的新 graph QL-Express 项目和 MongoDB 数据库。

# 特点:

1.  命令行界面，用于更快地生成项目。
2.  从 CLI 初始化 Git。
3.  从 CLI 安装依赖项。
4.  TypeScript 支持(即将推出！)

> 什么是 graphql-express-app CLI？

它是一个 CLI(命令行界面),用于启动一个 GraphQL-Express 服务器，该服务器具有一些预先设计的 MongoDB 模型，用于处理数据库和一个模式，以访问数据库并执行 GraphQL 查询和变异。

> 从 CLI 中可以期待什么？

CLI 将引导您创建一个简单的 GraphQL-Express 应用程序，您可以根据项目需求进一步构建该应用程序。

> 生成的项目中有哪些依赖项？

构建此 CLI 是为了初始化 GraphQL-Express 服务器。它使用以下依赖项来启动一个新项目。

*   [graphql](https://github.com/graphql/graphql-js) :构建 graphql 模式，设计 GraphQL 类型，并使用它们来创建查询和变异。
*   [express](https://github.com/expressjs/express) :是一个 npm 模块，用于在后端应用中建立路由，创建中间件功能。
*   [express-graphql](https://github.com/graphql/express-graphql) :它帮助我们在 Express routes 中创建一个 GraphQL HTTP 服务器，作为一个中间件功能。
*   [graph QL-type-long](https://github.com/chadlieberman/graphql-type-long):graph QL 没有预定义的长整型。这个国家预防机制模块有助于弥合这一差距。
*   [dotenv](https://github.com/motdotla/dotenv) :用于存储 MongoDB URI。
*   [mongose](https://github.com/Automattic/mongoosehttps://github.com/Automattic/mongoose):用于连接 MongoDB 数据库，执行 CRUD 功能。

> 如何使用 graphql-express-app CLI？

> 确保安装了最新的 Node.js 版本和 MongoDB for database。

## 1.使用以下命令在您的计算机上全局安装 graphql-express-app CLI:

```
$ npm i -g graphql-express-app
```

## 2.根据您想要的名称创建一个文件夹并打开它:

```
$ mkdir folder_name && cd folder_name
```

## 3.在 shell 中运行以下命令:

```
[yolo@localhost folder_name]$ graphql-express-app
```

## 4.根据问题回答选项。

## 5.确保在你的机器上安装了一个 MongoDB 或者有一个在线的 MongoDB 实例，并修改*。env* 项目中相应的变量。

瞧啊。您已经成功创建了一个 GraphQL-Express 应用程序！

> 类型脚本支持

我目前正在为 CLI 开发[类型脚本](https://www.typescriptlang.org/)支持。很快我们将发布支持 TypeScript 的版本。

希望你喜欢这个项目，并利用它在你未来的项目❤

编码快乐！

# 项目的存储库:

[](https://github.com/QuBytes-OSS/graphql-express-app) [## QuBytes-OSS/graph QL-express-app

### 生成 GraphQL-Express 应用程序的命令行工具。它使用 npm 进行安装，可以安装在 macOs 上…

github.com](https://github.com/QuBytes-OSS/graphql-express-app) 

## 如果您将在您的项目中使用该工具，请给它一颗星😊

# 与我联系

GitHub:[https://github.com/yashgkar](https://github.com/yashgkar)

领英:[https://www.linkedin.com/in/yashgarudkar/](https://www.linkedin.com/in/yashgarudkar/)

推特:[https://twitter.com/codeitachi](https://twitter.com/codeitachi)