# 如何启动每个全栈项目

> 原文：<https://levelup.gitconnected.com/how-to-start-every-full-stack-project-7c92e07f8693>

## React 前端+ NodeJs + Express + Heroku 部署

在本文中，我将介绍我的全栈项目的初始设置。我的主要任务是:建立一个 Git 存储库，建立一个简单的部署过程，创建一个可以发送请求的前端，建立一个可以响应请求的后端，以及分离开发和生产环境变量。在这个模板中，我编写的代码独立于我想要实现的任何功能，因此它几乎可以用于任何全栈项目。

![](img/4fb1f3861bf47825fb6751c30d3e1fb5.png)

当然，根据您选择使用的技术，这将略有不同，但从概念上讲，这些是任何应用程序最重要的开始步骤。最后，我提供了一些可以用于大型项目的改进。

# 设置 Github 存储库

首先要做的是建立 Github 库，这样我们就可以跟踪所有的代码变更，并在需要时轻松回滚。第一步是导航到 Github.com 并创建一个新的存储库。之后，使用您的终端导航到您想要在其中建立项目的目录，并运行

```
git init
git remote add origin [https://github.com/YourAccount/YourProject.git](https://github.com/YourAccount/YourProject.git)
```

对于每个主要的更改，您将希望通过运行以下命令将所有文件添加到提交中:

```
git add .
```

然后将它们提交给存储库，并通过运行以下命令编写合适的消息:

```
git commit -m “add message here”
```

最后，通过运行以下命令将代码推送到远程存储库:

```
git push
```

注意:第一次运行 git push 时，它可能会要求您设置上游，只需复制并粘贴它要求您运行的命令，它应该会工作

# 设置后端

## 正在初始化 NPM

第一步是初始化 NPM，并通过运行以下命令在目录中生成 package.json:

```
npm init
```

您可以选择在任何提示下填写信息，但也可以在每个提示下按 return / enter 键跳过。

除了启动脚本和应用程序元数据之外，package.json 还包含项目中的所有依赖项。

进入 package.json，在 scripts 下添加下面一行，这将允许我们通过运行 npm start 来运行我们的服务器:

```
"start": "node app.js"
```

然后我们需要安装 express 和 cors，我们可以通过运行:

```
npm install express cors
```

除了自动创建 packge-lock.json 之外，您还应该在 package.json 的依赖项列表中看到 express 和 CORS。CORS 允许我们绕过 Access-Control-Allow-Origin 头来放松应用于 API 的安全性，Access-Control-Allow-Origin 头指定了哪些头可以访问 API。请注意，除非您的最终项目想要提供面向公众的 API，否则 cors 不应该在您的生产服务器上启用。但是本文的范围是全栈应用程序的开始，所以我们不会深入这个主题，我们现在将使用 cors。

## 应用程序代码

然后，使用以下代码创建 app.js 文件:

```
const express = require("express");const path = require("path");const cors = require("cors");const app = express();app.use(express.static(path.resolve(__dirname, "./client/build")));app.use(cors());app.get("/api", async (req, res) => {res.send({ data: "received" });});const PORT = process.env.PORT || 5000;app.listen(PORT, () => {console.log(`Listening on port ${PORT}`);});
```

“require”语句实际上导入了下载的节点模块。

“use”语句使用指定的模块作为中间件。在本例中，我们使用 cors 中间件来允许 localhost:3000(我们的 React 应用程序将在此运行)和 localhost:5000(我们的服务器在此运行)之间的请求。

```
app.use(express.static(path.resolve(__dirname, "./client/build")));
```

稍后当我们在 Heroku 上部署应用程序时使用，它实际上通知 Heroku 使用 client/build 文件夹来服务 React 应用程序。

```
app.get("/api", async (req, res) => {res.send({ data: "received" });});
```

上面的代码块指定对/api 路由的 get 请求应该返回一个带有数据的对象:“received”。我们仅将此作为对后端请求被正确路由和响应的指示。

在下面的代码块中，我们分配了想要监听的端口。对于我们的开发环境，我们希望它在 localhost:5000 上侦听，所以我们为它分配了端口 5000。然而，当我们将应用程序部署到 Heroku 时，我们需要找到分配给它的端口 Heroku 来监听，这是从 process.env.PORT 获得的。

```
const PORT = process.env.PORT || 5000;app.listen(PORT, () => {console.log(`Listening on port ${PORT}`);});
```

# 设置前端

