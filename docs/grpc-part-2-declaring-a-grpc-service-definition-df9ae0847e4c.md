# 声明 gRPC 服务定义

> 原文：<https://levelup.gitconnected.com/grpc-part-2-declaring-a-grpc-service-definition-df9ae0847e4c>

![](img/de22580be509bd0b258d9c7c2f928879.png)

图片由 Renee French 提供

## GRPC:第二部分

## 服务定义是 gRPC 开发的基础部分，所以让我们从正确的方面开始。

对于 gRPC 的简要介绍，请在继续之前检查第 1 部分，尽管这不是继续的必要条件。

[](/grpc-part-1-a-case-for-grpc-69592155cfbd) [## gRPC 的一个案例

### gRPC 将继续存在，这就是原因。

levelup.gitconnected.com](/grpc-part-1-a-case-for-grpc-69592155cfbd) 

# 服务定义

gRPC 基于定义服务和客户可以用来操作服务的方法的思想。gRPC 的*服务定义*使用*协议缓冲区*作为其接口定义语言来描述服务。在本系列中，我们将创建一个有用的服务来安全地存储用户名和密码。在我们开始尝试这样做的逻辑之前，我们必须创建我们的服务定义。这被称为接口优先编程，是 gRPC 所要求的。

# 开始之前

**1。protocol buffer compiler protocol** protocol 是一个编译工具，可以将服务定义编译成目标语言的客户机和服务器代码。您可以将相同的服务定义编译到 Node 或 Java 或 Golang 或任何其他具有协议插件的流行语言中。这就是 gRPC polygot 的特点。

您可以通过[下载](https://github.com/protocolbuffers/protobuf/releases)并将文件解压到您的路径中来手动安装 protobuf 二进制文件`protoc`。

或者，您可以使用**自制软件**来安装 protobuf 二进制文件`protoc`。

```
$ brew install protobuf@3.17
```

**2。安装 golang 插件**

```
$ go get -u google.golang.org/grpc@v1.40.0
$ go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.27.1
$ go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.1.0
```

# 关于目录结构的一个注记

gRPC 服务定义与消息传递协议和客户端代码紧密结合。我们可以依靠 gRPC 的这个构造来显著地改进向客户分发服务定义的过程。在实践中，我发现维护服务定义的整体项目是有效的。

**1。
新建一个目录**创建下面的目录结构和`salt_hash.proto`文件。随着你作为服务提供商的成长，你会发现在这个项目中添加其他服务是有价值的，因为客户已经熟悉了导入。

`proto`目录应该位于`$GOPATH/src/proto`。

整体服务定义项目的初始目录

用下面的命令创建上面的目录结构。

```
$ mkdir -p $GOPATH/src/proto/security/salt_hash/datastore/v1
```

**2。创建 SaltHash 服务定义** 通过添加以下代码在`salt_hash.proto`内创建一个空服务定义。

```
$ touch $GOPATH/src/proto/security/salt_hash/datastore/v1/salt_hash.proto
```

*   `syntax`字段(1)声明该服务定义使用协议缓冲版本`proto3`。
*   `go_package`字段(2)向代码生成工具提供关于生成的代码应该放在哪里的指令。
*   `package`字段(3)防止协议消息类型之间的名称冲突，并用于生成代码。
*   (5)是服务定义接口。现在这是空的，但是很快将描述客户可以用来操作这个服务的方法。

**3。将 RPC 方法添加到服务定义** 将`Credentials`类型和方法添加到`salt_hash.proto`文件。

*   import 语句(1)导入 google 提供的一组常见类型，例如 StringValue 和 BoolValue。避免在这里重新创建轮子是很方便的。
*   (2)向数据存储区添加凭据的远程方法。
*   (3)从数据存储中删除凭证的远程方法。
*   (4)消息类型凭证的定义。
*   (5)保存用户名值的字段。
*   (6)保存密码值的字段。

一旦服务定义完成，您就可以自动生成 gRPC 代码，并为其实现客户机和服务器。

# 汤姆·范·弗利克的一课

> 如果某件事值得做一次，那就值得打造一个工具去做。汤姆·范·弗利克

…并且您将大量编译服务定义，所以让我们创建一个 Makefile 来尊重我们未来的自己。

**1。在下面的位置创建一个 Makefile 文件。**

```
$ touch $GOPATH/src/proto/Makefile
```

**2。将以下内容添加到 Makefile 中。**

# 编译服务定义

编译服务定义将允许您实现服务的业务逻辑。

**从** `**proto**` **目录运行下面的**

```
$ make compile
```

如果没有出错，那么您将拥有以下目录结构和文件。

# 创建 Go 模块

从`proto`项目中创建一个 Go 模块，以便于客户端导入。

```
$ go mod init proto
$ go mod tidy
```

# 摘要

*   gRPC 及其固有的接口优先需求可以用来改进向客户分发代码的过程。
*   gRPC 使用协议缓冲区作为其接口定义语言来编写服务定义。
*   通过使用`protoc`二进制和语言插件，服务定义被编译成目标语言的文件。
*   必须先创建和编译服务定义，然后才能实现它们。

# 延伸阅读…

本文是四篇系列文章中的第二篇，将带您步入 gRPC 的正轨。点击下面的链接查看本系列的下一篇文章。

[](/grpc-part-3-implementing-a-grpc-service-definition-cac5404c5c1b) [## 实施 gRPC 服务定义

### gRPC 构建生命周期有一个陡峭的学习曲线，但是您会看到我们可以自动完成这个过程。

levelup.gitconnected.com](/grpc-part-3-implementing-a-grpc-service-definition-cac5404c5c1b)