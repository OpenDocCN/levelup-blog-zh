# Node.js 入门终极指南

> 原文：<https://levelup.gitconnected.com/the-ultimate-guide-to-get-started-with-node-js-4ce54579ceb7>

通过创建一个简单的 web 应用程序来学习 Node.js 的基础知识。

![](img/084c367db6146767b1ac160c9e6e2f0a.png)

Node.js 是当今最热门的服务器端技术之一。这是一个开源、跨平台、后端的 JavaScript 运行时环境。Ryan Dahl(node . js 的创建者)于 2009 年 5 月 27 日首次发布。现在最新的 LTS(长期支持)版本是 14.17.6。

Node.js 已经成为全球范围内的热门技术，尤其是在硅谷。这是你简历中的一项出色技能。如果你是一名软件开发人员，这将为你带来巨大的职业机会。您可以在此基础上开发许多很酷的应用程序，比如社交媒体应用程序、实时视频和消息应用程序、在线多人游戏、实时跟踪应用程序等等。

说到核心，Node.js 运行在谷歌浏览器的 V8 引擎上。这个引擎将 Node.js 的 JavaScript 代码编译成机器代码。因此，允许 Node.js 能够在服务器上运行，并被用作我们应用程序的后端技术。

以下是您未来将学习的内容:

1.  Node.js 简介
2.  使用 Node.js 的热门公司
3.  安装 Node.js 和 Node Package 模块(NPM)
4.  创建节点服务器
5.  处理请求并发送响应
6.  安装自动重启的 Nodemon
7.  下一步是什么？

# Node.js 简介

既然你正在阅读这篇文章，你一定知道 JavaScript。它是一种流行的编程语言，在网络浏览器中用来操纵网站的 DOM(文档对象模型)。它也可以通过几种方式来实现，比如显示确认消息、通知、模式，或者实现任何视觉动画和到元素的转换。所有这些功能都属于客户端或前端。

然而，JavaScript 并没有停止仅在客户端使用。它也可以在服务器上使用，Node.js 通过 V8 引擎使这成为可能。

Node.js 是超高速的。因为它通过使用“非阻塞”方法来服务请求，从而实现了低延迟和高吞吐量。它不会因为等待 I/O 请求而浪费时间和系统资源。它是异步和单线程的。这意味着所有的输入/输出操作都不会阻止任何其他操作。正因为如此，您可以同时发送电子邮件、读取文件、运行数据库查询以及做许多事情。

# 使用 Node.js 的热门公司

与其他编程语言相比，Node.js 是新的。但是有一个好消息。《财富》500 强公司偏爱 Node.js 胜过任何其他框架，原因有很多。他们都得出结论，Node.js 是值得的。

公司名单很大，因此列举了其中几个:

