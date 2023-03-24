# Git 子模块简介

> 原文：<https://levelup.gitconnected.com/an-intro-to-git-submodules-9f49523281fb>

## 如何让多个存储库协同工作？

![](img/3d5565d0692b66b049919df7aad25231.png)

it 子模块已经存在了十多年，然而许多开发人员从未使用过它们。感谢今天的包管理器和共享库的选项列表，git 子模块并不意味着每个需要共享包或依赖项的场景都有 git 子模块。从源代码和消费者的角度来看，子模块主要是用来控制代码的变化。让我们详述一下那个想法。

首先，**git 子模块只是对另一个库**的引用。你可以有一个总体项目，比如说你自己的 SDK(软件开发工具包)。该 SDK 可能依赖于子项目，如编译器、处理编译后二进制文件执行的运行时、代码样本等等。SDK 将是您的主要项目存储库，编译器和运行时作为子模块(当然，在实际应用程序中，您需要的子模块项目不仅仅是这两个)。如果您需要立刻对 SDK 源代码*及其子项目*进行修改，git 子模块可能是您的一个选择。

本文将重点介绍 git 子模块的一些关键特征，包括如何创建它们、基本功能以及在最后总结它们的主要亮点。

# 向新项目添加 Git 子模块

将子模块添加到现有的 git 存储库中很容易，但是需要注意一些事情。让我们从创建一个简单的项目`sdk`开始，该项目包含单个文件`README.md`以及 git 跟踪。

```
$ mkdir sdk
$ cd sdk
$ touch README.md
$ git init
$ git add README.md
$ git commit -m "initial commit"
```

现在，假设我们在 GitHub 中有另一个已经有远程源的存储库。我们可以很容易地将子模块添加到我们的`sdk` repo 中，如下所示:

```
$ git submodule add [https://github.com/Some-User/compiler.git](https://github.com/Israel-Miles/PasswordManager.git)
Cloning into 'path/to/sdk/compiler'...
remote: Enumerating objects: 345, done.
remote: Counting objects: 100% (345/345), done.
.....
```

我们现在将在`sdk`中拥有`compiler`存储库和`.gitmodules`文件。这个文件只有我们的子模块的基本信息，比如它们的名称、路径、url 等等。在再次达到干净的 git 状态之前，您需要添加并提交这些新文件。检查子模块的状态很容易。

```
$ git submodule status
ab18d94ae63 compiler (heads/main)
```

为`compiler`列出的提交引用是*对该存储库的主要分支的最近提交。*这样，git 子模块只是从一个存储库到下一个存储库的链接，增加的好处是每个存储库的源代码很容易访问和共享。

# 使用 Git 子模块

默认情况下，任何基本的 git 命令都只针对您当前所在的存储库运行。因此，如果您在`sdk`回购协议中(但在`compiler`回购协议之外)进行了更改，那么 git 将只向您显示那些在`sdk`中的差异。拉、推、提交等。都只针对您当前所在的回购执行。

如果您想在项目中的所有子模块上运行一个命令，比如`git pull`，那么您应该从`sdk`运行`git pull --recurse-submodules`。这将提取对`sdk`所做的任何更改，以及从每个子模块当前所在的分支提取新的更改。如果您的子模块有子模块，该过程将继续，直到所有子模块都被更新。要在运行`git pull`时自动提取子模块，可以运行`git config submodule.recurse true`。

为了跨子模块运行更多的通用命令，使用`foreach`术语来遍历每个子模块。示例:

```
$ git submodule foreach npm install
```

## 跟踪子模块提交引用

假设您对`sdk`的一个子模块进行了修改。您添加并提交您的更改，然后返回到`sdk`的根。如果您运行`git status`，您将看到以下内容:

```
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   compiler (new commits)
```

Git 现在告诉您，您的一个子模块中的当前提交与您之前的状态不同。您需要将更改的子模块添加到 git 跟踪中，然后提交更新，您的消息可能是“更新的子模块版本”。一个非常常见的错误是推送错误的子模块引用，因此跟踪您希望每个子模块指向的提交/分支非常重要。

## 克隆项目，包括其子模块

当您下载一个包含子模块的 git 存储库时，默认情况下您不会下载子模块。如果`sdk`项目被公开托管在 GitHub 上，并且您将`sdk`项目克隆到您自己的机器上，您将不会获得它的任何子项目。您将获得文件夹引用，但没有其他内容。

```
$ git clone git@github.com:Some-User/sdk.git
$ cd sdk/compiler
$ ls
$        # nothing here
```

要克隆包含其子模块的所有文件的项目，您可以使用`--recurse-submodules`标志进行克隆，或者在克隆`sdk`之后运行`git submodule update --init`。两者都将完成拉下所有相关文件的任务。

# Git 子模块突出显示

正如您所看到的，git 子模块不同于一般的包管理器。它们允许您链接到 git 存储库，但仍然将它们视为单独的项目。Git 子模块实际上在过去已经收到了相当多的负面反馈，但是我认为最重要的是要客观地了解子模块提供了什么，以及它们是否适合您的下一个项目。

*   允许链接 git 存储库以便于访问
*   命令可以一次跨多个存储库运行
*   可以高精度地维护存储库版本
*   版本跟踪通常变得更具描述性
*   源代码可以在存储库之间共享
*   将许多存储库作为一个存储库分发

## 一些需要考虑的事情

*   额外的版本控制复杂性会增加错误
*   子模块更新机制不会删除过时的子模块

如果你对 git 子模块有自己喜欢/不喜欢的地方，或者如果你对本文的任何部分感兴趣，请在评论中分享你的想法！如果你想直接支持我的写作，你可以使用下面我的推荐链接注册。感谢阅读！

[](https://israel-miles.medium.com/membership) [## 通过我的推荐链接加入 Medium 以色列万里行

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

israel-miles.medium.com](https://israel-miles.medium.com/membership)