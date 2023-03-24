# 如何清理 git repo 并减少其磁盘大小

> 原文：<https://levelup.gitconnected.com/how-to-clean-up-the-git-repo-and-reduce-its-disk-size-327d7b2d1f91>

![](img/f22b52f1380084337530d06761029f6f.png)

如果你在 Git repo 中工作了很长时间，那么你可以清理你的 repo 来获得磁盘空间。

Git 有一个内部垃圾收集工具，可以处理大多数用例，但是我们也可以做一些事情来清理回购。

我们来看一些清理回购的技巧。

在将这些技术应用于 git 之前，请确保获得了**的大小。git** 目录使用

## 删除远程分支的本地引用

合并后删除一个分支总是一个好的做法。Github 提供了一个选项，可以在合并 PR 后删除分支。但是这个只会删除远程中那个分支。

即使在远程系统中删除了分支，它在本地系统中仍然有引用。

删除远程分支的所有本地引用

```
git remote prune origin
```

## git 重新打包

包是 Git 内部表示，用于将所有单个对象组合成包。

不用深入研究，包是用来减少磁盘空间、镜像系统等的负载的。

这将创建回购中尚未打包的新包装。这有助于减小磁盘大小。

## git 西梅包装

Git 会有一些包文件。该命令将帮助您减少已经存在于包文件中的额外对象。这将帮助您减少包文件本身的大小。

```
git prune-packed
```

## git 引用日志过期

Git 有一个名为 reflog 的特性，可以帮助跟踪本地 repo 中的 Git 引用。Git 有一个内部垃圾收集机制来移除 Git 中的旧 refs。但也有一个手动机制来删除旧的参考。

```
git reflog expire --expire=1.month.ago
```

上述命令将删除所有超过一个月的引用。我觉得一个月比较安全。但是如果你能提到任何你觉得安全的价值。

## git gc

gc 代表垃圾收集。这个命令将帮助 Git 删除当前 repo 中不需要的数据。

```
git gc --aggressive
```

上述命令将删除存储库中超过两周的所有引用和不可访问的提交。**-激进的**将有助于更多的时间优化它。

## 组合所有命令

```
git remote prune origin **&&** git repack **&&** git prune-packed **&&** git reflog expire --expire=1.month.ago **&&** git gc --aggressive
```

我们已经组合了所有的命令。你可以把它作为一个别名，每周运行一次，进行一次干净的回购。

别忘了看看。清理您的 repo 后 git 目录大小。

在你机器上的所有 repos 上运行它肯定会帮助你减少一些磁盘空间。

感谢你的阅读，希望你学到了新的东西:)

## 如果你已经来了这么久，那么我想你会对 Git 非常感兴趣。你可以订阅我的时事通讯 [GitBetter](https://gitbetter.substack.com/) 来获得 Git 的技巧、提示和高级主题。