## 创建 React 应用程序

我们将使用 Create React App 来构建我们的 React 前端。这是一种快速启动项目的方法，具有许多现成的功能。如果你想对 webpack 有更多的控制，你可以随时推出 CRA 获得更多的手动控制。

要设置初始应用程序，请运行以下命令:

```
npx create-react-app client
```

然后，在客户端文件夹下，运行以下命令来安装 axios，我们将使用它向后端发出请求:

```
npm install axios
```

但是这会创建许多不必要的文件，所以只需删除 client/src 下的以下文件:

*   App.css
*   应用测试网站
*   index.css
*   setupTests.js
*   徽标. svg
*   报告网站重要信息

client/src 下唯一应该保留的文件是 index.js 和 App.js。

## 添加环境变量

我们将要编写的代码主要是为了方便向后端发出请求，并确保我们得到预期的响应。

我们要做的第一件事是添加一个环境变量，告诉我们的应用程序向哪里发送请求。请记住，在开发期间，我们希望向 localhost:5000 发出请求(因为 react 应用程序运行在 localhost:3000 上)，但是在生产环境中，我们希望向同一个域发出请求。有几种方法可以解决这个问题，但这是目前最简单的方法，可以让我们快速开始测试后端和前端功能。

在客户端目录下创建一个. env.development 文件，然后在其中添加一行代码:

```
REACT_APP_BACKEND_ROUTE=http://localhost:5000
```

这定义了一个名为 REACT_APP_BACKEND_ROUTE 的环境变量，并在本地分配它的值 [http://localhost:5000](http://localhost:5000) ，可以从前端的 process . env . REACT _ APP _ back end _ ROUTE 访问它。稍后当我们设置 Heroku 部署时，这个环境变量将被 Heroku 上调用的 web 应用程序所替换，这是我们的开发环境变量。使用 Create React App 时，环境变量名称必须以 REACT_APP_…开头，才能被选取。

确保。gitignore 有. env.development，但其中没有/build。这是为了当我们最终将应用程序部署到 heroku 时，除了使用生产环境变量而不是开发环境变量之外，我们还希望它能够访问构建文件夹。

## 编写前端代码

starter 项目的代码非常简单，本质上就是使用环境变量向后端发出请求，并在按下按钮后显示响应。index.js 和 App.js 文件应该是这样的:

## 创建生产版本

对于前端的最后一步，确保您在客户端目录中并运行

```
npm run build
```

这将为您的 React 应用程序创建一个生产版本。

# Heroku 部署

## 创建 Heroku 应用程序

如果你没有账户，请确保在这里注册一个:【https://signup.heroku.com/login

一旦你有一个帐户，登录并创建一个新的 Heroku 应用程序，点击“新建”按钮->“创建新的应用程序”。这应该带你到一个屏幕，在那里你命名应用程序，然后创建它，我通常选择默认区域。

创建账户后，确保你已经下载了 Heroku CLI，如果没有，按照说明设置:【https://devcenter.heroku.com/articles/heroku-cli

完成之后，在您的终端上运行这个命令来登录 Heroku

```
heroku login
```

登录后，在您的终端中导航到您的项目，并运行以下命令将这个 git repo 附加到 heroku:

```
heroku git:remote -a your-app-name
```

然后添加、提交并推送您的文件到 Heroku 进行部署:

```
git add .
git commit -m "Deploy commit message"
git push heroku master
```

这可能需要一段时间，但当它完成时，它会部署您的应用程序并为其分配一个 URL。复制那个网址。

然后转到您的客户机目录，创建一个名为. env.production 的新文件，并添加:

```
REACT_APP_BACKEND_ROUTE=your-app-URL
```

现在，最后，在您的客户机目录中运行

```
npm run build
```

这将使用您的. env.production 环境变量生成一个生产版本，然后再次运行 git add、commit 和 push heroku master 命令。部署后，您应该能够点击测试按钮，并收到回应。

# 包装好一切

本质上，我们在这个项目中完成的是创建一个能够在生产和开发环境中与后端通信的前端。从这里，它应该很容易添加功能，路线等…因为大部分的开始工作是为一个小项目做的。

你可以做些什么来让这款入门应用更上一层楼:

*   使用 docker 和 nginx 进行部署和前端到后端路由
*   创建自己的 webpack，而不是使用 create react 应用程序
*   使用您选择的后端技术

如果这是有用的，我会感谢一个关注！谢谢你