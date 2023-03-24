# 有用的 Git 命令列表

> 原文：<https://levelup.gitconnected.com/list-of-useful-git-commands-c3a514fbc46e>

![](img/fea980c2329a1dfb28027185ce846871.png)

照片由[степангалаааев](https://unsplash.com/@sgalagaev?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Git 是世界上最流行的版本控制系统之一。通过让我们与变更历史和恢复代码进行交互，它为我们省去了很多麻烦。

在本文中，我们将看到 Git 可以帮助我们处理一些我们可能忽略的命令的更多方式。

# 查找分支

我们可以通过运行`git branch --contains <commit>`找到具有给定提交的分支

# 修改最近的提交

我们可以使用`git commit --amend`来修改最后一次提交。

这样，我们可以更改提交消息或上次提交的文件。

或者我们可以添加`--no-edit`选项来修改最后一次提交，而不改变最后一次提交消息。

# 选择要交互提交的文件

我们可以使用`git add -p`命令交互地选择要添加到 out commit 中的文件。

`-p`是`--patch`的简称。

# 选择要交互隐藏的文件

我们可以使用`git statsh -p`命令来选择我们想要隐藏的文件。

同样`-p`是`--patch`的简称。

# 隐藏未跟踪的文件

命令让我们隐藏未被跟踪的文件。还有做同样事情的`-a`或`--all`选项。

# 还原选定的文件

为了让我们有选择地恢复文件，我们可以使用`git checkout -p`命令，也就是`-p`代表`--patch`，让我们有选择地恢复已经更改的文件。

# 切换到上一个分支

我们可以使用`git checkout -`命令切换到之前的分支。

`-`是上一个分支的别名。

# 还原所有本地更改

`git checkout .`命令让我们恢复所有本地更改。

# 显示更改

我们可以使用`git diff --staged`命令来比较阶段化的变更和当前提交的变更。

# 在本地重命名分支

要在本地重命名分支，我们可以运行:

```
git branch -m old-branch-name new-branch-name
```

将当前分支的名称从`old-branch-name`更改为`new-branch-name`。

# 远程重命名分支

我们可以通过运行以下命令来重命名重命名远程分支:

```
git push origin :old-branch-name
git push origin new-branch-name
```

第一个命令删除带有`old-branch-name`的旧分支，第二个命令将带有`new-branch-name`的新分支推送到我们的远程 Git 服务器。

# 一次打开所有有冲突的文件

我们可以通过运行以下命令打开所有有冲突的文件:

```
git diff --name-only --diff-filter=U | uniq  | xargs $EDITOR
```

`— diff-filter=U`过滤掉所有没有冲突的文件。

# 查找从给定日期开始发生更改的文件

Git 有一个`whatchanged`命令，让我们找到给定时期的文件。我们可以通过键入以下命令来运行它:

```
git whatchanged —-since=‘3 weeks ago’
```

然后 Git 将向我们显示自 3 周前以来更改过的所有文件。

# 从上次提交中删除文件

我们可以使用`rm`命令删除旧文件，然后运行`git commit --amend`修改最后一次提交来删除文件。

所以我们跑:

```
git rm —-cached file-to-remove
git commit —-amend
```

一个接一个地删除文件`file-to-remove`，然后运行`git commit —-amend`修改最后一次提交，从提交中删除文件。

# 用远程覆盖本地

我们可以使用`git reset --hard origin/<branch_name>`用远程存储库中的文件覆盖本地文件。

这将把历史记录重置为远程存储库中的内容。

![](img/205673a3232c4d7c36d121bd06807c15.png)

照片由 [Mael BALLAND](https://unsplash.com/@mael_bld?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 删除所有未跟踪的文件

我们可以运行`git clean -d -n`来做一次演习，看看哪些文件将被删除，如果我们对更改满意的话，可以运行`git clean -d -f`来进行删除。

# 存储更改

如果我们从一个远程存储库中提取变更，而它与我们在本地存储库中的内容冲突，我们就会隐藏变更。

隐藏是临时存储修改过的跟踪文件。

为了保存更改，我们运行`git stash save`。然后，一旦我们完成了远程提取，我们就可以运行`git stash pop`来恢复隐藏的更改。

# 结论

有很多我们可能没有用过的命令可以使用。因此，这些将有助于改善我们的 Git 工作流程。