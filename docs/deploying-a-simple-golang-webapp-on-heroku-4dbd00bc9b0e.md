# 在 Heroku 上部署一个简单的 Golang Webapp

> 原文：<https://levelup.gitconnected.com/deploying-a-simple-golang-webapp-on-heroku-4dbd00bc9b0e>

# 如何将 Golang Web 应用程序部署到 Heroku

[Go 编程语言](https://golang.org/)，通常被称为“golang”，已经在 DevOps 社区中获得了很多受之无愧的关注。许多最流行的工具如 [Docker](https://www.docker.com/) 、 [Kubernetes](https://kubernetes.io/) 和 [Terraform](https://www.terraform.io/) 都是用 Go 编写的，但它也是构建 web 应用程序和 API 的一个很好的选择。

Go 为您提供了编译语言的所有速度和性能，但感觉像是用解释语言编码。这归结为一个很棒的工具，你可以从默认提供的编译器、运行器、测试套件和代码格式化程序中获得。此外，为了最大限度地利用当今的多核或多 cpu 执行环境，Go 社区发展如此迅速的原因显而易见。

Go 给人的感觉是，它是根据特定的目标创建的，这导致了一种看似简单的语言设计，这种设计简单易学，而且不影响功能。

在这篇文章中，我将向您展示在 Go 中开发一个简单的 web 应用程序，将其打包成一个轻量级 Docker 映像，并将其部署到 [Heroku](https://heroku.com/) 是多么容易。我还将展示 Go 的一个相当新的特性:内置的包管理。

# Go 模块

对于本文，我将使用 Go 的内置模块支持。

Go 1.0 于 2012 年 3 月发布。直到 1.11 版本(2018 年 8 月发布)，开发 Go 应用需要[为每个“工作区”管理一个 GOPATH](https://golang.org/doc/gopath_code.html) ，类似于 java 的`JAVA_HOME`，你所有的 Go 源代码和任何第三方库都存储在`GOPATH`下面。

我总是觉得这有点令人不快，相比之下，用 Ruby 或 Javascript 之类的语言开发代码，我可以用更简单的目录结构来隔离每个项目。在这两种语言中，一个文件(`Gemfile`用于 Ruby，`package.json`用于 Javascript)列出了所有的外部库，包管理器为我跟踪管理和安装依赖项。

> *我不是说你不能管理* `*GOPATH*` *环境变量来将项目相互隔离。我特别发现包管理器方法更容易。*

谢天谢地，Go now 内置了优秀的包管理，所以这不再是个问题。然而，您可能会发现在许多旧的博客帖子和文章中提到了`GOPATH`,这可能有点令人困惑。

# 你好，世界！

让我们开始我们的 web 应用程序。像往常一样，这将是一个非常简单的“你好，世界！”app，因为我想把重点放在开发和部署过程上，把这篇文章控制在合理的长度。

# 先决条件

你需要:

*   golang 的最新版本(我用的是 1.14.9。)
*   码头工人
*   一个 [Heroku](https://heroku.com/) 账户(免费账户在这个例子中运行良好。)
*   Heroku 命令行客户端
*   [git](https://git-scm.com/)

# 开始模式初始化

为了创建我们的新项目，我们需要为它创建一个目录，并使用`go mod init`命令将其初始化为 Go 模块。

```
mkdir helloworld
cd helloworld
go mod init digitalronin/helloworld
```

> *通常的做法是使用您的 github 用户名来保持您的项目名称的全局唯一性，并避免与您的任何项目依赖项的名称冲突，但是您可以使用您喜欢的任何名称。*

现在您会在目录中看到一个`go.mod`文件。这是 Go 跟踪任何项目依赖关系的地方。如果您查看该文件的内容，它们应该如下所示:

```
module digitalronin/helloworld

go 1.14
```

让我们开始提交我们的更改:

```
git init
git add *
git commit -m "Initial commit"
```

# 杜松子酒

我们将在我们的 web 应用程序中使用 [gin](https://github.com/gin-gonic/gin) 。Gin 是一个轻量级的 web 框架，类似于 Ruby 的 [Sinatra](http://sinatrarb.com/) ，Javascript 的 [express.js](https://expressjs.com/) ，或者 Python 的 [Flask](https://pypi.org/project/Flask/) 。

创建一个名为`hello.go`的文件，包含以下代码:

```
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/hello", func(c *gin.Context) {
		c.String(200, "Hello, World!")
	})

	r.Run(":3000")
}
```

让我们稍微分解一下:

`r := gin.Default()`

这将创建一个路由器对象`r`，使用 gin 自带的默认设置。

然后，我们为路径`/hello`的任何 HTTP GET 请求分配一个处理函数，并返回字符串“Hello，World！”和 200 (HTTP OK)状态代码:

```
 r.GET("/hello", func(c *gin.Context) {
		c.String(200, "Hello, World!")
	})
```

最后，我们启动 web 服务器，并告诉它监听端口 3000:

```
r.Run(":3000")
```

要运行此代码，请执行:

`go run hello.go`

您应该会看到如下输出:

```
go: finding module for package github.com/gin-gonic/gin
go: found github.com/gin-gonic/gin in github.com/gin-gonic/gin v1.6.3
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /hello                    --> main.main.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :3000
```

现在，如果你在网络浏览器中访问`http://localhost:3000/hello`，你应该会看到“你好，世界！”

注意，我们不必单独安装 gin，甚至不必编辑我们的`go.mod`文件来声明它是一个依赖项。Go 发现了这一点，并为我们做了必要的更改，这就是我们在输出中看到这些行时发生的情况:

```
go: finding module for package github.com/gin-gonic/gin
go: found github.com/gin-gonic/gin in github.com/gin-gonic/gin v1.6.3
```

如果您查看`go.mod`文件，您会看到它现在包含以下内容:

```
module digitalronin/helloworld

go 1.14

require github.com/gin-gonic/gin v1.6.3 // indirect
```

你现在还会看到一个`go.sum`文件。这是一个文本文件，包含对所有包依赖关系的特定版本的引用，以及它们的依赖关系，以及相关模块版本内容的加密散列。

对于 Javascript 项目来说,`go.sum`文件的功能类似于`package-lock.json`,或者在 Ruby 项目中类似于`Gemfile.lock`,你应该总是将它和你的源代码一起签入版本控制。

让我们现在就开始吧:

```
git add *
git commit -m "Add 'Hello world' web server"
```

# 提供 HTML 和 JSON

我不打算深入研究用 gin 可以构建什么，但是我想演示更多的功能。特别是发送 JSON 响应和服务静态文件。

我们先来看看 JSON 的响应。将以下代码添加到您的`hello.go`文件中，就在`r.GET`块之后:

```
api := r.Group("/api")

api.GET("/ping", func(c *gin.Context) {
  c.JSON(200, gin.H{
    "message": "pong",
  })
})
```

这里，我们在路径`/api`后面创建了一个“组”路由，其中有一个路径`/ping`，它将返回一个 JSON 响应。

有了这些代码，用`go run`运行服务器，然后点击新的 API 端点:

`curl [http://localhost:3000/api/ping](http://localhost:3000/api/ping)`

您应该得到响应:

`{"message":"pong"}`

最后，让我们的 web 服务器服务静态文件。Gin 为此提供了一个额外的库。

将`hello.go`文件顶部的导入块改为:

```
import (
	"github.com/gin-gonic/contrib/static"
	"github.com/gin-gonic/gin"
)
```

> *最流行的代码编辑器都有 golang 支持包，你可以安装它们，它们会自动为你处理* `*import*` *声明，当你在代码中使用新模块时，它们会自动更新。*

然后，在`main`函数中添加这一行:

```
r.Use(static.Serve("/", static.LocalFile("./views", true)))
```

我们的 web 应用程序的完整代码现在如下所示:

`hello.go`

```
package main

import (
	"github.com/gin-gonic/contrib/static"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/hello", func(c *gin.Context) {
		c.String(200, "Hello, World!")
	})

	api := r.Group("/api")

	api.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})

	r.Use(static.Serve("/", static.LocalFile("./views", true)))

	r.Run()
}
```

`r.Use(static.Serve...`行使我们的 web 服务器能够服务来自`views`目录的任何静态文件，所以让我们添加一些:

```
mkdir -p views/css
```

`views/css/stylesheet.css`

```
body {
  font-family: Arial;
}

h1 {
  color: red;
}
```

`views/index.html`

```
<html>
  <head>
    <link rel="stylesheet" href="/css/stylesheet.css" />
  </head>
  <body>
    <h1>Hello, World!</h1>
  </body>
</html>
```

现在，使用`go run hello.go`重启网络服务器，并访问`http://localhost:3000`，您应该会看到这样的消息:

![](img/5b83252474a0227e99d61dc1c3bb71b2.png)

# 归档

我们已经编写了 go web 应用程序，现在让我们将它打包成 Docker 图像。我们可以把它创建成 Heroku buildpack，但是 Go 的一个很好的特性是你可以把你的软件作为一个单独的二进制文件分发。这是 Go 真正大放异彩的领域，使用基于 Docker 的 Heroku 部署让我们可以利用这一点。此外，这种技术并不局限于 Go 应用程序:您可以对任何语言的项目使用基于 Docker 的 Heroku 部署。所以，这是一个很好理解的技巧。

到目前为止，我们已经用`go run`命令运行了我们的代码。要将其编译成一个可执行的二进制文件，我们只需运行:

`go build`

这将编译我们所有的 Go 源代码并创建一个文件。默认情况下，输出文件将根据模块名命名，因此在我们的例子中，它将被称为`helloworld`。

我们可以这样运行:

`./helloworld`

我们可以像以前一样访问相同的 HTTP 端点，无论是使用`curl`还是我们的网络浏览器。

> *静态文件没有编译成二进制，所以如果你把* `*helloworld*` *文件放在不同的目录下，它将找不到* `*views*` *目录来为我们的 HTML 和 CSS 内容服务。*

这就是我们为我们正在开发的任何平台(在我的例子中，我的 Mac 笔记本电脑)创建二进制文件所需要做的全部工作。然而，要在 Docker 容器中运行(为了最终部署到 Heroku ),我们需要为 Docker 容器将要运行的任何架构编译一个二进制文件。

我将使用 [alpine linux](https://www.alpinelinux.org/) ，所以让我们在那个操作系统上构建我们的二进制文件。用以下内容创建一个`Dockerfile`:

```
FROM golang:1.14.9-alpine
RUN mkdir /build
ADD go.mod go.sum hello.go /build/
WORKDIR /build
RUN go build
```

在这个映像中，我们从`golang`基础映像开始，添加我们的源代码，并运行`go build`来创建我们的`helloworld`二进制文件。

我们可以这样建立我们的码头工人形象:

`docker build -t helloworld .`

> *不要忘记该命令末尾的* `*.*` *。它告诉 Docker 我们想要使用当前目录作为构建上下文。*

这创建了一个 Docker 映像，其中包含我们的`helloworld`二进制文件，但它也包含了所有需要**编译**我们的代码的 Go 工具，我们不希望在最终的部署映像中包含这些，因为这会使映像变得不必要的大。在 Docker 映像上安装不必要的可执行文件也有安全风险。

我们可以看到 Docker 图像的大小，如下所示:

```
$ docker images helloworld
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
helloworld          latest              9657ec1ca905        4 minutes ago       370MB
```

相比之下，`alpine`映像(一个轻量级 linux 发行版，通常用作 docker 映像的基础)要小得多:

```
$ docker images alpine
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
alpine              latest              caf27325b298        20 months ago       5.53MB
```

在我的 Mac 上，`helloworld`二进制文件大约有 14MB，所以 golang 图像比它需要的要大得多。

我们想要做的是使用这个 docker 文件来**构建**我们的 helloworld 二进制文件以在 alpine linux 上运行，然后将编译后的二进制文件复制到 alpine 基础映像中，而不需要所有额外的 golang 工具。

我们可以使用“多级”Docker 构建来实现这一点。将 docker 文件更改为如下所示:

```
FROM golang:1.14.9-alpine AS builder
RUN mkdir /build
ADD go.mod go.sum hello.go /build/
WORKDIR /build
RUN go build

FROM alpine
RUN adduser -S -D -H -h /app appuser
USER appuser
COPY --from=builder /build/helloworld /app/
COPY views/ /app/views
WORKDIR /app
CMD ["./helloworld"]
```

在第一行，我们将最初的 Docker 图像标记为`AS builder`。

稍后，我们切换到不同的基础映像`FROM alpine`，然后从我们的构建器映像中复制`helloworld`二进制文件，如下所示:

```
COPY --from=builder /build/helloworld /app/
```

建立新的 Docker 形象:

`docker build -t helloworld .`

现在，这是你所期望的基本阿尔卑斯山图像加上我们的 helloworld 双星的大小:

```
$ docker images helloworld
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
helloworld          latest              1d6d9cb64c7e        8 seconds ago       20.7MB
```

我们可以像这样从 Docker 映像运行我们的 web 服务器。(如果您使用`go run hello.go`或`./helloworld`运行另一个版本，您需要首先停止那个版本，以释放端口 3000。)

`docker run --rm -p 3000:3000 helloworld`

> *除了* *之外，* `*go run hello.go*` *和* `*./helloworld*` *版本* ***中的 web 服务器都有自己的静态文件副本。因此，如果您更改了* `*views/*` *中的任何文件，您将不会看到这些更改，直到您重新构建 Docker 映像并重启容器。***

# 部署到 Heroku

现在我们有了 dockerized web 应用程序，让我们将它部署到 Heroku。Heroku 是一个 PaaS 提供商，它简化了应用程序的部署和托管。您可以通过 Heroku UI 或 Heroku CLI 设置和部署您的应用程序。对于这个例子，我们将使用 Heroku 命令行应用程序。

# 从环境变量获取端口

我们已经将我们的 web 服务器硬编码为在端口 3000 上运行，但这在 Heroku 上不起作用。相反，我们需要修改它，使其运行在环境变量`PORT`中指定的端口号上，Heroku 会自动提供这个端口号。

为此，修改靠近我们的`hello.go`文件底部的`r.Run`行，并删除`":3000"`字符串值，这样该行变成:

`r.Run()`

gin 的默认行为是在`PORT`环境变量中的任何端口上运行(或者端口 8080，如果没有指定的话)。这正是 Heroku 需要的行为。

# 设置我们的 Heroku 应用程序

首先，登录 Heroku:

`heroku login`

现在，创建一个应用:

`heroku create`

告诉 Heroku 我们想使用 docker 文件而不是 buildpack 来构建这个项目:

`heroku stack:set container`

为此，我们还需要创建一个`heroku.yml`文件，如下所示:

```
build:
  docker:
    web: Dockerfile

run:
  web: ./helloworld
```

`heroku.yml`文件是一个清单，它定义了我们的应用程序，并允许我们指定在应用程序供应期间使用的附加组件和配置变量。

接下来，添加 git 并提交这些文件，然后推送到 heroku 进行部署:

`git push heroku main`

> *我的 git 配置使用* `*main*` *作为默认分支。如果你的默认分支叫做* `*master*` *，那么改为运行* `*git push heroku master*` *。*

您应该看到 Heroku 从您的 Docker 文件构建图像，并将其推送到 Heroku Docker 注册表。命令完成后，您可以通过运行以下命令在浏览器中查看部署的应用程序:

`heroku open`

# 结论

简单回顾一下，下面是我们今天所学内容的总结:

*   创建 golang web 应用程序，使用 go 模块和 gin web 框架来提供字符串、JSON 和静态文件
*   使用多级 Docker 文件创建轻量级 Docker 映像
*   使用`heroku stack:set container`和`heroku.yml`文件将基于 Docker 的应用程序部署到 Heroku

关于如何使用 Go 构建 web 应用程序和 API，我在本文中只触及了皮毛。我希望这足以让您开始使用自己的 golang web 应用程序。