# 去释放吧！

> 原文：<https://levelup.gitconnected.com/go-go-release-3f326921bc71>

![](img/fb1c5e1e3c380d79903c12f03e482a8b.png)

# TLDR；只是想要一个 Go 发布工具？

使用下面的键跳到 ***。***

# 全文阅读

说到围棋和它的生态系统，我既不是爱好者也不是憎恨者。总的来说，一切都有点简陋，但是非常开放和容易理解。你可能不得不自己做一些工作，但你将能够得到结果。

# 发布通用软件

无论使用何种语言或工具，都有一些简单的关于软件版本和发布的最佳实践:

*   使用[语义版本号](https://en.wikipedia.org/wiki/Software_versioning)表示版本。
*   确保发布版本号持续地标记在源代码和工件上。

做到这两点，你就得到一个有序的、可识别的、可复制的产品。

# 在 Go 中发布软件

那么，Go 对新版本有什么期待呢？如果你认为*完全是最小的最佳实践*，那你就对了:-)对于 Go，你需要使用[语义版本号](https://pkg.go.dev/golang.org/x/mod/semver?tab=doc)，并标记你的源代码。这样做，你的东西就会在 Go 模块系统中正常工作。

# 在 Go 中使用版本

在 Go 中，释放机制是[模块](https://blog.golang.org/using-go-modules)系统的一部分。让我们看看模块和版本的一些基本操作:

```
# What releases are associated with the current project?
**go list -m all**
github.com/nwillc/snipgo
github.com/DATA-DOG/go-sqlmock v1.3.3
github.com/alcortesm/tgz v0.0.0-20161220082320-9c5fe88206d7
github.com/anmitsu/go-shlex v0.0.0-20161002113705-648efa622239
github.com/armon/go-socks5 v0.0.0-20160902184237-e75332964ef5
github.com/atotto/clipboard v0.1.2
...
# What versions are available for a specific module?
**go list -m -versions github.com/nwillc/gorelease**
github.com/nwillc/gorelease v0.1.1 v0.1.2 v0.2.0 v0.3.0 v0.4.0
# Getting a module
**go get github.com/nwillc/gorelease**
# Getting a module with a specified version
**go get github.com/nwillc/gorelease@v0.4.0**
```

# 管理 Go 中的模块

在 Go 中管理模块非常简单，请阅读上面的链接或直接查找。但最少如下。首先，您将启动模块支持。让我们假设我们将把这个项目保存在 *GitHub* 中，并且库将是 *nwillc/demo* :

```
go mod init github.com/nwillc/demo
```

这会将模块管道添加到您的当前项目中:

```
ls -1 go.*
go.mod
go.sum
```

有了这些，您可以根据需要向项目中添加模块，例如:

```
go get golang.org/x/mod/semver
```

并将该模块引用为代码中的*导入*。

# 所以是时候发布你的代码了

你是做什么的？

*   提交所有代码
*   设置一个合适的 git 标签，比如 v0.1.0
*   将标签和代码推送到 github

这就够了。这些步骤可能如下所示:

```
# Make sure the current code is all checked in
git commit -am 'Ready for release v0.1.0'
# Now tag it
git tag v0.1.0
# Push the tag
git push origin v0.1.0
# Push the code 
git push
```

几个样板步骤，它做的工作，但缺乏一些优雅。请继续阅读。

# Go 中的一个发布助手

我想简化上面的步骤，并实现一个额外的东西，我想在包含字符串标记的代码中有一个可用的常量。对于第二个目标，有不同的方法，一个流行的方法是使用编译时标志 *-ldflags -X* ，但是我希望它成为代码的一部分，因为我希望所有标准的构建方法，从源代码开始，做正确的事情。

为了这些目的，我写了一个 Go 程序， [gorelease](https://github.com/nwillc/gorelease) ，来自动化这个过程。我在 Go 中写这篇文章是基于这样的假设:如果你在 Go 中工作，你的环境将被设置为运行 Go。

## 使用 gorelease

要使用*gore release*，你需要一份承诺回购协议，并具备以下条件:

```
# Get gorelease - only need to do this once
go get github.com/nwillc/gorelease
# Indicate the version to release...
echo v0.1.0 > .version
# Run gorelease
gorelease
```

这将生成一个包含版本的`version.go`文件，标记 repo，并推送标记和 repo。

要了解更多关于 *gorelease* 的信息，请前往 [github](https://github.com/nwillc/gorelease) 。

## 注意:v2

Go 的模块版本控制在 v2.0.0+ 上来了一个[急转弯，进入了一片疯狂的土地，要么研究它，要么保持低于那个版本号。在 gorelease 中还没有解决*这个问题，但是很快就会增加支持。*](https://blog.golang.org/v2-go-modules)