# 我如何在 Go 中构造云函数

> 原文：<https://levelup.gitconnected.com/how-i-structure-cloud-functions-in-go-61e151b278ac>

![](img/3d83b2c3958de705e8be6f80164e59fc.png)

在 Go 中部署云功能

> 这篇文章也发表在[https://rizwaniqbal . com/posts/how-I-structure-cloud-functions-in-go/](https://rizwaniqbal.com/posts/how-i-structure-cloud-functions-in-go/)

去年 Golang 第一次发布测试版时，我就开始在其中部署云功能。云函数的 Go 运行时支持目前支持 Go 版本 1.11.6 和 Go 版本 1.13.1 (Beta)。这是最稳定的，在过去 6 个月的生产运行中，我遇到了一些“啊哈”的时刻，如果我有一个像我现在写的这样的指南，我就可以避免。因此，这篇文章更多的是关于旅程和我现在使用的结构，在迭代了一些不同的技术之后。

# 语境

我使用云函数来处理小的日常任务，有时来自客户端，有时作为 Google PubSub 主题的订阅者。所以，我同时部署了[后台函数](https://cloud.google.com/functions/docs/writing/background)和 [HTTP 函数](https://cloud.google.com/functions/docs/writing/http)。所有调用都需要一个服务帐户或一个已登录的 firebase 用户，并且是安全的。考虑到调用函数的次数是要收费的，这些限制是在安全环境中运行所必需的。

关于冷函数调用和热启动也有一些警告。大多数情况下，您的函数已经在实例中运行，并准备好响应触发器，但是，有时您的函数可能由于以下原因而处于“冷状态”:

*   您的功能已经部署，但尚未触发。
*   您的函数已经空闲了足够长的时间，可以被回收以释放资源。
*   您的功能是自动扩展以创造容量。

所有这些都需要仔细考虑您需要在函数中包含哪些依赖项，以及您需要在`init()`方法中放置什么，以确保您在那里完成所有繁重的计算和初始化。

> 考虑到这一点，让我们开始着手处理手头的问题。我们需要以冷启动时间短的方式构建云功能。我们可以在本地运行该函数，如果需要，我们可以定义私有依赖关系。

# 过程

因此，我们有一套标准和界限，我们必须按照这些标准和界限来运作，以充分利用我们的云功能。

我最初的方法是将我所有的功能放在一个存储库中，如果一些常见的依赖关系发生变化，就用部署脚本来部署它们。你可以想象，当我达到第五个函数的时候，事情变得一团糟。除了在这样一个共享代码库中你通常会做的噩梦之外，我不想重复大量代码的诚实意图现在已经在我试图复制文件时结束了，因为你需要部署所有的依赖项以及每个函数。如果你有用于初始化的公共代码，你将会把它复制到你所有的函数中。

像任何正常的 Go 开发人员一样，我决定在同一个存储库中创建一个我自己的包，并将该包导入到我的函数中，但是这也不起作用，因为您需要上传整个包目录和所有其他云函数，因为您上传的是整个根文件夹。

```
// crappy idea|-- pkg
    |
    |-- common code
|-- function1
    |-- function code
|-- function2
    |-- function code
```

因此，我做了下一件明智的事情，创建一个模块，并从一个必须是私有的外部存储库中导入这个模块。对于那些研究围棋的人来说，你已经知道模块是如何工作的，供应商模式是什么，我就不赘述了，如果你需要了解更多，你可以通读[https://blog.golang.org/using-go-modules](https://blog.golang.org/using-go-modules)。这导致了一个新的问题，因为如果您有私有存储库，cloud deploy 将无法提取这些模块，您需要使用供应商模式。这很容易解决，在这里[有详细的记录](https://cloud .google.com/functions/docs/writing/specifying-dependencies-go)。

有了这个，我找到了一个为我工作了一段时间的结构。

```
.
├── README.md
├── cmd
│   ├── func1
│   │   └── main.go
│   ├── func2
│   │   └── main.go
├── deploy.sh
├── go.mod
├── go.sum
├── pkg
│   ├── func1
│   │   ├── func1.go
│   │   ├── someInit.go
│   │   ├── go.mod
│   │   ├── go.sum
│   │   └── vendor
│   ├── func2
│   │   ├── func2.go
│   │   ├── someInit.go
│   │   ├── go.mod
│   │   ├── go.sum
│   │   └── vendor
```

这种结构有其自身的问题。首先，同一个存储库中的多个 go.mod 文件使得几乎不可能在本地测试这些功能。我在本地使用这种结构的过程是:

*   签出到不同的分支
*   推动分支机构的职能转变
*   在`cmd/`包内运行`go get -d func1@branchName`
*   继续运行本地服务器进行测试

这连同`deploy.sh`对我来说很麻烦。多个 go.mod 造成了很大的破坏，我当时想要为不同的函数使用不同的环境变量。然而，所有这些函数共享一个我不想重写的公共初始化脚本。

# 该结构

我现在最终得到的东西，帮助我更快地编码和部署，易于测试，并且易于在本地运行。我做的第一件事是将每个功能拆分到它自己的存储库中。为此，所有的初始化代码被打包到一个外部包中。

这是我现在的函数结构:

```
.
├── README.md
├── .env.yaml
├── .gcloudignore
├── cmd
│   └── main.go
├── deploy.sh
├── functions.go
├── FunctionOne.go
├── FunctionOne_test.go
├── go.mod
├── go.sum
└── vendor
```

我有一个 functions.go，可以在冷启动的情况下初始化依赖关系。每次调用一个函数时，这些都会被重用。

```
// functions.go// Package function is the package that houses my cloud function
package functionimport (
        "context"
        "encoding/json"
        "fmt"
        "io/ioutil"
        "log"
        "net/http"
        "os" "github.com/automaticalldramatic/mypackage"
)// projectID is set from the GCP_PROJECT environment variable, which is
// automatically set by the Cloud Functions runtime.
var envVar = os.Getenv("SOME_VAR")var (
	client *mypackage.Client
	logger *log.Logger
)func init() {
        // err is pre-declared to avoid shadowing client.
        var err error

        logger = log.New(os.Stdout, "[Timefox::Function::"+funcName+"]", log.LstdFlags)
 // client is initialized with context.Background() because it should
        // persist between function invocations.
        client, err = mypakcage.NewClient(context.Background(), envVar)
        if err != nil {
                logger.Fatalf("something: %v", err)
        }
}
```

外部包也有重试和不重试特定情况的错误代码。当您正在编写后台函数，并且不希望某些场景在执行出错时重试时，这些是很重要的。

```
&package.ErrorResponse{
    Error:   errors.New("400 - Bad Request"),
    Message: "You are not welcome here, my dear",
    Code:    http.StatusNoContent, // we don't retry the message with a 204
}
```

为了在本地运行这个函数，我用一个主文件创建了一个命令模块。通常看起来是这样的:

```
// cmd/main.gopackage mainimport (
	"log"
	"net/http"
	"net/http/httputil" "github.com/gorilla/mux" "github.com/automaticalldramatic/server" function "github.com/automaticalldramatic/function-module"
)func main() { options := server.DefaultOptions
	options.Address = ":8080"
	s := server.NewServer(options) r := mux.NewRouter() log.Printf("server started on: %s", options.Address)

	r.Use(VerifyHTTPRequest) r.HandleFunc("/function/method", function.FunctionName)
	s.Handler = r
	log.Fatal(s.ListenAndServe())
}
```

这有助于我在本地测试并应用中间件来检查我的身份验证令牌。

# 部署

我使用`.gcloudignore`来忽略 go.mod 文件，因为由于私有包，我使用的是出售模式。如果你愿意，你可以跳过这一步，但是拥有其中一个被认为是很好的实践。

```
# For more information, run:
#   $ gcloud topic gcloudignore
#
.gcloudignore
deploy.sh.env.yamlgo.mod
go.sum
```

`deploy.sh`可以被任何配置项替换。将`.env.yaml`添加到`.gitignore`和`.gcloudignore`中，并且仅用于本地部署。当您使用 CI 设置时，您最好将 env 变量和秘密添加到您的 CI 中。

然而，这是我使用环境文件从我的本地部署文件部署该函数所运行的基本内容:

```
gcloud functions deploy FunctionName --entry-point FunctionName --trigger-http  --region europe-west1 --runtime go113
 --env-vars-file ./.env.yaml  --clear-labels --update-labels version=someVersion
```

[](https://github.com/agilefoxHQ/go-function-template) [## GitHub-agile fox HQ/go-function-template

### 该函数是使用“agilefoxhq/go-template-function”存储库生成的。这是一个谷歌云函数写的…

github.com](https://github.com/agilefoxHQ/go-function-template) 

我希望这有助于您部署和维护您的云功能。如果您需要进一步的帮助，文档—https://cloud . Google . com/functions/docs/how-to 非常好，几乎涵盖了所有的用例。