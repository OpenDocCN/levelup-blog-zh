# gRPC 网关

> 原文：<https://levelup.gitconnected.com/grpc-part-4-the-grpc-gateway-b68bc065a8ec>

![](img/d0b2fda286ab598148967bc30a561a7f.png)

图片由 Renee French 提供

## GRPC:第四部分

## 让我们怀旧一下，把 HTTP 1.x 带回未来。

本系列的这一部分依赖于第 3 部分。如果您还没有这样做，请在继续之前阅读第 3 部分。

[](/grpc-part-3-implementing-a-grpc-service-definition-cac5404c5c1b) [## 实施 gRPC 服务定义

### gRPC 构建生命周期有一个陡峭的学习曲线，但是您会看到我们可以自动完成这个过程。

levelup.gitconnected.com](/grpc-part-3-implementing-a-grpc-service-definition-cac5404c5c1b) 

# 什么是 gRPC 网关

gRPC 网关是`protoc`的一个插件，它创建一个 HTTP 代码转换服务器，将 **HTTP 转换成 gRPC** 。gRPC 网关[项目](https://github.com/grpc-ecosystem/grpc-gateway)仅适用于 Golang。除了代码转换，gRPC 网关插件还可以为您的 gRPC 服务定义生成 Swagger 文档。

gRPC 网关服务器将允许客户端通过 HTTP 连接到您的 gRPC 服务。

# 安装 gRPC 网关

为了开始使用 **grpc-gateway** ，我们需要在`proto`项目中做一些工作。

## 1.用新文件`tools.go`创建一个名为`tools`的新目录。

```
$ mkdir $GOPATH/src/proto/tools
```

## 2.向`tools.go`添加内容

如果我们能够正确地管理我们的依赖关系，安装 gRPC 网关是最容易的。为此，将以下内容添加到`tools.go`。

```
$ touch $GOPATH/src/proto/tools/tools.go
```

## 3.安装 gRPC 网关插件

首先，整理依赖关系

```
$ go mod tidy
```

然后，从`proto`目录安装依赖项。

```
$ go install \
    github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway \
    github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2 \
    google.golang.org/protobuf/cmd/protoc-gen-go \
    google.golang.org/grpc/cmd/protoc-gen-go-grpc
```

## 配置 gRPC 网关选项

服务定义可以用 gRPC 网关注释进行注释。这将指导 gRPC 网关插件如何创建网关服务器 HTTP 端点。

## 1.添加 googleapis 原型文件

gRPC Gateway 需要托管在 googleapis 存储库中的一些原型文件。不幸的是，我们必须手动将这些原型文件添加到我们的项目中。

添加目录`third_party/google/api`和以下文件

*   [http.proto](https://gist.github.com/hyperstripe50/3b7e6722790d7af6dcde50cc2b6644fe)
*   [annotations.proto](https://gist.github.com/hyperstripe50/c7bb051c0bcc6266ae130d2c5a107ad4)

```
$ mkdir -p $GOPATH/src/proto/third_party/google/api
$ touch $GOPATH/src/proto/third_party/google/api/http.proto
$ touch $GOPATH/src/proto/third_party/google/api/annotations.proto
```

您的`proto`目录现在应该看起来像这样

## 5.添加一个`google.api.http`注释

将`salt_hash.proto`的内容更改如下。通过这样做，您添加了自定义的 **google.api.http** 注释，该注释将把 http 代码转换成 gRPC。

*   (1)添加自定义 **google.api.http** HTTP 到 gRPC 转码细节。
*   (2)声明该 gRPC 方法代码转换为 HTTP POST 方法和路径 **/v1/credentials** 。
*   (3)声明 HTTP 请求的主体模式。
*   (4)具有内插的`{value}`参数的删除方法。

添加注释后，您可以使用 gRPC 网关插件生成 HTTP 网关代码。

## 6.调整 Makefile 以使用 gRPC 网关插件

我们需要对 Makefile 进行两处修改。首先，我们将编译 **annotations.proto 和 http.proto** 文件。其次，我们将使用 gRPC 网关插件和**协议**为我们的 **salt_hash** 服务定义创建 HTTP 代码转换的服务器代码。

## 7.构建 gRPC 网关

使用 Makefile 生成 gRPC 网关代码转换服务器代码。

```
$ make compile
```

# 在 salt-hash-svc 项目中公开 gRPC 网关

从在`proto`目录下工作切换到在`salt-hash-svc`目录下工作。如果我们使用 Go 模块，管理我们的依赖项和版本是最容易的，所以我们接下来将从`salt-hash-svc`项目中创建一个 Go 模块。

## 1.从`salt-hash-svc`目录，初始化模块

```
$ go mod init salt-hash-svc
```

## 2.在`go.mod`文件中配置一个`replacement`。

```
replace proto => ../proto
```

## 3.整理依赖版本

```
$ go mod tidy
```

**4。安装 grpc-gateway 运行时**

```
$ go get github.com/grpc-ecosystem/grpc-gateway/v2/runtime@v2.7.1
```

# 引导并运行 gRPC 网关服务器

gRPC 网关插件在我们的`proto`项目中为我们生成了服务器代码。文件`salt_hash.pb.gw.go`已创建。

## 1.创建网关入口点

从`salt-hash-svc`项目中，通过添加`gw/main.go`文件创建以下目录结构。

```
$ mkdir $GOPATH/src/salt-hash-svc/datastore/gw
$ touch $GOPATH/src/salt-hash-svc/datastore/gw/main.go
```

## 2.向`gw/main.go`文件添加代码以创建网关入口点。

## 3.在 Makefile 中包含 gRPC 网关步骤

将 gRPC 网关构建和运行命令添加到 makefile 中

*   (1)构建 gRPC 网关服务器。
*   (2)创建一个命令来运行我们的网关服务器和 gRPC 服务器。
*   (3)运行网关服务器的逻辑。

## 3.构建并运行网关示例

使用 Makefile 来引导 gRPC 服务器和网关服务器。网关服务器必须通过 URL 进行配置，以便与 gRPC 服务器连接。

```
$ make
$ make credentials_gw_ex
```

这将启动`localhost:8081`上的 HTTP 网关。

# 与 gRPC 网关服务器交互

gRPC 网关服务器已被配置为 HTTP 1.x 反向代理，它将传入的消息代码转换为 gRPC，并将它们转发到我们的 gRPC 服务器。来自 gRPC 服务器的响应被从 gRPC 转换成 HTTP 1.x。让我们通过 HTTP 1.x 网关服务器与 gRPC 服务器进行交互，来看看这在实践中是如何工作的。

## 1.创建用户

```
$ curl -X POST [http://localhost:8081/v1/credentials](http://localhost:8081/v1/credentials) -d '{ "username": "new-user", "password": "password"}'
```

## 2.删除用户

```
$ curl -X DELETE [http://localhost:8081/v1/credentials](http://localhost:8081/v1/credentials)/new-user
```

# 摘要

*   到目前为止，对于一个人来说，以文本消息的形式与 HTTP 1.x 服务交互肯定比 gRPC 更方便。幸运的是，gRPC 网关可以自动为我们生成一个代码转换器服务器，它所需要的只是一些注释。
*   gRPC 网关充当反向代理，必须作为独立于 gRPC 服务器的进程运行。gRPC 网关通过指定 gRPC 端点坐标将请求转发到 gRPC 服务器。