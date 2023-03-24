# 使用 TypeScript 使用 Node.js、Express、MongoDB 设置 RESTful API

> 原文：<https://levelup.gitconnected.com/setup-restful-api-with-node-js-express-mongodb-using-typescript-261959ef0998>

![](img/375add0fa33c1c03f0e375e244c8701d.png)

第 1 部分:[设置 REST API](https://medium.com/@jpbinith/setup-restful-api-with-node-js-express-mongodb-using-typescript-261959ef0998)

第二部分:[REST API 中的项目结构和建设路线](https://medium.com/@jpbinith/project-structure-and-building-routes-of-restful-api-with-node-js-f3a8b53d94e7)

第 3 部分:[连接到 MongoDB 和 CRUD 操作](https://medium.com/@jpbinith/connect-express-rest-api-with-mongodb-and-crud-operations-using-typescript-58d9afcc06d)

在本文中，我将帮助您使用上述技术建立您的第一个 REST API，并在您的机器上运行它。希望你会喜欢它。😃

在开始之前，让我们对将要使用的技术和工具有一些了解。

## 什么是 API，它是如何工作的？

API(应用程序编程接口)有点像信使。它充当两个或更多需要相互对话的应用程序之间的中介。换句话说，它将你的请求传递给提供者(你向他们请求什么),并在那里对你做出响应(你请求的东西)。因此，这是一种允许两个应用程序使用一组**规则进行交互的机制。**

想象你在一家餐馆里享用一些食物。你的桌子上有一份菜单，上面有很多选择，在厨房里，有一位厨师准备好为你做饭。但是厨师不能来帮你点菜，因为他/她正在厨房忙着做饭。所以服务员会从你那里接订单，送到厨房，告诉厨师你点的是什么(要求)。当食物(回应)准备好了，服务员会把它送到你面前。在这里，服务员充当 API。这个例子将让你对什么是 API 以及它如何工作有一个基本的概念。

## **原料药的种类？**

*   肥皂
*   XML-RPC
*   JSON-RPC
*   休息

## 什么是 Rest API，我们为什么要使用它？

REST(**RE**presentational**S**state**T**transfer)API 是一个应用程序接口，它使用 HTTP 请求来获取、上传、发布和删除数据。它不像其他 web 服务那样是一个协议，相反，它是一组架构原则。REST 建议为客户机请求的数据创建一个对象，并将对象的状态发送给用户。REST APIs 比其他类型的 API 更快、更灵活，开发人员喜欢 REST，因为学习曲线不太陡。

## 节点. js

Node.js 是一个开源的跨平台运行时环境，用于开发服务器端和网络应用程序。Node.js 应用程序是用 JavaScript 编写的，可以在 OS X、Microsoft Windows 和 Linux 上的 Node.js 运行时中运行。

## 表达

Express 是 Node.js 的一个快速、自信、基本和适度的 web 框架。您可以假设 express 是构建在 Node.js 之上的一个层，帮助管理服务器和路由。它提供了一组健壮的特性来开发 web 和移动应用程序。

## MongoDB

MongoDB 是一个面向文档的 NoSQL 数据库，用于大容量数据存储。MongoDB 使用带有模式的类似 JSON 的文档。

## 以打字打的文件

TypeScript 是一种由微软开发和维护的开源编程语言。它是 JavaScript 语言的超集，为该语言添加了可选的静态类型。TypeScript 的目标是帮助通过类型系统尽早发现错误，并使 JavaScript 开发更加高效。

## 刻薄？

在 MEAN stack 中，M 代表 MongoDB，E 代表 Express，A 代表 AngularJS，最终 N 代表 Node.js. Angular 是一个前端开发框架，因为我们专注于后端，所以我不打算在这里谈论它。MEAN stack 比其他竞争对手都出名，因为它覆盖了使用 javascript 从前端到后端开发的整个 web 开发周期，有助于保持 web 应用程序开发的有序性，抵制不必要的繁重工作，并且因为它们是开源的，所以具有良好的社区支持。

在我们开始之前，确保您的机器上已经安装了 [NodeJS、](https://nodejs.org/en/)[MongoDB](https://docs.mongodb.com/manual/administration/install-community/)[。如果您在本地安装了 MongoDB，您应该为 GUI 界面安装](https://nodejs.org/en/) [Mongo Compass](https://docs.mongodb.com/compass/master/install/) 或 [Robo 3T](https://robomongo.org/) 。我建议你使用 [VS 代码](https://code.visualstudio.com/)进行开发。

如果您在开始我们的项目之前全局安装 TypeScript 会更好。然后在终端中键入下面一行，并按 Enter 键。

```
npm install -g typescript ts-node
```

为了测试 HTTP 请求，我们可以使用 [Postman](https://www.getpostman.com/apps) 发送示例请求并查看响应。

现在我们可以深入到编码部分了💪让我们先设置我们的项目

> **第一步:启动一个节点项目**

打开要在其中创建项目的文件夹。然后单击地址栏中的文件浏览类型 cmd 并输入。然后在你的终端上输入“代码”它会把你引向 vs 代码。在 vs 代码中，你可以通过点击终端标签并选择新终端或者按 ctrl + shift +` '来打开终端。您可以现在回答问题，也可以在之后的任何时间进行编辑。完成初始化后，你可以看到' **package.json** '已经创建好了。

```
mkdir my-first-project
cd .\my-first-project\
npm init
```

> **第二步:安装所有的依赖项**

完成后，您可以看到' **package-lock.json** '文件已创建，且' **node_modules** '已导入。

```
npm install --save @types/express express body-parser mongoose   nodemon
```

> **第三步:配置 TypeScript 配置文件**

我们使用 typescript 进行开发，但是因为我们在 Node.js 上运行它，所以我们需要将其转换为 javascript。所以在这里我们把我们所有的 typescript 文件放在一个名为 ***lib*** 的文件夹里面，而 java script 文件放在一个名为 ***dist*** 的文件夹里面。让我们在下一篇文章中讨论这种文件格式。现在在你的项目中创建一个文件，命名为' **tsconfig.json** '，复制并粘贴如下内容。

每当我们运行 typescript 编译器' **tsc** '时，' **lib** 文件夹中的所有 typescript 文件都会转换成 javascript 并保存在' **dist** 文件夹中

```
tsc
```

> **第四步:设置脚本运行 API**

在' **package.json** '文件中更新主文件如下。

```
"main": "./dist/server.js"
```

在脚本下的' **package.json** '文件中添加以下脚本。

因此，对于开发，我们可以通过在您的终端上运行以下命令来运行测试服务器

```
npm run test / npm run dev
```

为了生产

```
npm run prod
```

在这里，我们使用 typescript 编译器(tsc)将 typescript 编译成 javascript，并转到 server.ts 文件所在的方向，使用 nodemon 运行它。

要终止该过程，请按

```
ctrl + c
```

> **步骤 5:在应用程序上配置基本配置**

现在让我们在' **lib** '文件夹中创建另一个名为' **config** 的文件夹。在下一篇文章中，我们将更多地讨论文件格式。在这个“ **config** 文件夹中创建一个名为“ **app.ts** 的文件，复制并粘贴以下内容。在处理程序之前解析中间件中的传入请求体，在`req.body`属性下可用。目前，我们不打算在这方面整合 mongo。

然后创建 server.ts 文件

从现在开始，您可以通过运行' **npm run dev** '来测试项目，下面的内容将会打印在您的终端上。

> 快速服务器侦听端口 3000

完整代码请访问我的 [Github 库](https://github.com/jpbinith/test-project)。如果你感兴趣，你可以从下面的文章中学习关于 Node Express REST APIs 的移动。

1.  [设置 REST API](https://medium.com/@jpbinith/setup-restful-api-with-node-js-express-mongodb-using-typescript-261959ef0998)
2.  [REST API](https://medium.com/@jpbinith/project-structure-and-building-routes-of-restful-api-with-node-js-f3a8b53d94e7)项目结构和建设路线
3.  [连接到 MongoDB 和 CRUD 操作](https://medium.com/@jpbinith/connect-express-rest-api-with-mongodb-and-crud-operations-using-typescript-58d9afcc06d)