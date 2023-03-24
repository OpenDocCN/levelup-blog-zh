# Go 1.17 简短采用说明

> 原文：<https://levelup.gitconnected.com/go-1-17-short-adoption-notes-14e09504f277>

![](img/7078ad4a28875a60a0bfcae2a3a09db1.png)

[Go 1.17](https://go.dev/blog/go1.17) 现已上市。如果我们假设遵守[语义版本化](https://semver.org/)规则，发布版本应该是*一个以向后兼容方式添加功能的版本*。总体来说，我对 Go 升级有积极的体验，1.17 进展顺利。我确实在工具中发现了至少一个向后兼容性问题，我将对此进行讨论。

## 获得释放

对于我想维护多个版本和支持多个平台的包管理，我是 asdf 的忠实粉丝。Go 1.17 版本立即可以通过 asdf golang 插件[获得，并且运行完美。有了 *asdf* 东西*就行*所以我不打算讨论任何替代方案。](https://github.com/kennyp/asdf-golang)

## 迁移项目

[语言发布说明](https://golang.org/doc/go1.17#language)涵盖了迁移项目。它太简单了，你可能会错过:

```
$ go mod tidy -go=1.17
```

就是这样。在几个不同的项目中完美地为我工作。

## Github 操作

我使用 [setup-go](https://github.com/actions/setup-go) Github 动作。在这里，1.17 也是现成的。

## 可以说，一个突破性的工具变化

在 1.17 中，有一件事发生了变化，可以说是一个突破性的变化，那就是如何安装一个包含可执行文件的模块。我们将以 [goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports) 为例。以前，安装将通过 *go 获得*，如下所示:

```
$ go get -u golang.org/x/tools/cmd/goimports
```

用 1.17 代替，你将使用*去安装*如下:

```
$ go install golang.org/x/tools/cmd/goimports@latest
```

这很好，并且有注释和警告来指导你。务必注意上面的*最新*版本，在*安装*上指定版本允许该单个命令在模块之前未通过 *get 时工作。*

虽然这不是一个制动语言特性，但它是一个突破性的工具变化，因为如果您使用像 goimports 这样的东西，如果不修复您的工具，您将无法在 1.17 和以前的版本之间来回切换。