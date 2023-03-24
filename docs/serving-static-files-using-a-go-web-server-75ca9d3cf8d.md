# 使用 Go Web 服务器提供静态文件

> 原文：<https://levelup.gitconnected.com/serving-static-files-using-a-go-web-server-75ca9d3cf8d>

## 使用 Go web 服务器提供静态文件的初学者指南。

![](img/7ad795d18ff8258d5d2267697b126dc8.png)

[墙](https://unsplash.com/@walling?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[防溅板](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍照

在本教程中，您将了解如何使用 Go web 服务器提供静态文件。如果您已经使用 NodeJs 或任何其他框架开发过应用程序，您可能已经更详细地体验过这个过程。

**Golang** 有点类似于 **C** 编程语言。如果你对 C 有所了解，那么 Golang 对你来说应该不难。本教程基本上由两部分组成；第一部分将介绍如何创建一个简单的 Go web 服务器，第二部分将演示如何使用 Go 服务器提供静态文件(如 HTML 和 CSS)。

# 第一部分

在这一部分，我将讨论如何创建一个基本的 Go 服务器。首先，你需要在电脑上安装 Golang。如果你的电脑上没有安装 Golang，请点击下面的链接下载 Golang。

[](https://golang.org/dl/) [## 下载

### 下载适合您系统的二进制版本后，请按照安装说明进行操作。如果你是…

golang.org](https://golang.org/dl/) 

一旦你设置了 Golang，打开你的文本编辑器，创建一个新文件，并确保使用文件扩展名为`.go`让我们看看下面的代码片段。

上面的代码片段描述了基本的 Go web 服务器，您可以通过在终端上运行`$ go run main.go`在浏览器上打开它。键入`localhost:8080`，你将在浏览器上看到输出。

您可以添加另一个函数来处理到不同页面的路由，并通过`main` 函数调用它。

新功能`about_handler`能够处理转发到`/about`端点的路由。前往`localhost:8080/about`，你会看到新的网页。

您可以在项目中创建一个单独的文件夹，并将所有 HTML、JS 和 CSS 文件放在一个地方，而不是在代码中添加几个函数。对于你来说，用最新的特性来维护和更新你的项目是很容易的。让我们转到第二部分，看看我们如何做到这一点。

# 第二部分

在你的项目中创建一个新文件夹，命名为`static`现在你可以把你所有的 HTML，CSS 和 JS 文件放在这个文件夹中。对于本教程，我将创建示例静态文件。让我们看看如何使用 Golang 为他们服务。

让我们创建 index.html、about.html、styles.css 和 routes.js 文件(你可以从我的[**Repo**](https://github.com/randiltennakoon/golang-web-server/tree/main/static)**中找到示例静态文件)。所有这些文件都需要放在静态文件夹**中。一旦完成了静态文件，就可以开始创建 Go 服务器了。****

**让我们关注下面的代码片段:**

**一旦您运行命令`$ go run server.go`，它将在浏览器上打开`index.html`文件，并按照您在代码中指定的方式转发项目中的其余流量(确保您在运行代码之前已经将所有文件放在了`static`文件夹中)。**

# **结论**

****恭喜*！*** 您已经使用 Go webserver 成功地提供了静态文件。感谢阅读。我希望本文中的信息对您有用。如果您发现任何可疑之处，请在下面留下您的回复，以便我可以回复您。**

***快乐编码！*👨🏻‍💻**