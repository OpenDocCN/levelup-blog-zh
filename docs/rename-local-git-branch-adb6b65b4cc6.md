# 如何重命名本地 Git 分支

> 原文：<https://levelup.gitconnected.com/rename-local-git-branch-adb6b65b4cc6>

## 在 Git 上重命名本地分支

![](img/e134dcec15f020709b827e2c776ca60b.png)

由 [Roman Synkevych](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 介绍

在版本控制系统(如 Git)上创建分支时，开发人员通常低估了拥有有意义的名称的重要性。

一般来说，一个合适的分支名称应该是自描述性的，这样当另一个开发人员看到它时，它的范围就可以是直观的。此外，您还应该利用前缀约定来指示一个分支是`hotfix` / `fix`还是`feature`。通常，我们还会使用`/`来分隔分支描述和前缀，使用连字符(`-`来分隔描述中的单词。

在今天的文章中，我们将展示如何在 Git 上重命名本地分支。更具体地说，我们将探索如何:

*   重命名当前分支
*   在处理另一个分支时重命名该分支(即，当当前分支不同于要重命名的分支时)
*   推送本地分支并重命名上游分支

## 重命名当前本地分支

如果您想要重命名您在本地工作的当前分支，您需要运行以下命令

```
$ git branch -m <new-branch-name>
```

请注意，如果您要更改分支名称的大小写—例如，您要将`my-feature-branch`重命名为`My-Feature-Branch`，您必须使用`-M`标志(而不是`-m`)，否则会出现一个错误，提示您分支已经存在。

```
$ git branch -M <new-branch-name>
```

## 当您正在处理另一个分支时，请重命名该分支

现在，如果您正在处理一个分支，但是想要重命名另一个本地分支，那么您必须显式地指定旧的(即现有的)分支名称，以及想要的新分支名称。

```
$ git branch -m <old-branch-name> <new-branch-name>
```

## 推送本地分支并重命名上游分支

最后，如果您正在处理一个本地分支，并且希望以不同的名称将其推送到远程主机，那么下面的命令将会实现这个目的

```
$ git push origin -u <new-branch-name>
```

## 最后的想法

在今天的简短教程中，我们展示了如何更改本地 Git 分支的名称。尽管这听起来可能不相关，但是正确地命名您的分支是很重要的，尤其是在涉及许多开发人员的项目中。提醒一下，正确的分支名称应该是自描述性的，并且特定于其缩进的范围。此外，它还可能包括一个前缀，该前缀可能指示分支是否与一个新特性或者一个 bug 修复相关。

请注意，您可以通过帮助页面找到关于我们在本文中探索的命令的更多细节。`git branch --help`或`man git-branch`将列出关于特定命令、选项和用法的所有相关细节。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](/delete-local-remote-git-branch-1d8c0870eebc) [## 如何在本地和远程删除 Git 分支

### 展示如何在 Git 中删除本地和远程分支

levelup.gitconnected.com](/delete-local-remote-git-branch-1d8c0870eebc) [](https://betterprogramming.pub/common-git-errors-2e379516dc65) [## 6 个常见的 Git 错误以及如何避免它们

### 探究 Git 中一些最常见的错误

better 编程. pub](https://betterprogramming.pub/common-git-errors-2e379516dc65) [](https://betterprogramming.pub/how-to-merge-other-git-branches-into-your-own-2fe69f70a2b4) [## 如何将其他 Git 分支合并到您自己的分支中

### 使用命令行或 GitHub 桌面快速合并分支

better 编程. pub](https://betterprogramming.pub/how-to-merge-other-git-branches-into-your-own-2fe69f70a2b4)