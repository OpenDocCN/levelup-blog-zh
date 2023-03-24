# 升级到 MacOS Ventura 后如何修复 Git 中的“无效活动开发者路径”

> 原文：<https://levelup.gitconnected.com/fix-invalid-active-developer-path-macos-upgrade-1b8afedb690a>

## 了解并修复升级到最新 OSX 时 Git 上的“无效活动开发者路径”

![](img/3352ce884d8d19de7c6b30b16ce35ffd.png)

照片由[罗曼·辛克维奇·🇺🇦](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果您正在访问此页面，那么您可能最近将 macOS 升级到了最新版本(目前是 MacOS Ventura ),并且在尝试运行`git`命令时，您会收到以下错误:

```
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
```

如果是这样的话，那你就来对地方了。在接下来的几个部分中，我们将快速浏览这个错误的主要原因，然后提供一个非常简单而有效的解决方案，这样您就可以像往常一样开始做(git)业务了。

## 错误是关于什么的？

您在 Mac 上进行重大升级时看到此错误的原因是，您尚未同意 Xcode 命令行工具的许可协议。

现在重要的是要提到，`git`本身并没有指明错误是来自本地机器还是远程主机。在与遥控器交互时(例如在`git pull`上)，您可能会看到此错误，但在与本地遥控器交互时(例如`git status`)可能不会看到此错误。基于以上所述，您可能需要在本地或远程主机(或者两者都有)上运行下一节中概述的建议解决方案。

## 解决方案

现在，为了解决这个问题，您需要做的就是使用以下命令安装命令行工具包:

```
xcode-select --install
```

> 命令行工具包是一个独立的小软件包，可以从 Xcode 单独下载，它允许您在 macOS 中进行命令行开发。它由 macOS SDK 和命令行工具如 Clang 组成，安装在`/Library/Developer/CommandLineTools`目录中。— [苹果技术说明](https://developer.apple.com/library/archive/technotes/tn2339/_index.html)

请注意，没有必要安装 XCode，因为这将是一个巨大的安装。你所需要的(至少是解决这个问题)只是命令行工具，它们只有几百兆字节。

现在，如果上述命令对您不起作用，那么您可能还需要运行以下命令:

```
sudo xcode-select --reset
```

## 最后的想法

`error: invalid active developer path`是开发人员将他们的机器升级到最新的 OSX/macOS，同时试图运行`git`命令时最常见的错误之一。不过请放心，这不是您的错——您只需要运行命令行工具安装命令，如前一节所演示的那样，然后就可以开始了！

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](https://towardsdatascience.com/diagrams-as-code-python-d9cbaa959ed5) [## Python 中作为代码的图

### 用 Python 创建云系统架构图

towardsdatascience.com](https://towardsdatascience.com/diagrams-as-code-python-d9cbaa959ed5) [](https://towardsdatascience.com/visual-sql-joins-4e3899d9d46c) [## SQL 连接的直观解释

### 用维恩图和实际例子理解 SQL 连接

towardsdatascience.com](https://towardsdatascience.com/visual-sql-joins-4e3899d9d46c) [](https://towardsdatascience.com/infrastructure-as-code-f153d810428b) [## 基础设施作为代码

### 用代码管理基础设施资源

towardsdatascience.com](https://towardsdatascience.com/infrastructure-as-code-f153d810428b)