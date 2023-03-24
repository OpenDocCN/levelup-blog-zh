# 运行 vs 构建 vs 安装

> 原文：<https://levelup.gitconnected.com/go-run-vs-go-build-vs-go-install-c7c0fd135cf9>

## 权威指南

## 你有没有想过构建、运行和安装有什么不同？三个几乎相似的 go 命令。

![](img/4fcb6c092346a0233e3832f0d49e6b5c.png)

[https://github.com/MariaLetta/free-gophers-pack/](https://github.com/MariaLetta/free-gophers-pack/)

Go 有三种类似命令的变体:运行、构建和安装。我们将在这个故事中看到每个命令的目的是什么以及如何使用它们。

这些命令要么接受一个 Go 文件，要么接受一系列 Go 文件。在这个故事中，我们将使用一个 Go hello world 程序作为例子，在整个过程中引用。

```
// file name : hello.go
package mainimport "fmt"func main(){

     fmt.Println("Hello, world!")}
```

# `**go run**`

如果您在终端/命令提示符下运行命令`go run hello.go`,您应该会看到输出显示为`Hello, world!`。

当您运行这个命令时，Go 会将代码编译成一个二进制文件，但是将这个文件放在一个临时目录中，并从那里执行这个二进制文件，并在您的程序完成后删除它。该命令对于在应用程序的初始开发阶段测试小程序非常有用。

# **去构建**

所以现在你想让你的应用程序使用你构建的二进制文件，或者在远程计算机上运行这个二进制文件，那么你需要使用下面的`go build`命令，在当前目录下创建一个名为`hello`的二进制文件(在 windows 中是 hello.exe)

```
go build hello.go
```

提示:如果您想更改生成的二进制文件的名称，请使用 file -o

```
// This results a binary file "application"
go build -o application hello.go 
```

# `**go install**`

该命令确实执行与`go build`完全相同的操作，但是将二进制文件放在$GOPATH/bin `目录中，与通过`go get`安装的第三方工具的二进制文件放在一起。现在，如果您运行`$GOPATH/bin/hello`，您将看到`Hello, world!`打印在您的终端上。

使用`go install`的建议是，如果您计划编写想要在自己的计算机上使用的工具，请使用它；如果您要在其他地方运行这个应用程序，请使用`go build`