1.  领英([https://www.linkedin.com](https://www.linkedin.com/))
2.  网飞([https://www.netflix.com](https://www.netflix.com/))
3.  https://www.uber.com([优步](https://www.uber.com/)
4.  特雷罗(【https://trello.com】T2
5.  贝宝(【https://www.paypal.com】T4)
6.  美国宇航局([https://www.nasa.gov](https://www.nasa.gov/))
7.  https://www.ebay.com[易贝](https://www.ebay.com/)
8.  中等([https://medium.com](https://medium.com/))
9.  Groupon([https://www.groupon.com](https://www.groupon.com/)
10.  沃尔玛([https://www.walmart.com](https://www.walmart.com/))
11.  Mozilla([https://www.mozilla.org](https://www.mozilla.org/en-US/))
12.  戈达迪([https://www.godaddy.com](https://www.godaddy.com/))
13.  yandex([https://yandex.com](https://yandex.com/)
14.  向上工作([https://www.upwork.com](https://www.upwork.com/))
15.  雅虎([https://yahoo.com](https://yahoo.com/))

我知道，你读到这里一定很兴奋。所以，没有任何延迟，让我们开始安装 Node.js 和 NPM。

# 安装 Node.js 和节点包模块(NPM)

在开始编写实际代码之前，我们首先需要在我们的工作站(计算机)上安装 Node.js 和 NPM。建议下载 LTS(长期支持)版本，因为它比最新版本更稳定。点击以下链接进入下载页面。

注意:NPM 附带了 Node.js 安装程序。因此，没有必要单独安装。

[](https://nodejs.org/en/download/) [## 下载| Node.js

### Node.js 是基于 Chrome 的 V8 JavaScript 引擎构建的 JavaScript 运行时。

nodejs.org](https://nodejs.org/en/download/) 

或者，您可以根据您使用的操作系统点击下面相应的链接。

**视窗**
[https://nodejs.org/dist/v14.17.6/node-v14.17.6-x64.msi](https://nodejs.org/dist/v14.17.6/node-v14.17.6-x64.msi)

**Linux
T35[https://nodejs.org/dist/v14.17.6/node-v14.17.6.tar.gz](https://nodejs.org/dist/v14.17.6/node-v14.17.6.tar.gz)**

**Mac** [https://nodejs.org/dist/v14.17.6/node-v14.17.6.pkg](https://nodejs.org/dist/v14.17.6/node-v14.17.6.pkg)

一旦安装完毕，我们就可以开始使用它了。验证 Node.js 是否已经成功安装并可以使用的最快方法是使用 Mac/Linux 上的默认系统终端或 Windows 上的命令提示符。运行以下命令查看您下载并安装的 Node.js 版本:

```
node -v
```

如果上面的命令成功执行，我们知道我们已经准备好开始使用它了。您还可以通过运行以下命令来验证 NPM 安装:

```
npm -v
```

下一步是什么？让我们开始创建一个节点服务器，并设置一个基本的应用程序。

# 创建节点服务器

让我们在我们的文件系统中创建一个名为**“node-starter-app”**的新文件夹。您可以根据自己的选择更改名称。然后导航到这个文件夹，创建一个名为**“app . js”的新文件。**

这个 **"app.js"** 文件将作为 Node.js 应用程序的根文件。现在，在这个文件中，我们将设置一个服务器。在此之前，让我告诉您 Node.js 附带了几个模块。举几个例子:

## **http**

HTTP 模块可以创建一个监听服务器端口并向客户端返回响应的服务器。

## **https**

HTTPS 模块可以创建服务器，并提供一种通过 HTTP SSL/TLS 协议传输数据的方法，这是一种安全的 HTTP 协议。

## **fs**

FS 模块或文件系统模块允许您使用计算机上的文件系统。常见用途有:

1.  读取文件
2.  创建文件
3.  更新文件
4.  删除文件
5.  重命名文件

## **路径**

Path 模块提供了一种处理目录和文件路径的方法。一些方法是:

1.  basename()
    返回路径的最后一部分。
2.  dirname()
    返回路径的目录。
3.  extname()
    返回路径的文件扩展名。
4.  如果路径是绝对路径，则返回 true，否则返回 false。
5.  format()
    将路径对象格式化为路径字符串。

## **os**

OS 模块提供有关计算机操作系统的信息。一些方法是:

1.  arch()
    返回操作系统的 CPU 架构。
2.  CPU()
    返回包含计算机 CPU 信息的数组。
3.  freemem()
    返回系统空闲内存的数量。
4.  hostname()
    返回操作系统的主机名。
5.  networkInterfaces()
    返回具有网络地址的网络接口。

现在，我们将使用 Node.js 附带的 HTTP 模块中的方法来创建一个服务器。默认情况下，该模块不是全局可用的。我们需要导入它来使用它和它提供的方法。请参考以下代码片段来完整设置您的节点服务器:

如果您已经在文件夹**“node-starter-app”**中创建了上述文件，那么导航到该文件夹并启动您的终端或命令提示符。并执行以下命令:

```
node app.js
```

这能做什么？嗯，它将在您提到的端口上启动节点服务器。在本例中，它是端口 3000。现在，请在您的网络浏览器上访问此 URL:

```
http://localhost:3000
```

现在，您不会在屏幕上看到任何输出，因为我们刚刚使用了 console.log()。因此，输出仅在终端或命令提示符下可见。现在，通过退出该终端来停止服务器。在下一节中，您将知道如何处理这些请求并发回响应以打印在屏幕上。

# 处理请求并发送响应

让我们看看在上面的例子中 console.log()语句中使用的 **"request"** 对象。当我们访问 URL:[http://localhost:3000](http://localhost:3000)时，这个**“请求”**对象保存所有传入请求的数据。

在 **"request"** 对象中，我们有一个名为 **"headers"** 的键。它保存添加到请求中的信息。其中，有一个我们现在需要的重要字段，用于计算当前的 URL。那就是**“req . URL”**。参考下面的代码片段来处理请求并发回响应。

执行以下命令再次启动服务器:

```
node app.js
```

现在，通过您的 web 浏览器重新访问以下 URL:

```
http://localhost:3000
```

您将能够在屏幕上看到一条欢迎信息。

通过上面的要点，您已经知道如何为请求的页面或 URL 发送响应。当您开始学习**express . js:node . js 的后端 web 应用程序框架**时，这将变得容易得多。

你注意到了，我告诉你停止并重启服务器。那么，我们如何解决这个问题，并告诉节点服务器在代码发生变化时自动重启呢？让我们在下一节了解这一点。

# 为自动重启安装 Nodemon

Nodemon 是一个由 rem([https://twitter.com/rem](https://twitter.com/rem))开发的命令行界面(CLI)实用程序，它包装你的节点应用程序，监视文件系统，并自动重启进程。

在这里，您将学习如何安装、设置和配置 nodemon。

您可以使用 NPM 全局安装 nodemon:

```
npm install nodemon -g
```

或者，也可以在本地安装 nodemon。在执行本地安装时，我们可以将 nodemon 安装为一个 dev 依赖项:

```
npm install nodemon --save-dev
```

安装完成后，我们可以使用 nodemon 来启动节点脚本。由于我们的项目中有一个 **"app.js"** 文件，我们可以启动它并观察如下变化:

```
nodemon app.js
```

现在，您已经完成了 Node.js 项目的 Nodemon 设置。

# 下一步是什么？

太棒了。您已经了解了 Node.js，可以开始探索 Node.js 提供的其他特性。一旦你掌握了 Node.js 的基本特性，试试**express . js(node . js 的后端 web 应用框架)**。我相信你会喜欢的。

> *如果你喜欢读这篇文章，并且学到了一些新东西，那么请鼓掌，分享给你的朋友，并关注我以获得我即将发布的文章的更新。你可以在* [*LinkedIn*](https://www.linkedin.com/in/tara-prasad-routray-b83027145/) *联系我。*