# 实施 gRPC 服务定义

> 原文：<https://levelup.gitconnected.com/grpc-part-3-implementing-a-grpc-service-definition-cac5404c5c1b>

![](img/2798e23e1ed9f896d68670f2f53a349d.png)

图片由 Renee French 提供

## GRPC:第三部分

## gRPC 构建生命周期有一个陡峭的学习曲线，但是您会看到我们可以自动完成这个过程。

本系列的这一部分依赖于第 2 部分。如果您还没有这样做，请在继续之前阅读第 2 部分。

[](/grpc-part-2-declaring-a-grpc-service-definition-df9ae0847e4c) [## 声明 gRPC 服务定义

### 服务定义是 gRPC 开发的基础部分，所以让我们从正确的方面开始。

levelup.gitconnected.com](/grpc-part-2-declaring-a-grpc-service-definition-df9ae0847e4c) 

第 2 部分中创建的服务定义为我们提供了实现业务逻辑的机会。在第 3 部分结束时，您将能够运行一个管理凭证的 gRPC 服务器和一个操作凭证管理服务的 gRPC 客户机。

# 实现 gRPC 客户端和服务器

**1。创建一个新项目和一些文件** 首先在与你的`proto`项目相同的层次上创建以下目录结构，名为`salt-hash-svc`，例如`$GOPATH/src/salt-hash-svc`。

```
$ mkdir -p $GOPATH/src/salt-hash-svc $GOPATH/src/salt-hash-svc/datastore/examples $GOPATH/src/salt-hash-svc/datastore/grpc
```

**2。实现 gRPC 服务器接口** `protoc`编译器为我们创建了一个接口来实现 gRPC 服务器。将下面的代码添加到`salt_hash_datastore_svc.go`中。

```
$ touch $GOPATH/src/salt-hash-svc/datastore/grpc/salt_hash_datastore_svc.go
```

**3。添加业务逻辑** 我们的 Salt 哈希服务应该允许客户端安全地存储凭证。salt-hashed 密码是一种单向散列协议。存储的密码只是原始密码的一种表示，可以安全地存储在数据库中。实际的方法是 SHA256 散列一个随机字符串，称为 salt，具有标准格式的原始密码，例如`SHA256(<salt>.<password>)`。现在让我们在 gRPC 服务器上实现这个逻辑。

将以下代码添加到`salt_hash_datastore_svc.go`。

**4。使用 gRPC 客户端**

生成的 gRPC 代码提供了一个现成的客户端。这对 gRPC 来说是一个很大的优势，因为它提高了一致性，减少了开发时间，并且紧密耦合了客户机和服务器。

```
$ touch $GOPATH/src/salt-hash-svc/datastore/examples/manage_client_credentials.go
```

将以下代码添加到`manage_credentials.go`

**5。创建 gRPC 服务器入口点** 一旦在`salt_hash_datastore_svc.go`、中实现了 gRPC 服务器，您就可以引导它在`manage_credentials.go`中创建一个入口点供您的客户端与之交互。

```
$ touch $GOPATH/src/salt-hash-svc/datastore/grpc/main.go
```

将以下代码添加到`main.go`

6。创建一个 Go 模块
创建一个 Go 模块，这样我们就可以构建这个项目并引用我们的本地 *proto* 文件。

```
$ go mod init salt-hash-svc
```

这个命令创建了一个`go.mod`文件，我们应该调整它来引用本地 *proto* 目录。编辑文件以匹配以下内容。

```
module salt-hash-svcgo 1.16replace proto => ../proto
```

整洁的

```
$ go mod tidy
```

# 运行 gRPC 客户端和服务器

测试您的更改的循环过程是构建客户机和服务器二进制文件，然后运行它们。我们将使用谚语*“如果某件事情值得做一次，那么就值得建立一个工具来做它。”，*又来了。

1.  **创建 Makefile** 这个 make 文件负责构建我们的客户机和服务器二进制文件，并运行它们。

```
$ touch $GOPATH/src/salt-hash-svc/Makefile
```

将以下代码添加到`Makefile`

**2。构建客户端和服务器** 使用 Makefile 构建客户端和服务器二进制文件。

```
$ make
```

您的目录现在应该具有以下结构。

**3。运行客户端和服务器** 使用 Makefile 运行客户端和服务器。检查输出以进行验证。

```
$ make manage_credentials_ex
```

输出:

*   (1)是表明凭据已添加到数据存储区的客户端日志。
*   (2)是表明凭据已从数据存储区中删除的客户端日志。

# 摘要

*   gRPC 服务定义用编译器`protoc`和语言插件编译成目标语言。
*   编译 gRPC 服务定义是一项频繁的任务，因此最好提供简单的工具来降低复杂性。
*   编译的 gRPC 服务定义代码产生一个服务器接口，必须实现该接口才能提供任何业务逻辑。
*   应该引导并启动实现的 gRPC 服务器，以便客户端与之通信。
*   编译后的 gRPC 服务定义代码会生成一个客户端接口，该接口应被配置为与实现的引导服务器进行通信。

# 延伸阅读…

本文是四篇系列文章中的第三篇，它将带您步入 gRPC 的正轨。点击下面的链接查看本系列的下一篇文章。

[](/grpc-part-4-the-grpc-gateway-b68bc065a8ec) [## gRPC 网关

### 让我们怀旧一下，把 HTTP 1.x 带回未来。

levelup.gitconnected.com](/grpc-part-4-the-grpc-gateway-b68bc065a8ec)