# 使用断点在本地调试 Node.js Lambda 函数

> 原文：<https://levelup.gitconnected.com/debugging-nodejs-lambda-functions-locally-with-breakpoints-dfb1e2e3c77d>

![](img/9efe589d2360172b8e2237248fa13064.png)

我一直是任何无服务器产品的超级粉丝。以这种方式构建应用程序有很多好处。首先也是最重要的，你不必担心维护服务器和扩展它。其次，它非常便宜，而且在大多数情况下是免费的。第三，我说过它几乎是免费的吗？

但是，无服务器也有一些缺点。在我看来，最大的优势之一是能够在 Node.js 中使用断点在本地调试函数。网络堆栈。任何用过的人。NET 知道工具是不可思议的，即使是无服务器的功能。

使用 Node，您可以获得大部分相同的调试好处，但是它需要更多的设置。在本文中，我将介绍如何使用断点在本地调试 Node.js Lambda 函数**。**如果你一直在使用控制台日志语句，你会希望很久以前就已经解决了这个问题😬。

# 视频教程

视频教程

# 无服务器框架

为了能够本地调试，我们将使用[无服务器框架](https://serverless.com/)。这是一个用 Node 编写的免费开源框架。它用于使创建 Lambda 函数的过程比使用 AWS 的 CLI 简单得多。

## 装置

要安装它，请打开您的终端并运行以下命令:

```
npm install -g serverless
```

## 链接到 AWS 帐户

接下来，如果你还没有账户，你需要创建一个 [AWS](https://aws.amazon.com/) 账户。然后登录 AWS，转到 [IAM](https://aws.amazon.com/iam/) 并创建一个具有编程管理员访问权限的帐户。创建帐户后，您将获得一个访问密钥 ID 和秘密访问密钥。使用它们在您的终端中运行以下命令:

```
serverless config credentials --provider aws -key AKIAIOSFODNN7EXAMPLE --secret wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

有关这方面的更多详细说明，请查看[无服务器文档](https://serverless.com/framework/docs/providers/aws/guide/credentials/)。

## 离线安装无服务器

配置好您的帐户后，您需要全局安装[无服务器离线](https://github.com/dherault/serverless-offline) npm 软件包。为此，请在终端中运行以下命令:

```
npm i -g serverless-offline
```

## 创建项目

现在我们已经拥有了本地调试所需的一切，是时候设置我们的无服务器框架项目了。打开您的终端，cd 到您想要的目录，并运行以下命令:

```
serverless create -t aws-nodejs -p your-project-name
```

接下来，打开终端，cd 进入项目的根目录，并运行以下命令:

```
npm init -ynpm install serverless-offline --save-dev
```

对于本教程，你需要使用[和代码](https://code.visualstudio.com/)。在 VS 代码中打开项目。在项目的根目录中，您会看到两个主文件和一个. gitignore 文件。我们将要编写函数代码的文件是 handler.js，而 serverless.yml 文件是存储所有 API 配置的地方。

## 无服务器. yml

在 serverless.yml 文件中，我们将添加一个名为 plugins 的新属性，并添加 serverless-offline 插件。然后，我们将取消对 events 属性的注释，并设置一个路径和方法。完成后，文件应该如下所示:

## handler.js

如前所述，这是您编写 API 代码的地方。出于本教程的目的，我们不会编辑这个文件。在以后的教程中，我将介绍如何在一个无服务器项目中构建您的代码，使其更易于维护。

# 设置 VS 代码调试器

在项目的根目录下，打开 package.json 文件。在“脚本”属性下，粘贴以下内容:

```
"start": "./node_modules/.bin/serverless offline -s dev",
"debug": "SET SLS_DEBUG=* && node --inspect C:\\\\Users\\YOUR_USER_NAME\\AppData\\Roaming\\npm\\node_modules\\serverless\\bin\\serverless offline -s dev
```

scripts 标签下的 debug 属性应该指向本地机器上无服务器离线插件的位置。如果你得到一个错误，你必须在你的机器上找到它的位置。您的文件应该如下所示:

package.json

在 VS 代码中，单击左侧菜单中的调试图标。

![](img/874697a4c99c2ea57e9f88da5dfb87e2.png)

VS 代码调试图标

然后点击“创建一个 launch.json 文件”在下拉列表中，单击 Node.js。这将创建一个 launch.json 文件。在该文件中，粘贴以下内容:

启动. json

如果您的调试器在连接时遇到问题，请检查端口号。您可以通过点击 debug play 按钮并打开调试控制台来找到端口。

![](img/217679cbe47d72f85304b55d5e51dc31.png)

查找调试端口

# 排除故障

现在点击 VS 代码中的绿色播放图标。

![](img/c6d55a8eb10a08a45da41937f2a2acdc.png)

VS 代码调试图标

在调试控制台中，您应该会看到类似这样的内容:

![](img/0b5e40bf602b683ae286b401c968d92f.png)

调试控制台

这告诉我们，我们的函数已经准备好在端口 3000 上被调用，端点为[。](http://localhost:3000/dev/hello.)现在进入 handler.js 文件，在 return 上设置一个断点。为了触发这个断点，我将使用[邮递员](https://www.postman.com/)。为 get 请求将 URL 粘贴到 postman 中，然后点击 Send。

您的断点将被触发，您将能够将鼠标悬停在变量上并查看它们的所有其他属性和方法。

![](img/14e1fb6ae2b3e40c4eec788fe918ac84.png)

在 VS 代码中调试

# 结论

现在你有了一个节点 Lambda 函数，可以在本地单步执行。如果您对本教程有任何问题、评论或疑虑，请在下面留言。下次见，编码快乐！

bit bucket Repo:[https://bit bucket . org/the diligent dev/AWS-debug-locally/src/master/](https://bitbucket.org/TheDiligentDev/aws-debug-locally/src/master/)