# OMBD#23:在 Git 中的时间旅行？认识 Git Reflog

> 原文：<https://levelup.gitconnected.com/time-traveling-in-git-meet-git-reflog-ff216419e564>

## 让我们学习使用这一高级工具的力量来拯救灾难

欢迎来到第 23 期，继续成为一名更好的开发人员，在这里，通过阅读简短的知识，每次一分钟，你将成为一名更成功的软件开发人员。

## [⏮️](https://medium.com/codex/a-snazzy-trick-how-to-style-your-console-log-messages-2b23ac281b31) [🔛](https://jportella93.medium.com/one-minute-to-become-a-better-developer-ombd-5b1a1d37468e)

![](img/84ecbb5df4fdcaaaa88e933042aab080.png)

我的好友洛尔·尼古拉斯的艺术作品

## 问题是

一个被意外删除的本地分支，被压扁的提交了我们希望不被压扁的…当灾难发生时，如果我们能在 Git 中**时间旅行不是很棒吗？**

## 一个解决方案

让我们来学习如何使用`**git reflog**`。首先，一个位的上下文。

引用日志或“reflogs”记录本地存储库中分支和其他引用的提示何时被更新。

默认情况下，目标是 HEAD——当前活动分支的符号引用——但是其他分支、标签、远程和 Git 存储也可以作为目标。

`**git reflog**`用语法`name@{qualifier}`显示“动作”。`HEAD@{2}`意为“两招前的头”。

让我们付诸实践吧。

## 恢复本地删除的分支

我们删除了一个本地分支，如何恢复它？

```
git branch -D navbar-feature
```

我们可以使用`git reflog`来寻找时间旅行到的历史点。我们看到，一个移动之前，我们在刚刚删除的分支上执行了最后一次提交。

```
3b7a6fdb (HEAD -> master) HEAD@{0}: checkout: moving from navbar-feature to master
**9a07e99f HEAD@{1}: commit: feat: add Navbar** 3b7a6fdb (HEAD -> master) HEAD@{2}: checkout: moving from master to navbar-feature
```

因此，我们可以创建一个新的分支，包含该历史点的内容:

```
git checkout -b navbar-feature HEAD@{1}
```

太棒了，我们已经恢复了我们的分支及其提交！

## 恢复压缩的提交

我们有 3 个提交:

```
747ef1e feat: add Button
19d9327 feat: add Navbar
effb3b4 initial commit
```

我们意识到我们忘记了在 Navbar 提交中添加一些东西，所以我们添加了这些更改，并通过修正`git commit --fixup 19d9327`提交。我们现在的历史:

```
c2149d1 fixup! feat: add Navbar
747ef1e feat: add Button
19d9327 feat: add Navbar
effb3b4 initial commit
```

现在，为了清理我们的历史，我们使用`git rebase --autosquash --interactive HEAD~4`来压缩最后一次提交及其引用。我们的历史现在是清白的:

```
de1e9de feat: add Button
b3b4932 feat: add Navbar
effb3b4 initial commit
```

然而，我们怎么能回到我们的历史基数之前呢？`git reflog`显示我们提交修复时是在 4 次移动之前`HEAD@{4}`，而最近的移动是基础的一部分。

```
4610b383 HEAD@{0}: rebase -i (finish): returning to refs/heads/master
4610b383 HEAD@{1}: rebase -i (pick): feat: add Navbar
52d0c48c HEAD@{2}: rebase -i (fixup): feat: add Navbar
07097c96 HEAD@{3}: rebase -i (start): checkout HEAD~4
**c2149d1 HEAD@{4}: commit: fixup! feat: add Navbar**
```

所以我们可以`git reset HEAD@{4}`并且我们已经恢复了我们的历史！这是`git log`现在显示的:

```
c2149d1 fixup! feat: add Navbar
747ef1e feat: add Button
19d9327 feat: add Navbar
effb3b4 initial commit
```

## 感谢阅读！继续学习，不要停止编码😊

【资源
[https://git-scm.com/docs/git-reflog](https://git-scm.com/docs/git-reflog)
[https://www . atlassian . com/git/tutorials/rewriting-history/git-ref log](https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog)
[https://www.edureka.co/blog/git-reflog/](https://www.edureka.co/blog/git-reflog/)

## 如果你喜欢这个故事，你可能也会喜欢:

[](https://towardsdev.com/5-remarkable-git-commands-that-will-boost-your-coding-productivity-e8635b20b4bf) [## 5 个非凡的 Git 命令将提高您的编码效率

### 这些是定制的，它们不会出现在 Git 文档中！在过去的两年里让我免除了无数的头痛…

towardsdev.com](https://towardsdev.com/5-remarkable-git-commands-that-will-boost-your-coding-productivity-e8635b20b4bf) [](https://jportella93.medium.com/master-git-diff-with-these-not-so-known-commands-9fecfa3006d0) [## 主 Git Diff 使用这些不太知名的命令

### 查看您自己的拉动式请求时节省时间

jportella93.medium.com](https://jportella93.medium.com/master-git-diff-with-these-not-so-known-commands-9fecfa3006d0) 

## [⏮️](https://medium.com/codex/a-snazzy-trick-how-to-style-your-console-log-messages-2b23ac281b31) [🔛](https://jportella93.medium.com/one-minute-to-become-a-better-developer-ombd-5b1a1d37468e)