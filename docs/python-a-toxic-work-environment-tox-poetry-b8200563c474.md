# Python:有毒的工作环境

> 原文：<https://levelup.gitconnected.com/python-a-toxic-work-environment-tox-poetry-b8200563c474>

![](img/318b6f663c88c6f7a543d7d0312ae83d.png)

丹·迈耶斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

请随意[克隆我的示例 GitHub 库](https://github.com/dbudwin/MediumBlogExamples/tree/master/ToxicWorkEnvironment)并在阅读这篇博客时跟随👍

在开发项目时，我最喜欢的两个自动化工作流程的工具是`tox`和`poetry`。这两个工具的结合使得跨多个 Python 版本的林挺、测试和虚拟化我的代码变得轻而易举！

我越来越讨厌在 Python 虚拟环境之外管理多个版本的 Python 和 Python 包。在一两个有少量依赖项的项目之后，保持一切正常工作就变成了一团乱麻。**修改一个项目中的依赖项不应该以任何方式影响您机器上的其他项目。**项目间 Python 的依赖和版本应该是幂等的。随着时间的推移手动管理项目是不切实际的，因为新版本的 Python 和包的更新是在不使用 Python 虚拟环境的情况下发布的，并且不会破坏机器上的另一个项目。

下面是对这对充满活力的组合的高度介绍:

# 诗意

如果你对`poetry`不熟悉，它扮演着与`setup.py`或`pipenv`相似的角色，但是提供了更多的灵活性、功能性，并且根据我自己的经验，更容易使用。[从他们的 GitHub 页面](https://github.com/python-poetry/poetry):

> 诗歌:Python 的依赖管理
> 
> poem 帮助您声明、管理和安装 Python 项目的依赖项，确保您在任何地方都有正确的堆栈。

虽然`poetry`的目的在很大程度上可以与`pipenv`互换，但由于新版本的频繁中断以及在我的一些项目中锁定和安装软件包的速度明显变慢，我最近一直在从`pipenv`迁移。此外，`poetry`在与它交互时，看起来和感觉上都更加现代。`poetry`最近也在`pipenv`的[下载量](https://hugovk.github.io/top-pypi-packages/)上取得进展。

# 毒药

`tox`本质上是一个工具，用于杂耍各种 Python 虚拟环境(想想不同版本的 Python)并针对它们运行命令。[从他们的网站](https://tox.readthedocs.io/en/latest/):

> `tox`旨在自动化和标准化 Python 中的测试。这是简化 Python 软件的打包、测试和发布过程的更大愿景的一部分。

# 创造有毒的工作环境

要让`poetry`和`tox`很好地配合，还需要做一点工作。幸运的是，这一切都很简单。以这种方式配置项目的一个好处是，它减少了全局安装包的数量，并使针对任何数量的环境测试您的代码成为可能(想想针对 Python 3.7 和 3.8 运行测试)。

在下面的例子中，我们将创建一个项目，它使用`poetry`来管理依赖项，`tox`用于测试自动化，而`pytest`作为测试运行器来演示所有这些工具如何协同工作以及它们将如何使您的项目受益。这个例子将针对 Python 3.7 和 Python 3.8。稍后，我们将利用 Python 3.8 中的一个新特性——[自记录 f 字符串](https://docs.python.org/3/whatsnew/3.8.html#f-strings-support-for-self-documenting-expressions-and-debugging)——来演示测试如何在 Python 3.7 中失败，但在 Python 3.8 中通过。您需要在您的机器上安装您想要测试的 Python 的每个版本。

演示 Python 3.8 功能的 Python 代码，当 tox 针对 Python 3.7 运行此代码时将会失败

基本的概念验证 pytest，tox 将运行两次(一次针对 Python 3.7，一次针对 Python 3.8)

## pyproject.toml

pyproject.toml 将最低 Python 版本与 pytest 和 tox 一起定义为开发依赖项

这个`pyproject.toml`有两个开发依赖项，一个用于`pytest`，一个用于`tox`。这个文件就是`poetry`读取以创建锁文件并安装所请求的包。没有必要把`poetry`指定为依赖项，因为它应该是你在全球范围内安装的少数几个包之一。

## 毒性信息

tox.ini 配置为针对 Python 3.7 和 Python 3.8 的 pipenv

当我们从命令行运行`tox`时，上面的`tox.ini`文件将作为我们测试程序的入口点。第 3 行指示`tox`按顺序为 Python 3.7 和 Python 3.8 运行我们的`[testenv]`部分中概述的步骤。

# 结束语

当所有东西都放在一起时，只需从命令行运行`tox`即可。在我们的例子中，我们有一个测试，总共运行两次，每个 Python 版本一次。默认情况下，一个测试会失败，一个会通过。调整在`python_version_printer.py`中被注释掉的行，两个测试都将通过。

将`tox`与`poetry`结合使用使得管理依赖关系变得容易，尤其是当您需要处理多个不同的 Python 项目时。近年来，创建和使用 Python 虚拟环境变得越来越容易，消除了障碍。从最初的 Python `virtualenv`，到逐渐完善的`pipenv`，再到现代的`poetry`。

`tox`不仅仅局限于安装依赖项和运行单元测试。有一大堆`tox`插件和脚本，你可以配置它们来扩展运行`tox`时的功能，比如`flake8`林挺来构建包。

编码快乐！🧑🏻‍💻

[GitHub](https://bit.ly/dbudwin-github)|[LinkedIn](https://bit.ly/dbudwin-linkedin)|[Medium](https://bit.ly/dbudwin-medium)|[budw . in](https://bit.ly/budw_dot_in)