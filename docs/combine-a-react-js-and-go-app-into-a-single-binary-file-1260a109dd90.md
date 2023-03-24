# 将 React.js 和 Go 应用程序合并成一个二进制文件

> 原文：<https://levelup.gitconnected.com/combine-a-react-js-and-go-app-into-a-single-binary-file-1260a109dd90>

![](img/0f2cef56dc6ce898c716a0fdcb0a4a83.png)

用 [gopherize.me](https://gopherize.me/) 创建

# 介绍

本文的目的是演示如何使用 Go server 服务 React.js 单页应用程序(SPA ),并将整个应用程序合并到一个二进制文件中，该文件可以在任何操作系统中运行。

# 先决条件

对于本教程，您需要:

*   Go 环境([指令](https://golang.org/doc/install))
*   Node.js 环境([指令](https://nodejs.org/en/download/))
*   Go 包 [*go.rice*](https://github.com/GeertJohan/go.rice) ( `$ go get [github.com/GeertJohan/go.rice](https://github.com/GeertJohan/go.rice)`)
*   [*创建-反应-应用*](https://create-react-app.dev/) 工具(`$ npm i create-react-app`)
*   React 库[*react-router-DOM*](https://github.com/ReactTraining/react-router)(`$ npm i react-router-dom`)
*   一个新的目录，e.x `react-go-binary`，在你的`GOPATH`里面，我们所有的代码都将存放在那里

# 创建我们的 React.js SPA

作为第一步，我们将使用脸书 React 工程师团队开发的精彩 CLI 工具 *create-react-app* 生成 React.js 前端客户端。

我们所要做的就是在我们的项目目录(例如，react-go-binary)中运行`$ npx create-react-app ./client`,一个新的目录`client`将被创建，其中包括非常标准的源文件。

该客户端将包括一个简单的导航栏和两(2)个页面，一个主页和一个 ping 页面，按钮在我们的 REST API 上发出 http 请求。

我们将把我们的逻辑放在关于路由器、页面的`src/App.js`文件上，并从`src/index.js`文件呈现我们的应用程序。

在`src/App.js`文件中定义 *App* 功能组件，包括路由器、home 组件、ping 组件和 navbar 组件。

在`src/index.js`文件中导入并呈现我们的应用程序。

我们可以安全地删除所有其他源文件，因为我们的应用程序中没有使用它们。

是时候构建我们的应用程序了。这非常简单，我们所要做的就是运行`$ npm run build`，一个新的目录`build`就会出现。我们将进一步使用这个目录，从我们的 Go 服务器提供 SPA 服务。

# 创建我们的 Go 服务器

完成 React.js 前端客户端后，就该创建 Go 服务器了。总而言之，将使用我们之前生成的`build`文件夹，从我们的前端客户端提供所有必要的文件，并公开一个端点`/ping`，该端点将使用 *pong* 消息进行响应。

在我们的项目目录中创建一个新文件`main.go`，它将包含我们所有的源代码。

上述代码的分解非常简单:

1.  从 React.js 应用程序的构建目录创建一个饭盒
2.  为`/api/ping`端点定义一个处理程序
3.  定义一个处理程序来处理 rice box 中的所有静态文件
4.  定义一个无所不包的处理程序并提供 React.js `index.html`文件，该文件将负责前端路由
5.  最后在`8080`端口启动服务器

# 最后的步骤

毕竟，我们现在必须构建二进制文件。我们可以在项目目录的根目录下使用下面的命令来完成。

```
$ CGO_ENABLED=0 go build -ldflags "-w" -a -o react-go .
```

> 以上命令将为您当前的操作系统构建一个二进制文件。如果您想为一个不同的构建创建一个构建，您必须定义`GOOS`和`GOARCH`变量([引用](https://golang.org/doc/install/source#environment))。例如，`$ CGO_ENABLED=0 GOOS=darwin GOOS=amd64 go build -ldflags “-w” -a -o react-go .`命令将为 macOS 64 位操作系统构建一个二进制文件。

最后一步，我们必须借助 rice 将`build`文件夹嵌入到我们的二进制文件中。这非常简单，只需一个命令就能实现

```
$ rice append -i . --exec react-go
```

> Go 语言没有提供将静态文件嵌入二进制文件的现成方法，这就是为什么我们必须使用 rice 包。最近[Ross Cox 提交了一份](https://go.googlesource.com/proposal/+/master/design/draft-embed.md)设计草案，这一特性很可能在该语言的未来版本中得到支持。

就是这样！我们有一个单独的二进制文件`react-go`，它包含了一个完整的应用程序，可以在我们选择的任何操作系统上运行。

# 源代码

你可以在[https://github.com/chanioxaris/react-go-binary](https://github.com/chanioxaris/react-go-binary)找到本教程的所有源代码