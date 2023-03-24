# 如何使用 Pyenv 管理机器上的多个 Python 版本

> 原文：<https://levelup.gitconnected.com/how-to-manage-multiple-versions-of-python-on-your-machine-with-pyenv-956c365f41db>

![](img/ca210d8a914e6fbe1481cb45da63202e.png)

由[约书亚·雷德科普](https://unsplash.com/@joshuaryanphoto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你用 Python 编程，你可能会遇到在你的机器上管理多种语言版本的挑战。如果您需要在需要不同版本 Python 的不同项目上工作，这可能是一个问题，因为您需要在不同版本之间来回切换，而不会破坏您的环境。

这个问题的一个很好的解决方案是使用 *pyenv* 。在这篇博文中，我将展示如何使用 pyenv 来管理机器上的多个 Python 版本，而不会给环境带来任何问题。

# pyenv 是什么，我如何安装它？

Pyenv 是一个命令行工具，允许您轻松管理机器上的多个 Python 版本。它为安装、卸载和在不同版本的 Python 之间切换提供了一个简单的界面。

要使用 pyenv，首先需要在您的机器上安装它。您可以使用以下命令来完成此操作:

```
brew install pyenv
```

现在您已经准备好开始管理多个 python 版本了。

# 使用 pyenv 管理 Python 的多个版本

让我们开始吧。

首先，让我们使用 pyenv 列出可供安装的 python 版本。

```
pyenv install --list
```

现在你可以从列表中选择任何你想要的版本并安装它。例如，要安装 python 版本 3.9.13，可以运行以下命令。

```
pyenv install 3.9.13
```

一旦安装了想要使用的 Python 版本，就可以使用`pyenv global`命令将其设置为全局版本，这意味着无论何时在机器上运行 Python，都将默认使用该版本。

```
pyenv global 3.9.13
```

或者，您可以使用`pyenv local`命令为特定的项目或目录设置特定的 Python 版本。如果您想在不影响全局版本的情况下为特定项目使用不同版本的 Python，这将非常有用。

```
pyenv local 3.9.13
```

随时检查哪个版本的 python 当前是活动的是很容易的。

```
pyenv which
```

在您的机器上安装并使用了几个版本的 python 之后，您可能想要列出您已经安装的所有版本。您可以使用下面的命令来完成。

```
pyenv versions
```

您也可以卸载不再使用的旧版本 python，以节省计算机空间。

```
pyenv uninstall 3.7.3
```

pyenv 的一个我最喜欢的特性是能够管理你想要的任何 python 版本的虚拟环境。使用特定版本的 python 创建虚拟环境非常简单。

```
pyenv virtualenv 3.9.13 my-venv
```

然后，您可以使用`pyenv activate my-venv`进入环境，使用`source deactivate`退出环境。

# 结论

Pyenv 是一个强大的工具，用于管理机器上多个版本的 Python。它使您能够轻松地安装、切换和卸载不同版本的 Python，并为不同的虚拟环境设置特定的版本。使用 pyenv，在使用不同版本 Python 的项目之间切换是轻而易举的事情！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)