# 5 个 Git 命令让你摆脱困境

> 原文：<https://levelup.gitconnected.com/5-advanced-git-commands-5a9e81150800>

## 这是一个不太高级的 Git 命令集合，任何开发人员都应该知道。

![](img/4a21a399f3d20a03386a112c5369eb3e.png)

照片由[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

作为一名软件开发人员，我每天都在使用 Git，有时还需要更高级的命令来完成工作。

我将向你展示 5 条在需要的时候有用的命令。

## 1.对未推送的提交进行更改

有时候你需要对提交进行一些修改，幸好你没有推送，为此，你需要使用 ***修改*** 命令。

```
❯ git commit --amend
```

使用 ***修改*** 命令，您可以使用上面的命令将文件添加到最新提交，并使用下面列出的命令更改消息或两者。

```
❯ git commit --amend -m "The new commit message"
```

## 2.重置未推送的提交

如果您提交了一些您不应该提交的文件，您可以使用 ***重置*** 命令重置它。它有两种用法:重置提交并保留更改和重置提交而不保留任何更改。

```
❯ git reset HEAD~1 --soft   // keep changes
❯ git reset HEAD~1 --hard   // don't keep changes
```

代替 ***HEAD~1，*** 可以使用提交的散列来复位到特定的提交。

## 3.删除未跟踪的文件

有些时候，你需要清理所有未清理的东西。为此，您将使用下面的命令。

```
❯ git clean -fdx
```

## 4.快速查找开发分支中添加的错误

在我看来，这是一个很棒的命令，在有许多开发人员和大量日常提交的团队中，它可以节省您的时间。我说的这个命令是 ***平分，*** 下面是一个如何使用的例子。

```
❯ git bisect start <tag of a good commit>
```

当您想要使用平分命令时，您可以指定一个您知道是好的提交，然后它将在该提交和当前提交之间移动。如果可以的话，你可以用

```
❯ git bisect good
```

并且它将继续缩小范围以找到被破坏的提交。
该命令选项的完整列表可在这里[找到。](https://git-scm.com/docs/git-bisect)

## 5.从任何分支重新应用特定的提交

例如，当您在未来的分支上提交了一些工作，并且您在您的分支中也需要这些工作，但是没有已经提交的所有附加工作时，这个命令是有用的。为此，您可以使用**精选**命令。

```
❯ git cherry-pick <commit hash>
```

如果您想将这些更改添加到您的工作目录中而不提交它们，您可以添加选项 ***no-commit。***

```
git cherry-pick --no-commit <commit hash>
```

上面的命令不是最常用的也不是最先进的，只是我认为有用的一部分，我希望它们对你有用。

保持安全和快乐的编码！

**资源:**

[](https://git-scm.com/docs/) [## 参考

### 快速参考指南:GitHub 备忘单|可视化 Git 备忘单

git-scm.com](https://git-scm.com/docs/)