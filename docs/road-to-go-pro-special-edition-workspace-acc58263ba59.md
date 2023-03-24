# Road to Go Pro 特别版:工作空间

> 原文：<https://levelup.gitconnected.com/road-to-go-pro-special-edition-workspace-acc58263ba59>

*在我们开始之前，你可以在这个* [*资源库*](https://github.com/songx23/road-to-go-pro-example) *中找到本教程使用的代码。你可以在这里* *找到 Road to Go Pro* [*的全部内容。如果你错过了最后一个，你可以通过这个*](https://medium.com/@songx/road-to-go-pro-f9d1f8a51fad) [*链接*](/road-to-go-pro-special-edition-fuzzing-e1630dd58ade) *找到它。*

欢迎回到专业版之路。我开始这个新的 Go stories 分支是因为 Go 1.18 终于发布了。这个版本带来了一些令人兴奋的新功能，我迫不及待地想与你分享。在这个故事中，我们将回顾 Go 1.18 中最后一个主要的新功能，即**“工作区”**。

# 工作空间

![](img/5fb26820f624d0c0455e751e173de913.png)

由[奥斯汀·迪斯特尔](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Workspace 是一个专门为在一个存储库中开发多个 Go 模块的需求而设计的特性。

在 1.18 之前，如果想把多个 Go 模块放到一个库中，只能操作与`go.mod`文件同级的模块。如果您想要构建和运行您的模块，您必须在不同的模块文件夹之间跳转。那个开发体验有点烦，有点繁琐。

1.18 将 workspace 引入到 Go 生态系统中，因此我们可以在一个地方(存储库)轻松开发/运行多个 Go 模块。

现在，让我们快速浏览一下工作区，并了解如何使用这个特性。

## 创建模块

首先，让我们创建一个名为`workspace-example`的文件夹。然后，我们将在这个文件夹中创建两个 Go 模块。所需的文件夹结构看起来像下面的文件树。

```
❯ tree -d
.
├── equation
└── sample
```

在这两个模块之间，我们将首先开发`equation`模块。正如你已经知道的，我们需要运行`go mod init`命令来初始化 Go 模块。这是一个初始化`equation`包后的样本`go.mod`文件，你可以使用不同的模块名。

方程式模块的 go.mod 示例

在本模块中，我们将开发一些简单的数学计算函数。由于我不是解数学方程的专家，我选择了最简单的方程开始(线性方程:`ax+b=0`)。

要添加到方程模块中的函数

当然，下一步是添加这个函数的单元测试。我已经在这里提供了样品测试代码[。当我们在`equation`文件夹中时，我们可以通过使用`go test ./...`命令轻松地运行测试。请参见下面终端中的示例输出:](https://github.com/songx23/road-to-go-pro-example/blob/main/equation/equation_test.go#L5-L47)

```
❯ go test ./...
ok  go tgithub.com/songx23/road-to-go-pro-example/equation    0.424s
```

然而，如果我们转到存储库的根级别并尝试运行测试(`go test ./equation/...`)，我们将得到下面的错误消息:

```
❯ go test ./equation/...
pattern ./equation/...: directory prefix equation does not contain main module or its selected dependencies
```

我们必须与`go.mod`文件处于同一级别，才能执行通常的 Go 操作。现在，让我们初始化一个工作区，看看它如何改变事情。

## 创建工作区

创建工作区与创建模块非常相似。我们运行的命令是库的根级别的`go work init`。该命令创建一个带有 go version 指令的`go.work`文件(例如`go 1.18`)。回到我们在尝试执行单元测试时遇到的障碍，我们现在可以通过在我们的工作空间中包含`equation`模块来解决它。这样做的命令看起来非常直观:`go work use ./equation`。我们应该看到一个新的`use`指令被添加到了`go.work`文件中。

```
use (
   ./equation
)
```

好了，让我们尝试在存储库的根级别再次运行测试。这一次，多亏了 Go 工作区，测试应该成功运行，没有任何问题。

## 创建另一个模块

我们已经完成了`equation`包的开发，是时候开始其他模块的开发了。像往常一样，我们首先初始化 Go 模块。这里是`sample`模块的另一个示例`go.mod`文件。

示例模块的 Sample go.mod

在示例模块中，我们将使用`equation`模块求解线性方程。要添加`equation`模块作为依赖项，我们可以运行`go get <equation_module_name>`。这可以在没有工作空间设置的情况下工作，因为 Go 可以根据模块名(模块的 URL)解析依赖关系。

使用方程式模块的主函数示例

之后，我们可以在主函数中使用`SolveLinear`函数。

## 解决依赖关系

如果我们想在`equation`模块中添加更多的求解函数，我们需要提交新的更改，将它们推送到主分支，然后更新`sample`模块中的模块。这不是一个短暂的过程。如果我们在`equation`模块中发现一个 bug，我们需要重复这个过程来修补它。

有了工作区设置，我们可以通过运行`go work use ./sample`将`sample`模块添加到我们的工作区。当开发`sample`模块时，Go 检测到`equation`模块在工作区内，因此，它使用模块的本地版本，而不是从远程存储库中获取它。换句话说，当我们向`equation`包中添加新函数时，我们可以在不实际推送代码的情况下访问它。这简化了上面提到的过程，并节省了在两个模块之间来回切换的大量时间。

# 下一步是什么？

![](img/51aa9da57dd4c0cccd66fe97e98d5372.png)

在这个故事中，我们看了新的**工作空间**特性。这是在一个存储库中开发多个模块的简便方法。我还没有机会在任何专业项目中使用这个特性，但是我很想听听你对这个新特性的看法。**欢迎在下方留言评论。**这篇文章涵盖了 Go 1.18 的新功能。

**感谢您的阅读！**

如果你想支持我，你可以通过这个推荐链接成为一个中等会员。谢谢你。

[](https://songx.medium.com/membership) [## 用我的推荐链接加入 Medium 宋 x。

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

songx.medium.com](https://songx.medium.com/membership)