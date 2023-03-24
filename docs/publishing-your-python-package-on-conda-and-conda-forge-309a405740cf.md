# 在 conda 和 conda-forge 上发布您的 Python 包

> 原文：<https://levelup.gitconnected.com/publishing-your-python-package-on-conda-and-conda-forge-309a405740cf>

值得在 conda 和 conda-forge 上发布您的 python 包

![](img/9e59445e74b681b050fbc058277ce6db.png)

由[大卫·克劳德](https://unsplash.com/@davidclode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/anaconda?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 为什么用康达？

我最近[写了一篇文章](/turn-your-python-code-into-a-pip-package-in-minutes-433ae669657f)指导用户将他们的 python 包发布到 PyPI 以通过 pip 安装的过程。然而，许多用户更喜欢使用康达。我喜欢让我的包在 conda 上可用，因为它可以很好地处理具有外部依赖性的包。有时一个包也只能在 conda 通道上使用，为了避免依赖性问题，最好避免在一个环境中混合 pip 和 conda 安装。

> **注意**:如果你还没有在 PyPI 上发布你的包，请确保在我的另一篇文章中看到初始步骤 I 布局[。您需要有正确的文件层次结构、`__init__.py`文件和许可证。参见我的](/turn-your-python-code-into-a-pip-package-in-minutes-433ae669657f)[示例 Github repo](https://github.com/wino6687/pip_conda_demo) 获取正确文件层次的示例。

## 康达频道

通道只是存储包的位置。一些非常受欢迎的包存储在默认的 conda 通道中，许多较小的包存储在个人用户自己的通道中，或者有像 conda-forge 这样的社区通道。

您可以在命令行中指定安装软件包时要使用的通道。或者您可以创建一个 [conda 配置文件](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html) ( `.condarc`)，它允许您按优先级顺序列出通道。

```
conda install swepy --channel conda-forge
```

在这里，我指定我想从 conda-forge 频道下载 python 包`swepy`。[康达锻造](https://conda-forge.org/)是一个由成千上万贡献者组成的频道；它像 PyPI 一样作为许多人的包的中央存储库。但是它的与众不同之处在于，conda-forge 上的每个包都必须依赖于 conda-forge。这意味着你可以在你的整个环境中使用 conda-forge，避免讨厌的依赖问题！

在这篇文章中，我将向你展示如何在你的个人 conda 频道上发布你的包，然后我将介绍在 conda-forge 频道上发布包所需的步骤。

# 发布 Conda 包

1.  创建一个`meta.yaml`文件
2.  创建您的`build.sh`和/或`bld.bat`文件

## 创建 meta.yaml 文件

你的`meta.yaml`文件是告诉康达如何建立你的图书馆的配方。它类似于用于为 pip 创建库的`setup.py`。

这里有一个来自我早期的一个库 swepy 的例子:

可以看到我先列出了包信息，然后 conda 在哪里可以找到源代码。在`requirements`部分中，`run:`下的列表包含了您的库需要的所有依赖项。最后，`about`部分包含基本信息，如许可证和简要总结。

## 创建 build.sh 和/或 bld.bat 文件

*   `build.sh`是 macOS 和 linux 的 shell 脚本
*   `bld.bat`是用于 windows 的批处理文件

这些文件非常简单，应该完全如下所示:

## 构建您的包

现在，在包的根目录中，运行以下命令:

```
conda build .
```

一旦完成，它将显示构建文件的路径。它应该是这样的:

```
~/anaconda/conda-bld/linux-64/packagename-py37_0.tar.bz2
```

## 将您的包上传到 Anaconda

1.  [用 Anaconda 创建一个账户](http://Anaconda.org)
2.  安装 Anaconda 客户端`conda install anaconda-client`
3.  使用`anaconda login`登录您的 Anaconda 帐户
4.  使用之前的文件路径上传您的包

```
anaconda upload ~/anaconda/conda-bld/linux-64/packagename-py37_0.tar.bz2
```

恭喜你。您的软件包现在可以在您的个人频道上获得，并且可以通过 conda 而不是 pip 进行安装。安装时，您需要指定您的个人频道:

```
conda install mypackage --channel <username>
```

或者将其添加到您的`.condarc`文件中:

```
conda config --add channels <username> conda install mypackage
```

# 康达-福吉

Conda-forge 是迄今为止我最喜欢的发布 python 包的地方。在 conda-forge 上发布一个包之前，它必须通过一个简短的审查过程，并且它的所有依赖项必须已经可以通过 conda-forge 安装。对所有的包使用 conda-forge 可以避免由于混合通道或安装程序而产生的依赖性问题，从而为您省去一些麻烦。

## 提交给康达锻造公司之前要采取的步骤

1.  编写单元测试(我喜欢使用 [pytest](https://docs.pytest.org/en/stable/)
2.  确保 conda-forge 上已经发布了所有依赖项
3.  至少为您的软件包编写基本的文档

理想的情况是，您已经为您的包编写了一些单元测试，以便它们可以在更新 conda-forge 原料时运行。你也应该把你的代码文档化，这样用户就能知道如何使用它。

## 添加您的康达锻造配方

1.  叉子[康达锻造/分阶段配方](https://github.com/conda-forge/staged-recipes)
2.  从分段配方主分支创建一个新分支
3.  在“recipes/ < mypackagename > /目录中添加一个新配方(`meta.yaml`)
4.  将这些更改作为“拉”请求提交(这将触发对所有操作系统的测试)
5.  一旦合并，您将获得一个新的原料库，您的包将在这里建立并上传到康达锻造！

在所有这些步骤中，唯一有点混乱的部分是创建你的食谱。您应该在分叉的“分段配方”存储库中创建的文件层次结构如下所示:

```
/recipes
    /<packagename>
        meta.yaml 
```

下面是我用来制作我的一个图书馆 SWEpy 的食谱。菜谱是一个`meta.yaml`文件，很像你的基本 conda 包所用的文件，但需要更多的细节。

有几件事需要注意:

*   我使用我的 PyPI 包作为源代码。这样，当我用 CI 服务更新我的 PyPI 包时，我的 conda-forge 包也会自动更新。
*   我为 conda-forge 设置了一个更复杂的测试套件，包括模仿一个服务器和提供两个小文件，以便在不需要访问 web 的情况下测试文件抓取。
*   配方可能比这复杂得多，并且涉及到在 Python 之外安装依赖项。参考[康达锻造文档](https://conda-forge.org/docs/maintainer/adding_pkgs.html#the-recipe-meta-yaml)了解如何制作更复杂的配方。

一旦您提交了拉动请求，conda-forge 的人员将会评论在合并到自己的原料之前是否需要进行任何更改。在它被合并到自己的原料中之后，它将被构建，然后被部署，以便可以通过 conda-forge 频道下载！

# 包扎

您现在知道如何在 conda 和 conda-forge 上发布您的 Python 库了！但是没有什么比在多个地方手动维护您的包更烦人的了。

我通过设置与 Travis-CI 的持续集成解决了这个问题，Travis-CI 会自动将我的代码的任何新版本部署到 PyPI，然后将新版本从 PyPI 部署到我的 conda-forge 原料。

你可以[阅读我的另一篇文章](/tools-for-writing-effective-and-robust-python-libraries-28bfd4c018c1)关于我如何设置我的工作流程以使 Python 库更加健壮。在更新库的时候，持续集成是你最好的朋友；Travis 将运行您的单元测试，如果通过，就部署它们以保持您的包是最新的。这样你就再也不用手动上传新版本了。

# 链接和文档

*   [Github 回购示例](https://github.com/wino6687/pip_conda_demo)
*   [我的构建健壮 Python 库指南](/tools-for-writing-effective-and-robust-python-libraries-28bfd4c018c1)
*   [康达包装文件](https://conda.io/projects/conda-build/en/latest/user-guide/tutorials/build-pkgs.html)
*   [康达锻造公司添加配方指南](https://conda-forge.org/#add_recipe)