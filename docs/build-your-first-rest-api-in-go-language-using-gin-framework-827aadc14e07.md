# 使用 Gin 框架用 Go 语言构建你的第一个 REST API

> 原文：<https://levelup.gitconnected.com/build-your-first-rest-api-in-go-language-using-gin-framework-827aadc14e07>

![](img/f2869fe772af8be4a522bafd3330ac3c.png)

Go 是一种开源编程语言，可以轻松构建简单、可靠、高效的软件。

> 在本教程中，我们将创建一个允许用户从服务器上传和下载文件的 API。最终的源代码可以在 [**GitHub**](https://github.com/mehrdadep/go-rest-example) 上找到。

我们将使用 [Gin](https://github.com/gin-gonic/gin) web 框架来处理我们的 REST APIs。Gin 是一个用 Go (Golang)编写的 HTTP web 框架。它有一个类似 Martini 的 API，性能更好。

## 先决条件

*   我们假设您已经安装了 GO。如果没有，请在此查看[。](https://golang.org/doc/install)
*   你需要知道一点关于 [Go 模块](https://blog.golang.org/using-go-modules)的知识。我们将使用模块作为我们的依赖管理解决方案。

# 第一步

首先，您需要用您的模块名创建一个目录。然后使用`go mod init`命令初始化 go 模块。现在您必须使用`go get github.com/gin-gonic/gin`将 Gin 作为您的依赖项之一

现在在你的模块的根目录下创建一个`main.go`文件。这个文件是一切开始的地方。您的主文件应该是这样的:

```
package main

func main(){
   // function starts here
}
```

现在创建一个名为 api 的目录，并在 api 目录下创建两个名为 ***控制器*T22**和*视图*的新目录。我们将把控制器背后的逻辑从我们的视图中分离出来。所以现在你的项目应该有这样的结构:****

```
/go_rest_example/
    /api/
        /service/
            main.go
        /view/
            main.go
    main.go
    go.mod
    go.sum
```

# 我们上传点东西吧

在这一部分，我们将创建我们的上传 API。在 view 的 main.go 文件中创建一个名为`StartServer`的函数。这个函数将使用 Gin 框架来处理 API 端点，并在本地机器的一个端口上启动一个 web 服务器。所以不要忘记在你的文件顶部导入`gin-gonic/gin`包。首先我们必须从 Gin 的`Default`函数中创建一个路由器。

我们可以在路由器上使用 Gin 的`Group`功能将一堆相同的端点分组。在这个项目中，我们将有两个端点，一个用于上传，另一个用于下载。

将我们的路由器分组在`/api` 端点上，因为我们所有的端点都以它开始，然后将`/api`与 `/v1` 分组，因为(当然)这是这个 API 的第一个版本，最后将 `/v1`分组在 `/files`上。现在我们有了最终的终点:

```
POST /api/v1/files/ 
GET /api/v1/files/:name/
```

要在您的最终端点上指定`http`方法，请使用大写字母的 http 方法名(例如`POST`、`GET`)。这些函数的第一个参数是端点地址，第二个参数是处理 API 调用的函数。

Gin 将请求放在`*gin.Context`类型中，因此您可以从`*gin.Context`变量中访问请求头、文件等。您应该将该类型作为参数传递给`http`方法的第二个参数。

要在自定义端口上运行您的服务器，请使用带有`“:PORT_NUM”`的 run 方法。例如，要在端口 8095 上运行，请使用`router.Run(“:8095”)`

视图目录中的 main.go 文件现在应该是这样的:

```
package view

import (
   "github.com/gin-gonic/gin"
)

func StartServer() {
   router := gin.Default()
   api := router.Group("/api")
   v1 := api.Group("/v1")
   files := v1.Group("/files")
   files.POST("/", func(c *gin.Context) { // Controller code's goes here
})
   router.Run(":8080")
}
```

现在，我们必须从请求中接收一个文件，并将其保存在某个地方。使用 Gin 的`FormFile`函数从请求中检索单个文件。如果出现任何错误，您可以使用 Gin 的`JSON`函数和 Gin 的`H` 类型返回一个`JSON`响应。现在你的`POST`函数的主体应该是这样的:

```
file, err := c.FormFile("file")
if err != nil {
   c.JSON(http.*StatusBadRequest*, gin.H{"error": err.Error()})
}
```

现在我们必须在控制器中处理上传。在`./api/controller/main.go`中创建一个名为`Upload`的函数。这个函数将接收一个文件作为参数，并将它保存在一个目的地(或者您可以在视图的主文件中使用 Gin 的`SaveUploadedFile`函数)。现在控制器的 main.go 文件应该是这样的

```
package controller

import (
   "fmt"
   "io"
   "mime/multipart"
   "os"
   "time"
)

// This is the root directory of uploaded files
var base = "/home/mehrdadep/example"

func Upload(file *multipart.FileHeader) (string, error) {
   src, err := file.Open()
   if err != nil {
      return "", err
   }
   defer src.Close()
   n := fmt.Sprintf("%d - %s", time.Now().UTC().Unix(), file.Filename)
   dst := fmt.Sprintf("%s/%s", base, n)
   out, err := os.Create(dst)
   if err != nil {
      return "", err
   }
   defer out.Close()

   _, err = io.Copy(out, src)
   return n, err
}
```

回到视图，我们可以通过调用控制器的上传函数来完成上传 API 的实现。现在你的`POST`函数的主体应该是这样的:

```
file, err := c.FormFile("file")
if err != nil {
   c.JSON(http.*StatusBadRequest*, gin.H{"error": err.Error()})
   return
}
n, err := controller.Upload(file)
if err != nil {
   c.JSON(http.*StatusInternalServerError*, gin.H{"error": err.Error()})
   return
}
c.JSON(http.*StatusOK*, gin.H{
   "success": "Uploaded successfully",
   "name": fmt.Sprintf("%s", n),
})
```

# 我们也能下载吗？

当然了。Gin 可以使用`ShouldBindUri`函数绑定`url`变量。现在让我们创建我们的下载端点。在你的视图的`main.go`文件的顶部创建一个新的自定义类型命名文件。我们将使用这个类型和 Gin 的`ShouldBindUri`从`uri`中读取一个变量。

```
type File struct {
   Name string `uri:"name" binding:"required"`
}
```

现在添加端点，使用它的名称下载文件。

```
files.GET("/:name/", func(c *gin.Context) {
   var file File
   if err := c.ShouldBindUri(&file); err != nil {
      c.JSON(http.*StatusBadRequest*, gin.H{"msg": err})
      return
   }
})
```

之后，如果`uri`绑定到文件类型，我们必须将它传递给控制器的`Download`函数来处理文件的下载。最后，我们可以使用 Gin 的数据函数提供文件。现在视图中的`GET`方法应该是这样的:

```
var f File
if err := c.ShouldBindUri(&f); err != nil {
   c.JSON(http.*StatusBadRequest*, gin.H{"error": err})
   return
}
m, cn, err := controller.Download(f.Name)
if err != nil {
   c.JSON(http.*StatusNotFound*, gin.H{"error": err})
   return
}
c.Header("Content-Disposition", "attachment; filename="+n)
c.Data(http.*StatusOK*, m, cn)
```

在控制器的主文件中创建一个名为`Download`的函数。这个函数获取一个文件名，并将它的 mime 类型和内容返回给视图。我们使用`http`包的`DetectContentType`函数来检测文件的内容类型。现在`Download`功能变为:

```
func Download(n string) (string, []byte, error) {
   dst := fmt.Sprintf("%s/%s", base, n)
   b, err := ioutil.ReadFile(dst)
   if err != nil {
      return "", nil, err
   }
   m := http.DetectContentType(b[:512])

   return m, b, nil
}
```

# **给我造点我能用的东西**

在你的模块的根文件夹中运行`go build -o ./rest-api main.go`命令，将你的模块构建到一个文件中。现在您可以使用`./rest-api`运行它。或者，您可以使用`go run main.go`运行您的项目，而不构建它。

Gin 的默认环境是调试模式。在使用`gin.SetMode(gin.ReleaseMode)`调用`view.StartServer()`功能之前，您可以将其更改为释放模式。如果你正在使用调试模式，你应该会看到类似这样的输出，这意味着你的服务器正在运行，等待你调用它的 API。

```
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)[GIN-debug] POST   /api/v1/files/            --> go-rest-example/api/view.StartServer.func1 (3 handlers)
[GIN-debug] GET    /api/v1/files/:name/      --> go-rest-example/api/view.StartServer.func2 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080
```

# **我没有涵盖的内容**

*   杜松子酒的中间产品
*   使用数据库保存文件的元数据
*   上传前的 MIME 类型检查
*   功能的单元测试