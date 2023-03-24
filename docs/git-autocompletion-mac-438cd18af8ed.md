# 在 Mac 上启用 Git 标签自动完成功能

> 原文：<https://levelup.gitconnected.com/git-autocompletion-mac-438cd18af8ed>

## 在 OSX 终端上为命令和分支启用 git 选项卡自动完成的分步指南

![](img/86526646e5b957d9c7aba56a61a39a50.png)

Yulia Matvienko 在 [Unsplash](https://unsplash.com/s/photos/lego?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Git 是现代开发技术栈中最常用的工具之一，因为它允许团队和个人跟踪和管理他们代码库的变更。

大多数时候，开发人员使用终端与 Git 进行交互，并执行某些操作。在本文中，我们将探讨如何在 Mac 终端上设置 Git 自动完成功能，这样您只需按 tab 键就可以自动完成 Git 命令甚至分支。

## 步骤 1:下载 git-completion zsh 脚本

第一步是下载官方 git 库上的`[git-completion.zsh](https://github.com/git/git/blob/master/contrib/completion/git-completion.zsh)` [脚本。为此，我们只需要运行一个`curl`命令:](https://github.com/git/git/blob/master/contrib/completion/git-completion.zsh)

```
curl https://github.com/git/git/blob/master/contrib/completion/git-completion.zsh -o ~/.git-completion.zsh
```

注意，如果我们将内容输出到主目录中，那么当前的工作目录并不重要，所以可以从您想要的任何目录运行上面的命令！

如果你在相当老的 OSX 上，有可能你还在使用过时的 bash，而不是 zsh。如果是这种情况，那么你将不得不下载`[git-completion.bash](https://github.com/git/git/blob/master/contrib/completion/git-completion.bash)`，但这是极不可能的，因为你至少运行卡特琳娜。

## 第二步:更新你的。zshrc 文件

现在打开位于`~/.zsrhc`中的 zsrhc 文件，简单地复制并粘贴下面的代码片段。

```
if [ -f ~/.git-completion.zsh ]; then
    . ~/.git-completion.zsh
fi
```

## 步骤 3:重新加载。zshrc 文件

为了使您在`.zsrhc`文件中所做的更改生效，您可以重启您的终端或者运行以下命令:

```
source ~/.zsrhc
```

现在应该可以开始了——无论何时键入 git 命令，现在都可以按下`tab`按钮来自动完成命令或分支名称。

## 最后的想法

Git 自动完成是一个有用的工具，当在命令行上使用 Git 时，它可以节省您的时间和精力。通过下载 git 完成脚本并在 zshrc 文件中启用它，您可以在 Mac 终端上快速轻松地完成 g it 命令和分支名称。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](https://towardsdatascience.com/data-engineer-tools-c7e68eed28ad) [## 数据工程师的工具

### 数据工程工具箱的基础

towardsdatascience.com](https://towardsdatascience.com/data-engineer-tools-c7e68eed28ad) [](https://towardsdatascience.com/parallel-computing-92c4f818c) [## 什么是并行计算？

### 理解并行计算在数据工程环境中的重要性

towardsdatascience.com](https://towardsdatascience.com/parallel-computing-92c4f818c) [](https://towardsdatascience.com/big-o-notation-32fb458e5260) [## 大 O 符号

### 用 Big-O 符号计算算法的时间和空间复杂度

towardsdatascience.com](https://towardsdatascience.com/big-o-notation-32fb458e5260)