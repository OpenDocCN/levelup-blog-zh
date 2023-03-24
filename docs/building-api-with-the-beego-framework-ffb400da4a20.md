# 用 Beego 框架构建 API

> 原文：<https://levelup.gitconnected.com/building-api-with-the-beego-framework-ffb400da4a20>

每当我们创建一个应用程序时，web 应用程序框架是必不可少的，因为它通过消除编写大量复杂代码的需要并提供模型、服务和其他组件等功能，大大加快并简化了我们的工作。使用应用程序框架，我们可以利用其架构设计的优势，并促进应用程序的增长。

模型-视图-控制器是 web 应用程序框架的一种流行形式。MVC 的工作原理是分别呈现每一个。我们将向您展示如何使用 Bee 框架帮助您使 API 变得简单快捷

打开终端，通过命令安装***github.com/beego/bee***包

```
$ go get github.com/beego/bee
```

将下面几行添加到 **~/。bashrc** ，还会继续。但是您可以使用其他路径作为 **GOPATH** ，而不是示例中的 **$HOME/go** 。

```
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

和 put 命令为您的特定环境设置 shell。

```
$ source ~/.bashrc
```

现在，你可以使用 ***蜂*** 命令

```
$ bee help
```

您应该会看到以下输出:

```
Bee is a Fast and Flexible tool for managing your Beego Web Application.USAGE
    bee command [arguments]AVAILABLE COMMANDSversion     Prints the current Bee version
    migrate     Runs database migrations
    api         Creates a Beego API application
    bale        Transforms non-Go files to Go source files
    fix         Fixes your application by making it compatible with newer versions of Beego
    dlv         Start a debugging session using Delve
    dockerize   Generates a Dockerfile for your Beego application
    generate    Source code generator
    hprose      Creates an RPC application based on Hprose and Beego frameworks
    new         Creates a Beego application
    pack        Compresses a Beego application into a single file
    rs          Run customized scripts
    run         Run the application by starting a local development server
    server      serving static content over HTTP on portUse bee help [command] for more information about a command.ADDITIONAL HELP TOPICSUse bee help [topic] for more information about that topic.
bee: Too many arguments.
Use bee help for more information.
```

我们想创建一个 API 应用程序，我们可以使用 ***蜜蜂*** ***帮助 api*** 命令获取更多信息

```
$ bee help apiUSAGE
  bee api [appname]OPTIONS
  -conn
      Connection string used by the driver to connect to a database instance.-driver
      Database driver. Either mysql, postgres or sqlite.-tables
      List of table names separated by a comma.DESCRIPTION
  The command 'api' creates a Beego API application.Example:
      $ bee api [appname] [-tables=""] [-driver=mysql] [-conn="root:[@tcp](http://twitter.com/tcp)(127.0.0.1:3306)/test"]If 'conn' argument is empty, the command will generate an example API application. Otherwise the command
  will connect to your database and generate models based on the existing tables.The command 'api' creates a folder named [appname] with the following structure:├── main.go
     ├── conf
     │     └── app.conf
     ├── controllers
     │     └── object.go
     │     └── user.go
     ├── routers
     │     └── router.go
     ├── tests
     │     └── default_test.go
     └── models
           └── object.go
           └── user.go
```

让我们使用 ***bee api*** 命令创建一个项目

```
$ bee api my-api -driver=postgres \
-conn="postgres://user:pass@localhost:5432/db?sslmode=disable" && \
cd my-api
```

它将在控制台上创建一个新项目，如下图所示

```
______
| ___ \
| |_/ /  ___   ___
| ___ \ / _ \ / _ \
| |_/ /|  __/|  __/
\____/  \___| \___| v1.10.0
23:42:16 INFO     ▶ 0001 Creating API...
 create  /Users/go/src/my-api
 create  /Users/go/src/my-api/conf
 create  /Users/go/src/my-api/controllers
 create  /Users/go/src/my-api/tests
 create  /Users/go/src/my-api/conf/app.conf
 create  /Users/go/src/my-api/main.go
23:42:16 INFO     ▶ 0002 Using 'postgres' as 'driver'
23:42:16 INFO     ▶ 0003 Using 'postgres://user:pass@localhost:5432/db?sslmode=disable' as 'conn'
23:42:16 INFO     ▶ 0004 Using '' as 'tables'
23:42:16 INFO     ▶ 0005 Analyzing database tables...
23:42:16 INFO     ▶ 0006 Creating model files...
 create  /Users/go/src/my-api/models/migrations.go
 create  /Users/go/src/my-api/models/cars.go
23:42:16 INFO     ▶ 0007 Creating controller files...
 create  /Users/go/src/my-api/controllers/migrations.go
 create  /Users/go/src/my-api/controllers/cars.go
23:42:16 INFO     ▶ 0008 Creating router files...
 create  /Users/go/src/my-api/routers/router.go
