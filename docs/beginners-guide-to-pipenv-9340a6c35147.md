# Pipenv 初学者指南

> 原文：<https://levelup.gitconnected.com/beginners-guide-to-pipenv-9340a6c35147>

![](img/1f0f9e98585a888ab76cd0db09c3c4a9.png)

**Pipenv 概述:**

Pipenv 是一个 python 包管理器，它让程序员可以更好地控制他们的 pip 项目依赖性。Pipenv 允许您选择 python 的特定版本，以及所需的 pip 包的版本。对于熟悉 JavaScript 开发的人来说，pipenv 可以被认为是 npm 的 python 等价物。

Pipenv 解决了在 python 中使用 pip 包的一些问题。

1.  **Pipenv 允许用户选择项目需要的具体 python 版本。**假设一个名为 myapp 的项目使用了 python 中的异步特性。这需要 python 3.5 或更高版本。如果 myapp 的用户在他们的系统上安装了 python 3.4，安装过程中将会出现错误。当用户试图在没有所需功能的情况下运行程序时，这将防止许多运行时错误。
2.  **Pipenv 允许安装特定版本的 pip 软件包。**加班 pip 包经历变更和更新。假设一个项目使用 Django 2.2。Pipenv 将允许特定版本的 Django 或任何其他 pip 包被锁定到项目中。
3.  Pipenv 为您创建并管理一个虚拟的 python 环境。这使得分离你的全局 pip 包和你的项目使用的包变得容易。这进一步降低了依赖关系冲突的可能性。

**安装管道 nv:**

根据您的系统，有几种安装 pipenv 的方法。首先，您需要在系统上安装一个 python 3 版本。在尝试安装 pipenv 之前，请按照说明在您的系统上安装 python 3。

1.  **Mac 安装 pipenv:**

安装了 homebrew 的 Mac 用户只需在终端中运行以下命令:

```
brew install pipenv
```

2.**我的首选方式，适用于所有系统:**

如果您没有运行 macOS，有几种方法可以在您的系统上安装 pipenv。我会在这篇文章的底部留下 pipenv 文档的链接，这样你就可以参考了。但是，如果您的系统上已经安装了 python 3 和 pip(推荐),您可以简单地通过 pip 安装 pipenv。请注意，您将通过 pip 全局安装 pipenv。运行以下命令:

```
pip install pipenv
```

或者

```
pip3 install pipenv
```

**使用 Pipenv 设置虚拟环境:**

一旦所有东西都安装好了，用 pipenv 建立一个虚拟环境就很容易了。在项目目录中，在终端中键入以下命令:

```
pipenv install --python 3.8
```

在这个例子中，我创建了一个使用 python 3.8 的虚拟环境。然而，系统上安装的任何 python 版本都可以替代 3.8。

执行该命令后，您会注意到创建了两个文件。一个 Pipfile 和一个 Pipfile.lock。这些文件是你的项目的依赖项将被详细保存的地方。

创建虚拟环境后，您可以通过键入以下命令在终端中激活它:

```
pipenv shell
```

**使用 Pipenv 管理 Pip 包:**

Pipenv 还使管理您的 pip 包变得非常简单。与使用 pipenv 时相比，通常安装 pip 包的方式有一点不同。

通常要安装一个 pip 包，您应该运行:

```
pip install django
```

然而，使用 pipenv，您应该做一些事情来使包依赖关系更加可靠和明确。

首先，在您尝试安装任何 pip 包之前，请确保您处于终端窗口中，并使用 cd 进入您的 Pipfile 和 Pipfile.lock 所在的项目目录。

其次，确保您的虚拟环境是通过 pipenv shell 激活的。

设置好一切后，键入命令:

```
pipenv install django
```

请注意，pip 被更改为 pipenv。

您也可以指定软件包的版本，如下所示:

```
pipenv install django==3.0.6
```

注意，这指定了 django 的确切版本。如果我希望用户能够使用更新的修订版本，同时仍然使用 3.0，我还可以:

```
pipenv install django==3.0
```

这样就可以安装 django 3.0 的最新版本。这是一个很好的做法，因为修订版通常附带安全更新，没有任何重大更改。

请注意，当您安装一个包时，它将被添加到您的 Pipfile 和 Pipfile.lock 中。

pipenv 的另一个很酷的技巧是，您可以指定哪些包用于生产，哪些包用于开发。您可能希望让 pip 包用于诸如林挺和格式化等任务，成为开发依赖项。做到这一点很简单:

```
pipenv install --dev autopep8
```

您还可以指定开发包的特定版本，就像您为常规的产品包所做的那样。

```
pipenv install --dev autopep8==1.5
```

**卸载 pip 包:**

要卸载 pip 包，您不需要指定版本，如果它是开发包，也不需要指定版本。

```
pipenv uninstall django
```

该包将从 Pipfile 和 Pipfile.lock 中删除。

**移除虚拟环境:**

有时您希望删除并创建一个新的虚拟环境。不管是什么原因，pipenv 都有办法删除虚拟环境。

确保您在“终端”中 Pipfile 和 Pipfile.lock 所在的目录中。然后运行:

```
pipenv --rm
```

这将删除环境。

**结论:**

Pipenv 可以为您和其他开发人员省去很多版本和项目依赖方面的麻烦。这里是 pipenv 的 GitHub 页面的链接，供进一步阅读。[https://github.com/pypa/pipenv](https://github.com/pypa/pipenv)。