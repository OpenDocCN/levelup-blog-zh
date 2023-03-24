# 在 Go 中运行和构建程序的快速参考

> 原文：<https://levelup.gitconnected.com/a-quick-reference-to-running-building-programs-in-go-efa27fc0e87b>

再加上一些小技巧和窍门！

![](img/105ec5c5d6e50493ca1f6fbc27497449.png)

众所周知，Go 是一种全面而简洁的语言。它的客户端界面通过保持可能的命令简单而强大来保持这种范式。为了成为一名高效的 Go 开发者，这篇文章可以作为 Go 客户端常用命令的参考。

要覆盖的命令:

*   `run`、`build`和`install`命令
*   包括**构建标志**
*   如何构建**共享库**
*   检测**竞争条件**

**注意**:您可以随时使用`go help`来获得进一步的知识，尽管输出有时可能有点过于冗长。这篇文章试图解决这个问题！

让我们开始吧。

## 如何使用 Go 运行、构建和安装

Go `run`是一个高效的命令，因为运行程序不需要工件。Go 只是将程序编译成二进制文件，直接在内存中运行。只要正确定义了 Go 路径和环境变量，就可以从系统的任何地方调用`go run`。

虽然在 Go 项目中您总是需要一个`main`包，但是您可以使用`go run`执行任何文件。

**Go 运行示例:**

```
$ go run main.go # ran from within same file location
Hello World!
$ go run ../some/file/path/hello-world.go # ran from outside file location# you can run multiple go files at once
$ go run *.go # run all go files in current directory
$ go run ../dir/**/*.go # run all go files in all directories in dir
$ go run first.go second.go third.go $ run go files selectively
```

如果你想创建一个可执行文件，你可以使用`go build`命令。

**去构建例子:**

```
$ pwd
some/path/
$ go build # build all go files in current directory
$ ls
hello.go path # if given no argument, build creates executable with
              # name of current directory.
$ ./path
Hello World!# the -o flag will direct the output to a new subdirectory bin
$ go build -o bin/hello
$ ./bin/hello
Hello World!
```

Go `install`的工作方式与 go`build`命令相同，只是它将可执行文件放在位于`$GOPATH`的子目录`bin`中。如果你想知道你的`$GOPATH`是什么，运行`go env GOPATH`。一旦您成功地为一个给定的项目执行了`go run`，您就可以从任何地方运行生成的可执行文件，只要`bin`子目录也被添加到您的标准`PATH`环境变量中。

**去安装例子:**

```
$ pwd # example for Mac
/path/to/hello
$ go install
$ cd ../../
$ hello # you can now run hello as a command from anywhere!
```

## 包括构建标志

在大多数情况下，您只需使用 go `run`、`build`或`install`即可。如果您需要额外的控制，您可以包括多个标志来指定客户端执行。

*   `-a`标志强制重新构建包——如果您需要用新的或旧的包重新同步项目结构，这可能会有所帮助。
*   `-n`标志将打印出将为某个调用执行的命令，但并不实际执行它们。举个例子，你可以运行`go install -n`，你会看到 go 会创建新的目录，编译源文件并链接它们等等。但不会执行这些操作。
*   `-x`标志类似于`-n`标志，除了它仍然会在打印命令时执行命令。您可以将它包含在构建管道中，用于额外的调试/日志记录实用程序。
*   `-p`标志允许您控制可以并行运行的构建命令的数量。默认情况下，go 将此设置为机器上可用的 CPU 数量，但是如果需要，您可以更改此行为。
*   `-race`标志标识程序中的竞争条件。这非常有用，我们将在后面进行探讨。
*   `-v`标志在编译时打印软件包的名称——这对调试或日志记录非常有帮助。

还有很多标志，但这些是一些更常见的例子，作为一名开发人员，这些例子会给你带来即时的灵活性。

## 构建共享库

通常，Go 编译器通过将所有依赖项和库资源合并到一个文件中来使用静态链接。Go 允许动态链接和共享库，这可以使项目结构更加灵活，因为更新一个文件将自动反映在其他共享文件中。不仅如此，您还可以显著减小生成的可执行文件的大小。

使用`-linkshared`和`-buildmode`标志，您也可以这样做。第一步是运行以下命令，允许所有标准库可共享。

```
$ go install -buildmode=shared -linkshared std
$ go install -buildmode=shared -linkshared userownpackage 
```

当您准备好共享您的程序时，只需运行命令:

```
$ go build -linkshared yourprogram 
```

这可以将最终的程序大小从几兆字节降低到几千字节！

## 检测竞争条件

Go 是众所周知的，因为它内置了 go 例程的并发性。还有一个额外的好处，go 有一个`-race`标志，专门用来检查你的代码是否存在共享资源的竞争情况，这可能会导致棘手的编程错误。以下面的程序为例:

这段代码看起来很简单，但实际上并没有在 go 例程中保护`i`的值。我们可以通过该程序执行中包含的`-race`标志来验证资源安全性的缺乏:

```
$ go run -race example.go
==================
WARNING: DATA RACE
Read at 0x00c000136010 by goroutine 7:
  main.main.func1()
      /Users/israelmiles/Sandbox/threads.go:13 +0x3cPrevious write at 0x00c000136010 by main goroutine:
  main.main()
      /Users/israelmiles/Sandbox/threads.go:10 +0x108Goroutine 7 (running) created at:
  main.main()
      /Users/israelmiles/Sandbox/threads.go:12 +0xe4
==================
==================
WARNING: DATA RACE
Read at 0x00c000136010 by goroutine 8:
  main.main.func1()
      /Users/israelmiles/Sandbox/threads.go:13 +0x3cPrevious write at 0x00c000136010 by main goroutine:
  main.main()
      /Users/israelmiles/Sandbox/threads.go:10 +0x108Goroutine 8 (running) created at:
  main.main()
      /Users/israelmiles/Sandbox/threads.go:12 +0xe4
==================
2
3
4
4
5
6
7
8
9
9
Found 2 data race(s)
exit status 66
```

因此，在任何涉及货币的程序中包含`-race`标志绝对是一个好主意，它可以作为一个基线，并在竞争条件出现时提醒您。您应该*而不是*使用`-race`标志的唯一情况是，如果您正在部署到生产环境或者想要获得某种形式的基线性能测试指标。

虽然这不是 Go 中程序管理技术的完整列表，但它应该是提高开发人员能力的良好开端。Go 保持了许多简单而强大的约定，比如它的标志允许满足竞争条件，创建共享库或输出对日志/调试有用的信息。总会有更多的东西需要学习，所以如果有你想添加到这篇文章中的主题，我鼓励你在下面留下评论！感谢阅读。