23:42:16 SUCCESS  ▶ 0009 New API successfully created!
```

下一步我们需要通过命令设置 go mod

```
$ go mod init
```

让我们通过命令运行 API

```
$ bee run
```

一旦命令成功执行，如下所示

```
______
| ___ \
| |_/ /  ___   ___
| ___ \ / _ \ / _ \
| |_/ /|  __/|  __/
\____/  \___| \___| v1.10.0
23:50:56 INFO     ▶ 0001 Using 'my-api' as 'appname'
23:50:56 INFO     ▶ 0002 Initializing watcher...
my-api/routers
my-api
23:51:01 SUCCESS  ▶ 0003 Built Successfully!
23:51:01 INFO     ▶ 0004 Restarting 'my-api'...
23:51:01 SUCCESS  ▶ 0005 './my-api' is running...
23:51:01.964 [I] [router.go:270]  /Users/go/src/my-api/controllers no changed
23:51:01.965 [I] [router.go:270]  /Users/go/src/my-api/controllers no changed
23:51:02.003 [I] [asm_amd64.s:1373]  http server Running on [http://:8080](http://:8080)
```

我们将使用‘bee generate scaffold’命令。该命令需要三个选项:

*   田地（复数）；场；域；字段
*   驾驶员
*   指挥操舵

```
$ bee generate scaffold user -fields="first_name:string:255,last_name:string:255,email:string:255" -driver=postgres -conn="postgres://user:pass@localhost:5432/db?sslmode=disable"
```

这将要求您一步一步地遵循以下步骤

```
______
| ___ \
| |_/ /  ___   ___
| ___ \ / _ \ / _ \
| |_/ /|  __/|  __/
\____/  \___| \___| v1.10.0
00:08:46 INFO     ▶ 0001 Do you want to create a 'user' model? [Yes|No]
Yes
00:08:52 INFO     ▶ 0002 Using 'User' as model name
00:08:52 INFO     ▶ 0003 Using 'models' as package name
 create  /Users/go/src/my-api/models/user.go
00:08:53 INFO     ▶ 0004 Do you want to create a 'user' controller? [Yes|No]
Yes
00:08:56 INFO     ▶ 0005 Using 'User' as controller name
00:08:56 INFO     ▶ 0006 Using 'controllers' as package name
00:08:56 INFO     ▶ 0007 Using matching model 'User'
 create  /Users/go/src/my-api/controllers/user.go
00:08:56 INFO     ▶ 0008 Do you want to create views for this 'user' resource? [Yes|No]
No
00:08:58 INFO     ▶ 0009 Do you want to create a 'user' migration and schema for this resource? [Yes|No]
Yes
 create  /Users/go/src/my-api/database/migrations/20200528_000901_user.go
00:09:01 INFO     ▶ 0010 Do you want to migrate the database? [Yes|No]
No
00:09:06 SUCCESS  ▶ 0011 All done! Don't forget to add  beego.Router("/user" ,&controllers.UserController{}) to routers/route.go00:09:06 SUCCESS  ▶ 0012 Scaffold successfully generated!
```

一旦命令成功执行，bee 将创建一个文件。/models/user.go，。/controllers/user.go 并。/数据库/迁移/用户. go

打开 route.go，按如下方式更改代码:

```
// Package routers define version and api routes
// [@APIVersion](http://twitter.com/APIVersion) 1.0.0
// [@Title](http://twitter.com/Title) My API
// [@Description](http://twitter.com/Description) My API
package routersimport (
 "my-api/controllers""github.com/astaxie/beego"
)func init() {
 ns := beego.NewNamespace("/v1",
  beego.NSNamespace("/user",
   beego.NSInclude(
    &controllers.UserController{},
   ),
  ),
 )
 beego.AddNamespace(ns)
}
```

如果您想要生成 swagger 文档，请使用命令

```
$ bee generate docs
```

一旦命令成功执行，如下所示

```
______
| ___ \
| |_/ /  ___   ___
| ___ \ / _ \ / _ \
| |_/ /|  __/|  __/
\____/  \___| \___| v1.10.0
00:14:28 SUCCESS  ▶ 0001 Docs successfully generated!
```

我们可以通过以下步骤让它自动生成文档:

通过设置`conf/app.conf`中的`EnableDocs = true`来启用文档。

使用下面的命令运行 API 应用程序并自动重建文档。

```
$ bee run -downdoc=true -gendoc=true
```

参观你项目中的`/swagger`

您的 API 文档现在是可用的。打开你的浏览器并检查它

![](img/c920837122f6342abb77af6aee7f3b1d.png)

API 将在默认的 Bee 端口`8080`上运行，浏览`http://localhost:8080/swagger`将呈现 swagger UI，你可以通过使用 swagger UI 来尝试你的 API。

在这一章中，我们展示了 Bee 框架，它是最流行和最常用的 API MVC 框架之一。这个框架帮助你生成一些代码，非常简单