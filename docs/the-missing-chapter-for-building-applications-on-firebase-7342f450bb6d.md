# 在 Firebase 上构建应用程序的缺失章节:创建不同的 Firebase 环境

> 原文：<https://levelup.gitconnected.com/the-missing-chapter-for-building-applications-on-firebase-7342f450bb6d>

## 如何为测试、试运行和生产创建不同的环境

你终于在 Udemy 上买了那门课，去学[的火焰基础](http://firebase.google.com)。毕竟价格降到了 11.99 美元！好吧，也许是免费的 YouTube 教程。不管是什么，我假设你现在已经理解了如何使用 Firebase 服务，比如托管、认证、Firestore、功能和存储。你甚至建立了这个 ***惊人的*** 待办事项应用程序，只运行在 Firebase 服务上！

![](img/749c511264114b5d1764d834cd88004a.png)

照片由[奥斯汀·施密德](https://unsplash.com/@schmidy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在一个新的问题浮现在脑海里。我如何在 Firebase 上实现我的 SDLC 过程？

> 我需要有单独的测试、试运行和生产环境。

当看到我的数据在 Firestore 中，我的文件在存储中，最初的兴奋逐渐消失后，我有了同样的问题。对我来说，解决方案并不简单。我想和你们分享我是如何最终设计出一套让我真正满意的系统的。

本教程侧重于您的全栈应用的配置，以支持开发、测试、试运行和生产的概念。本教程将**而非**涵盖应用代码或如何使用 Firebase SDKs。

# 先决条件

由于本教程的主题，我将做出以下假设:

*   你是一名 Javascript 开发人员
*   你熟悉 Node.js、NPM 和 Yarn
*   你至少熟悉 Firebase
*   你对 git 和 GitHub 很熟悉

# 设置

如果您对 Firebase 感到满意，这一部分可能看起来微不足道，但我想从头开始。一个全新的项目。姑且称之为“猛男”。是的，那是我第一个想到的事情。奇怪。

让我们从目录结构开始。因为我们正在构建一个全栈应用程序，所以我创建了下面的结构。如果我没有指定一个工作路径，假设你正坐在项目目录中，“beefcake”。

```
beefcake
  L client
  L server
```

确保您已经安装了 Firebase 工具。

```
> npm install -g firebase-tools
```

如果您还没有在您的设备上使用 Firebase 工具，您将需要执行一次性登录。

```
> firebase login
```

## **客户端配置**

```
cd client
yarn init -y
firebase init
```

*   选择“主机”和“模拟器”
*   创建一个“沙盒”项目(例如，beefcake-sandbox)
*   保留默认公共目录
*   选择“是”重写所有 URL
*   选择“N”以自动构建
*   选择“托管”模拟器
*   保留默认端口
*   选择“N”启用仿真器用户界面
*   选择“N”下载仿真器

## **服务器配置**

```
cd server
firebase init # See answers below and then continue herecd functions
yarn add firebase-admin# If you get a Node engine error...
yarn config set ignore-engines true
yarn add firebase-admin
```

*   选择“Firestore”、“函数”和“模拟器”
*   选择现有项目(例如，beefcake-sandbox)
*   保留默认文件名
*   选择“JavaScript”
*   选择“N”进行 ESLint
*   选择“N”安装依赖项
*   选择“验证”、“功能”、“Firestore”和“发布/订阅”模拟器
*   将 Firestore 端口设置为“8070 ”,但将其余端口保留为默认值
*   选择“Y”以启用模拟器 UI
*   将仿真器 UI 端口设置为“4000”
*   选择“N”下载仿真器
*   返回到上面的代码块以完成命令…

现在我们的项目应该有以下目录和文件。

```
project
  L client
    L .firebaserc
    L .gitignore
    L firebase.json
    L package.json
    L public
  L server
    L .firebaserc
    L .gitignore
    L firebase.json
    L firestore.indexes.json
    L firestore.rules
    L functions
      L .gitignore
      L index.js
      L node_modules
      L package.json
      L yarn.lock
```

# **验证设置**

首先，让我们通过运行主机模拟器来验证客户端配置。

```
cd client
firebase emulators:start
```

在浏览器中打开 [http://localhost:5000](http://localhost:5000) 。您应该会看到一个 Firebase 页面和文本，表明 Firebase SDK 正在使用中。你现在可以用`Ctrl-c`停止模拟器。

在启动服务器模拟器之前，我们需要取消一些默认代码的注释。在你的编辑器中，打开`beefcake/server/functions/index.js`。取消对`helloWorld`函数的注释。

```
const functions = require("firebase-functions");

// // Create and Deploy Your First Cloud Functions
// // https://firebase.google.com/docs/functions/write-firebase-functions
//
exports.helloWorld = functions.https.onRequest((request, response) => {
  functions.logger.info("Hello logs!", {structuredData: true});
  response.send("Hello from Firebase!");
});
```

现在启动模拟器。

```
cd server/functions
firebase emulators:start
```

在浏览器中打开 [http://localhost:4000](http://localhost:4000) 。您应该会看到 Firebase Emulator Suite 页面，其中显示了我们的服务正在运行。在浏览器中打开[http://localhost:5001/beef cake-sandbox/us-central 1/hello world](http://localhost:5001/beefcake-sandbox/us-central1/helloWorld)(您的项目和区域可能不同，但其余部分应该相同)。你应该看到“你好，来自 Firebase！”。这表明函数所服务的端点正在工作。您现在可以关闭这些模拟器了。

# 发展

随着我们的客户端和服务器模拟器的运行，我们拥有了开发全栈应用所需的一切，而没有使用实时云服务的成本或延迟。

我之前说过，这个教程的重点不是如何开发你的 app。当使用 Firebase 时，我们关注的是贯穿我们开发生命周期的方法。所以让我们快进一点。

在这一点上，让我们假设我们已经“完成”了开发，并且我们已经准备好将我们所拥有的部署到 Firebase，以便其他人可以访问该应用程序。

# 沙盒部署

请记住，我们还没有使用生产环境。现在我们只有自己的沙盒。我们只需要将我们的客户端和服务器项目部署到那个环境中。

让我们从客户项目开始。

```
cd client
firebase deploy
```

搞定了。真的！部署完成后，您会在屏幕上找到一个“托管 URL”。如果你打开你的浏览器到那个网址，你应该会看到我们之前看到的相同的页面，只是这次，它是从 Firebase 提供给我们的。

服务器部署有一个“问题”。你必须将你的 Firebase 项目从“Spark”升级到“Blaze”。“星火”计划是免费的，但有限制。“火焰”计划提供免费使用限制，然后向你收取超额费用。仔细查看价格，确保你了解风险。我个人从来没有收到过超过 2 美元/月的账单，通常是低于 1 美元/月。

一升级就该部署了！

```
cd server
firebase deploy
```

此部署将比客户端花费更长的时间。完成后，您将看到“helloWorld”端点的 URL。现在你将使用 [Firebase 控制台](http://console.firebase.google.com)，而不是模拟器套件。只要“hello world”URL 有效，我们就完成了沙盒部署。

在这一点上，你应该给涉众客户的网址，让他们体验你的荣耀。

> 当与团队合作时，所有这些仍然适用。每个开发人员都有一份代码副本(例如 git)。如果有人第一次克隆存储库，那么在模拟器或部署工作之前，他们需要在客户机和服务器目录中运行`firebase init`。

# 测试

我不打算讲述*你如何*为你的应用程序做测试。我只想简单地谈谈 Firebase 如何在测试中发挥作用的几个关键点。

模拟器套件实际上非常适合测试。首先，不像在 Firebase 中运行应用程序，您可以测试的内容不受限制。模拟器的另一个好处是，当你停止并再次启动它们时，一切都是干净的。就像，空的。Firestore 中没有清除数据的工具。在开发或测试过程中处理实时服务中的数据并不有趣。不要用模拟器！

但是…如果您真的*而不是*想要丢失您的数据呢？如果需要预加载数据进行测试呢？再一次，模拟器来拯救我们了！当模拟器运行时，您可以导出数据。

```
cd server
firebase emulators:export ./export-dir
```

这将执行转储，然后可以导入该转储。您还可以指示模拟器在关闭之前总是导出。

```
cd server
firebase emulators:start --import=./export-dir --export-on-exit
```

您仍然需要使用在 Firebase 中运行的应用程序执行一些系统测试，但是对于单元和 e2e 测试，模拟器套件非常方便。

# 生产

现在我们终于得到了本教程的要点。我们已经处理了开发、测试，甚至为利益相关者提供了一个沙箱。

> 我们如何将它投入生产，并保留我们已有的东西用于未来的开发、测试等。？

好消息是，我们的代码和它所在的位置不需要改变。我们不需要创建新的项目代码文件夹。我们不需要运行并行 git 分支或类似的东西。

长话短说，我们只需要告诉我们的客户机和服务器关于新的 Firebase 项目的配置，然后就可以按需设置当前的项目。

```
cd client# Display a list of known projects
> firebase use
* default (beefcake-sandbox)> firebase use --add
```

首先，我们使用`firebase use`来查看当前配置了什么。我们可以看到我们已经配置了一个项目(“beefcake-sandbox”)，别名为“default”。

接下来我们跑了`firebase use --add`。这告诉 Firebase 我们想要添加另一个项目。因此，创建生产项目(例如“beefcake-prod”)并为其分配别名“prod”。

现在我们只需要在服务器项目中做同样的事情。

```
cd server
firebase use --add
```

确保选择我们刚刚创建的现有生产项目。

从现在开始，如果我们想将代码部署到产品中，我们需要首先运行`firebase use prod`。这为 Firebase 工具设置了上下文。

## **环境变量**

在您的应用程序代码中，您将拥有特定于环境的变量，当您在沙盒和生产之间切换时，这些变量也需要更新。有许多方法可以实现这一点。我并不是推荐一种方法而不是另一种方法，但是，因为它适用于这个主题，所以我将向您展示一种方法。

大多数时候我使用`dotenv`库来管理环境变量。这允许您将敏感令牌放在单独的文件中，然后用您的`.gitignore`文件将它们排除在任何公共回购中。因此，您通常会在客户端和服务器项目中创建一个`.env`文件。我还为每个环境创建了一个。稍后，我们将根据需要编写脚本复制它们。

```
beefcake
  L client
    .env          # used by dotenv
    .env.local    # copy to .env when running emulators
    .env.sandbox  # copy to .env on sandbox deploy
    .env.prod.    # copy to .env on prod deploy
  L server
    L functions
      .env
      .env.local
      .env.sandbox
      .env.prod
```

# 包脚本

这个配置的最后一部分是用我们的`package.json`文件中的脚本来简化事情。我们希望确保我们正确地设置环境，同时让事情变得真正简单易用。

到目前为止，除了创建`client`和`server`文件夹，我们还没有在项目根文件夹中做任何事情。我们将从这里运行我们的脚本，这将依次在客户端和服务器区域运行适当的脚本。

我们需要初始化项目目录以使用脚本，然后将一个名为`npm-run-all`的有用库添加到我们拥有`package.json`文件的所有三个位置。

```
yarn init -y
yarn add -D npm-run-allcd client
yarn add -D npm-run-allcd ../server/functions
yarn add -D npm-run-all
```

## 项目目录脚本

下面是一组保存在项目目录的`package.json`文件中的示例脚本。我排除了文件中不相关的部分，所以不要把这些内容当成字面意思。

```
{
  "name": "project",
  "scripts": {
    "serve:local": "npm-run-all --parallel client:local server:local",
    "deploy:staging": "npm-run-all client:deploy:staging server:deploy:staging",
    "deploy:prod": "npm-run-all client:deploy:prod server:deploy:prod",
    "client:local": "cd client && yarn serve:local",
    "client:deploy:staging": "cd client && yarn deploy:staging",
    "client:deploy:prod": "ce client && yarn deploy:prod",
    "server:local": "cd server/functions && yarn serve:local",
    "server:deploy:staging": "cd server/functions && yarn deploy:staging",
    "server:deploy:prod": "cd server/functions && yarn deploy:prod"
  }
```

## 客户端目录脚本

路径是`client/package.json`。

```
{
  "name": "client",
  "scripts": {
    "serve:local": "npm-run-all use:staging copy-env:local start:local",
    "deploy:staging": "npm-run-all use:staging copy-env:staging quasar:build:staging firebase:deploy:staging",
    "deploy:prod": "npm-run-all use:prod copy-env:prod quasar:build:prod firebase:deploy:prod",
    "use:staging": "firebase use default",
    "use:prod": "firebase use prod",
    "copy-env:local": "cp .env.local .env",
    "copy-env:staging": "cp .env.staging .env",
    "copy-env:prod": "cp .env.prod .env",
    "start:local": "firebase emulators:start",
    "firebase:deploy:staging": "firebase deploy -P default",
    "firebase:deploy:prod": "firebase deploy -P prod"
  }
```

## 服务器目录脚本

路径是`server/functions/package.json`。

```
{
  "name": "server",
  "scripts": {
    "serve:local": "npm-run-all use:staging copy-env:local start:local",
    "deploy:staging": "npm-run-all use:staging copy-env:staging firebase:deploy:staging",
    "deploy:prod": "npm-run-all use:prod copy-env:prod firebase:deploy:prod",
    "use:staging": "firebase use default",
    "use:prod": "firebase use prod",
    "copy-env:local": "cp .env.local .env",
    "copy-env:staging": "cp .env.staging .env",
    "copy-env:prod": "cp .env.prod .env",
    "start:local": "firebase emulators:start",
    "firebase:deploy:staging": "firebase deploy -P default",
    "firebase:deploy:prod": "firebase deploy -P prod"
  }
```

## 执行

通常，您将从项目目录开始。此时，您的选择是通过启动所有模拟器在本地运行，将客户端和服务器部署到沙盒环境，或者将客户端和服务器部署到生产环境。当您沿着脚本的路径行进时，您会注意到，为了确保环境的设置正好适合我们想要完成的任务，采取了多种措施。手动完成所有这些工作是自找麻烦。

```
# Run locally with emulators
yarn serve:local# Deploy to sandbox
yarn deploy:sandbox# Deploy to production
yarn deploy:prod
```

# 结论

在我对这个设置感到满意之前，我进行了大量的研究和反复试验。在我的研究中，我发现很多人都在问如何做这样的事情，但我从来没有找到一个对我来说足够完整或足够清晰的答案。

关于如何在你的项目中使用 Firebase，有很多课程和教程，但是我还没有找到一个能够满足多种环境需求的。我想让这个演示在这些说明结束的地方继续。请让我知道我是抓住了它，还是抓住了它！