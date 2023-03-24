# 如何在本地和远程删除 Git 分支

> 原文：<https://levelup.gitconnected.com/delete-local-remote-git-branch-1d8c0870eebc>

## 展示如何在 Git 中删除本地和远程分支

![](img/e134dcec15f020709b827e2c776ca60b.png)

由 [Roman Synkevych](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 介绍

有时，您可能不小心在本地机器或远程主机上分别创建了一个本地或远程分支。在其他情况下，您可能只是想摆脱远程或本地分支——可能是因为您不打算将您已经完成的工作合并为该分支的一部分。

在今天的简短教程中，我们将展示如何从命令行从本地机器和远程服务器删除本地 Git 分支。

## 删除本地 Git 分支

为了删除一个本地 Git 分支，您需要向`git branch`命令提供`--delete`或`-d`标志(后者只是`--delete`的别名)。

> `-d`，`--delete`
> 
> 删除分支。分支必须完全合并到其`upstream`分支中，或者如果没有用`--track`或`--set-upstream-to`设置`upstream`，则合并到`HEAD`分支中。

```
$ git branch --delete my-local-branch-name
```

或者

```
$ git branch -d my-local-branch-name
```

或者，您也可以使用`-D`标志，这是`--delete --force`的快捷方式。换句话说，使用`-D`标志，指定的分支将被删除，无论其合并状态如何。

```
$ git branch -D my-local-branch-name
```

## 删除远程 Git 分支

现在，如果您想要删除驻留在远程主机(服务器)上的远程分支，您需要执行`git push`。

在 Git 1 . 7 . 0 之前，您可以使用语法`git push [remotename] :[branch]`删除远程分支。举个例子，

```
git push origin :my-branch-name
```

从 [Git v1.7.0](https://github.com/gitster/git/blob/master/Documentation/RelNotes/1.7.0.txt#L154) 开始，为`git push origin :branch`引入了一种新的语法糖，现在可以重写(可能更容易记忆)为

```
git push origin --delete my-branch-name
```

## 下一步做什么

现在，一旦从远程主机上删除了一个远程分支，您还必须确保其他机器是同步的。即使从本地机器和远程主机上删除了一个分支，其他机器可能仍然有过时的跟踪分支，您可以通过运行`git branch -a`列出这些分支。

要确保所有机器现在都处于同步状态，并且已经从远程主机上删除的分支对它们来说不再可见，您需要运行

```
$ git fetch --all --prune
```

## 最后的想法

在今天的短文中，我们展示了如何使用命令行分别从本地机器和远程主机删除本地和远程 Git 分支。总结一下我们今天所学的内容，

*   使用`git branch -d my-branch-name`删除一个本地 Git 分支
*   使用`git push <remote_host> --delete my-branch-name`(这里`remote_host`通常是`origin`)

您可以在`git branch`的帮助页面中找到我们在本教程中讨论的命令的更多细节:

```
$ git branch --help
```

或者

```
$ man git-branch
```

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章您可能也喜欢**

[](https://betterprogramming.pub/common-git-errors-2e379516dc65) [## 6 个常见的 Git 错误以及如何避免它们

### 探究 Git 中一些最常见的错误

better 编程. pub](https://betterprogramming.pub/common-git-errors-2e379516dc65) [](/fix-password-authentication-github-3395e579ce74) [## 如何修复 GitHub 上移除的密码认证支持

### 了解如何在 GitHub 中配置和使用访问令牌

levelup.gitconnected.com](/fix-password-authentication-github-3395e579ce74) [](https://towardsdatascience.com/undo-last-local-commit-git-5410a18f527) [## 如何撤销 Git 中的最后一次本地提交

### 了解如何在 Git 中撤销最近的本地提交

towardsdatascience.com](https://towardsdatascience.com/undo-last-local-commit-git-5410a18f527)