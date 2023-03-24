# CLOC——计算代码行数

> 原文：<https://levelup.gitconnected.com/cloc-count-lines-of-code-883c6284b1d7>

## 编程；编排

## 计算各种编程语言中源代码的空行、注释行和物理行

![](img/c70ce574af446b984f71755023af8046.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com/) 上的照片

您可能需要计算项目中的代码行数，原因有很多。也许你想跟踪项目的进展，并对自己的进展感到满意。或者也许你的客户要求更新状态。不管你的目的是什么，计算跨越不同文件的代码行的总数都是一件非常麻烦的事情，尤其是当你的项目文件跨越数十甚至数百个的时候。

因此，我们可以使用 CLOC(计算代码行数)来帮助我们。这是一个由 [AIDaniel](https://github.com/AlDanial) 设计的项目，我在这里添加了一个他的项目[的链接。](https://github.com/AlDanial/cloc#Docker-)

# 装置

根据您的操作系统，这些安装方法中的一种可能适合您(对于 Windows，除了最后两个条目外，所有条目都需要 Perl 解释器):

```
npm install -g cloc                    # npmjs
sudo apt install cloc                  # Debian, Ubuntu
sudo yum install cloc                  # Red Hat, Fedora
sudo dnf install cloc                  # Fedora 22 or later
sudo pacman -S cloc                    # Arch
sudo emerge -av dev-util/cloc          # Gentoo
sudo apk add cloc                      # Alpine Linux
sudo pkg install cloc                  # FreeBSD
sudo port install cloc                 # Mac OS X with MacPorts
brew install cloc                      # Mac OS X with Homebrew
choco install cloc                     # Windows with Chocolatey
scoop install cloc                     # Windows with Scoop
```

你可以在这个项目的官方文档中找到更多信息。如果你的 Windows 笔记本电脑上没有 Perl，你可以在这里得到它。

作为一名 Mac 用户，我在这次安装中使用了自制软件。如果你想学习如何在你的 Mac 上使用家酿，你可能想看看这篇文章:

[](https://medium.com/macoclock/a-quick-guide-to-homebrew-24c64b19bd25) [## 自制啤酒快速指南

### 最好的 Mac 包管理器

medium.com](https://medium.com/macoclock/a-quick-guide-to-homebrew-24c64b19bd25) 

## GitHub 仓库的 CLOC

为此，您必须为您的项目创建一个 GitHub 存储库。完成设置后，将以下内容复制并粘贴到终端中:

```
git clone --depth 1 "your-github-repo-link" temp-cloc-clone &&
cloc temp-linecount-repo &&
rm -rf temp-cloc-clone
```

第一行在本地查找并克隆您的 GitHub 存储库，名称为`temp-cloc-clone`。️

第二行运行`cloc`命令来计算 GitHub 存储库中的代码行数。

第三行`rm -rf`删除 Mac 上名为`temp-clock-clone`的本地克隆存储库。

## 本地存储文件的 CLOC

这个很简单。只需在您的首选终端中使用`cloc`，并输入您想要运行命令的文件/文件夹的目录。

示例:

```
cloc ~/Documents/myprojectmainfolder/myproject.cpp
```

# 替代品(适用于 macOS 和 Windows)

如果您不熟悉或不习惯命令行，这里有一些可供选择的方法(尽管它们要求您的项目位于 GitHub 存储库中):

*   [GLOC](https://chrome.google.com/webstore/detail/github-gloc/kaodcnpebhdbpaeeemkiobcokcnegdki) ，一个为私有和公共库工作的 Chrome 扩展(这个扩展要求你包含一个个人访问令牌，如果你正在访问私有库，当你把这个扩展添加到 Chrome 或者 Chrome 浏览器时，它会提示你这么做——只要向下滚动并生成令牌)
*   [本 GitHub 项目行计数器](https://line-count.herokuapp.com/)

> ⚠️:这两种选择并不总是对我有效。我会推荐使用 CLOC，但是如果你真的不习惯命令行，这两种方法应该也很好(大多数时候)。

# 临时演员

*   在你的终端上运行`cloc --show --lang`来查看 CLOC 支持的所有语言。
*   运行`cloc --help`查看 CLOC 可用命令的完整列表
*   运行`cloc --version`来检查你在笔记本电脑/Mac 上运行的 CLOC 版本

现在，失陪一下，我在欣赏我的项目进展。🤭