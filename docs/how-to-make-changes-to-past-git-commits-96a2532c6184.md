# 如何对过去的 Git 提交进行更改

> 原文：<https://levelup.gitconnected.com/how-to-make-changes-to-past-git-commits-96a2532c6184>

## 你不必总是承诺你的承诺

![](img/8e937e004b7ff0075683ce773cbb818a.png)

由 [@ilumire](https://unsplash.com/@ilumire) 在 [Unsplash](https://unsplash.com/photos/kabtmcdcAbk) 上的基础图片

Git 是软件开发人员最重要的工具之一。它提供了一个清晰的、可追溯的代码随时间演变的历史。Git 对于代码审查也是必不可少的，因为它提供了代码变更的清晰视图。保持 git 提交最少、清晰和简洁有助于更好地跟踪变更，并使代码审查更容易。

然而，我们的开发人员经常搞砸我们的提交，这是非常诱人的回去改变他们。在这里，我们将看一堆这样的场景，并讨论如何处理它们。

## 合理的警告

本文中的很多例子都是关于更改提交历史的。请注意，如果您在进行这些更改之前已经推送至远程服务器，那么远程服务器将拒绝您的下一次推送。

在这种情况下，您可以使用`git push -f origin/branch-name`进行强制推送。但是，你必须确定在用力推之前没有人已经拉你的树枝。这会给他们带来问题。如果您已经将分支推送到远程服务器，那么最好再提交一次。这适用于这里的所有例子。

如果你想更多地了解压力会导致什么问题以及替代方法，这里的是马修·格罗夫斯[](https://medium.com/u/f96aa4ea1a98# 23——为什么要避免强迫</h2><div class=)

### [上一次，我写了一个管理活动请求代码的简便方法。本周，我想分享一些注意事项…](https://medium.com/u/f96aa4ea1a98# 23——为什么要避免强迫</h2><div class=)

[matthew-b-groves.medium.com](https://medium.com/u/f96aa4ea1a98# 23——为什么要避免强迫</h2><div class=)

# 我在最后一次提交时遗漏了一些东西

我经常发现自己在最后一次提交时打错了字，或者忘记在提交消息中添加一些重要信息。类似地，我也遇到过忘记在最后一次提交时添加/存放文件的情况。

如果你发现自己处于类似的情况，那么你可以使用命令`git commit --amend`。这就像一个普通的提交，除了这将修改或添加新的更改，而不是创建一个新的。

以下是两个需要更改上次提交的常见示例:

## 我在最后一条提交消息中打错了一个字

首先，让我们看一下我们所有的提交。

```
> git log --format=oneline
025d473ccd5cbde8894f83aeb8e78a59c37fee4d (HEAD -> main) commit 5
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

这里的最后一次提交，`commit 5`实际上应该是`commit 4`。我们可以使用下面的命令来解决这个问题。

```
> git commit --amend -m 'commit 4'
```

现在，如果我们查看日志，我们可以看到提交消息已经被更改。

```
> git log --format=oneline
d62d1512c53e78763860a04a54a2491590392ded (HEAD -> main) commit 4
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

## 我在最后一次提交时错过了一个更改

首先，添加/暂存更改，就像您对新提交所做的那样。

```
git add file-you-missed
```

现在，提交更改，但是添加`--amend`标志。

```
git commit --amend
```

这将把您的更改添加到上次提交中，而不是创建一个新的。

# 我需要撤销上一次提交

我也发现自己完全需要撤销上一次犯下的错误。有时提交没有意义，有时我只是将事情提交到错误的分支。

当我在一个未提交工作的分支上工作，并且需要切换到另一个分支时，我也有意地使用它。在切换到另一个分支之前，我在当前分支上执行`git commit -m "temp commit"`来保存我的工作。是的，我可以用`git stash`来做这个，但是我发现这样更方便，因为这样的话变更就在同一个分支中了。

这种情况下的提交历史如下所示:

```
> git log --format=oneline
66863cbd1d0bdf7644656b7cd30ba5093889ea48 (HEAD -> feature-branch) temp commit
d62d1512c53e78763860a04a54a2491590392ded commit 4
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

当我稍后回到分支时，我显然需要删除该提交。我使用下面的命令来撤销之前的提交。

```
> git reset HEAD~
```

如果我们查看日志，我们可以看到提交已被撤消。

```
> git log --format=oneline
d62d1512c53e78763860a04a54a2491590392ded (HEAD -> feature-branch) commit 4
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

这里，`HEAD`指的是你当前分支的尖端。这个命令告诉 git 重置`HEAD`提交，这是当前分支末端的提交。

然而，这并没有删除我在最后一次提交中所做的更改，而是从提交中删除了它们，这样我就可以继续处理它们。这在我描述的需要切换分支的场景中非常有用。

如果您想完全删除更改，可以使用以下命令:

```
> git reset --hard HEAD~
```

## 我需要撤销多个提交

您也可以通过在波浪符号(`~`)后添加要撤销的提交数量来撤销多个提交。这告诉 git 从当前分支的`HEAD`或 tip 中删除那么多提交。

```
git reset HEAD~1 // Undoes last commit
git reset HEAD~2 // Undoes last 2 commits
git reset HEAD~3 // Undoes last 3 commits. You get the idea
```

# 我想删除我的本地更改并同步到远程分支

有时，我在本地分支上进行多次提交，然后意识到我需要删除我的更改，只需同步回远程服务器上的内容。如果您也发现自己处于这种情况，您可以使用以下命令:

```
git reset --hard origin/branch-name
```

这会将本地分支重置为远程服务器上的版本。注意这一点，因为您将丢失在远程服务器上最后一次提交后所做的本地更改。

这在您忘记从主分支分支并向主分支本身添加提交的情况下尤其方便。我经常遇到这种情况。

在这种情况下，您可以`git branch new-branch`在新的分支中获得您的提交，然后`git reset --hard origin/main`将主分支重置为 remote/origin 上的版本。

# 我想恢复重置

所以，你听从了我的建议，硬重置到一个远程分支，但你意识到你实际上需要那些提交回来？

假设我们有这个提交历史。

```
> git log --format=oneline
d62d1512c53e78763860a04a54a2491590392ded (HEAD -> main) commit 4
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

但是我们将分支重置为原点上的版本。

```
> git reset --hard origin/main
HEAD is now at 97ae8ee commit 3> git log --format=oneline
97ae8ee86d41d7610634722bea4c5500d07e5545 (HEAD -> main) commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

现在，我们意识到我们实际上在`commit 4`有一些代码可以使用。

嗯，这也有一个解决方案！用`git reflog`就行了。这个命令将给出`HEAD`改变的时间列表。`HEAD`创建和恢复提交和签出分支时的变化。

```
> git reflog
97ae8ee (HEAD -> main) HEAD@{0}: reset: moving to HEAD~
d62d151 HEAD@{1}: commit: commit 4
...
```

然后我们可以运行`get reset --hard <hash_from_reflog_list>`回到过去。在这个特殊的例子中，我们需要使用散列值`d62d151`返回到`commit 4`。这就是我们如何回到那个状态。

```
> git reset --hard d62d151
HEAD is now at d62d151 commit 4
```

现在，如果我们查看日志，我们可以看到我们又回到了提交状态。

```
> git log --format=oneline
d62d1512c53e78763860a04a54a2491590392ded (HEAD -> main) commit 4
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

reflog 可用于恢复对`HEAD`所做的几乎所有本地更改。但是，git 会定期清除这个日志。所以，不要依靠这个回到过去的状态。此外，这仅适用于局部更改。

# 我在过去的提交中遗漏了一些东西

我们已经了解了如何使用`git commit --amend`来更改提交消息或者将丢失的文件添加到最后一次提交中。然而，也有这样的情况，我们可以在提交之前添加一些东西或者使用不同的消息。

下面是两个需要这样做的常见例子:

## 我在过去的提交消息中打错了一个字

这可能会有点复杂，所以让我们把它分成几个步骤。

1.确定要重命名的提交

让我们查看日志，以确定哪个提交需要重命名。

```
> git log --format=oneline
d62d1512c53e78763860a04a54a2491590392ded (HEAD -> main, branch2) commit 4
97ae8ee86d41d7610634722bea4c5500d07e5545 commit 3
3d6215f53c01a95e09f21707f02f9ad29d8156b6 commit 2
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

我们已经确定，出于某种原因，我们希望将`commit 2`重命名为`commit 2 changed`。为此，我们需要在`commit 2`之前获得一些提交的散列。在这种情况下，那就是`commit 1`。

2.开始一个交互式的 rebase

然后我们需要运行`git rebase -i <hash_of_commit_1>`。这将打开一个编辑器，其中包含我们所针对的提交之后的所有提交。编辑器的内容会是这样的:

```
pick 3d6215f commit 2
pick 97ae8ee commit 3
pick d62d151 commit 4
```

正常的 rebase 标识了我们当前分支和我们指定的目标提交或分支之间的共同祖先。在这个例子中，共同的祖先是`commit 1`本身。然后，它在该提交之上重新应用来自当前分支的所有进一步的提交。

Rebases 通常与分支一起使用。然而，我们也可以将`rebase`直接用于提交散列。

当我们使用来自我们所在的同一个分支的提交来重新确定基础时，它将简单地重新应用目标提交之后的所有提交。这与`-i`标志相结合，使得在当前分支中修改过去的提交成为可能。

`-i`标志将开始一个交互式的重置基础。交互式 rebase 允许我们重新安排受 rebase 影响的提交，甚至选择对每个提交执行的操作。其中一项行动是`reword`。

3.告诉 git 您想要改写提交消息

现在，我们需要把`*pick* 3d6215f commit 2`改成`*reword* 3d6215f commit 2`。不，在这里更改提交消息将不起作用。这里的消息只是为了让用户更容易识别提交。在我们将所有想要重命名的提交的`pick`更改为`reword`之后，我们需要保存文件并退出。

```
reword 3d6215f commit 2
pick 97ae8ee commit 3
pick d62d151 commit 4
```

4.更改提交消息

Git 将为我们运行所有的提交，当它到达任何有命令`reword`的提交时，它将给我们一个机会来更改提交消息。重定基础过程完成后，我们可以查看日志来查看更改。

```
> git log --format=oneline
201238ccd35d33dc481031bc935245cc367d0133 (HEAD -> main) commit 4
13bfa0c56b59b87a40fc2d87b17c9511bf070539 commit 3
e021011d384ddaca6562e16328888304797fa6ef commit 2 changed
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

## 我错过了过去提交中的更改

这几乎和更改提交的名称一样复杂。所以，让我们再一次把它分成几个步骤:

1.标识要添加更改的提交

让我们查看日志，以确定需要添加哪个提交。

```
> git log --format=oneline
201238ccd35d33dc481031bc935245cc367d0133 (HEAD -> main) commit 4
13bfa0c56b59b87a40fc2d87b17c9511bf070539 commit 3
e021011d384ddaca6562e16328888304797fa6ef commit 2 changed
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

假设我们想将丢失的文件提交给`commit 2 changed`。

2.使用`--squash`提交丢失的文件

我们现在必须使用`git commit --squash <commit-to-amend-changes-to>`提交我们的更改。在这种情况下，我们必须使用`commit 2 changed`的 hash。这将创建一个新的提交。

```
> git add missed-change-file> git commit --squash e021011d384ddaca6562e16328888304797fa6ef
[main 51874fe] squash! commit 2 changed
 1 file changed, 1 insertion(+)
 create mode 100644 missed-change-file
```

查看日志应该会向我们展示在顶部创建的新提交。

```
> git log --format=oneline
51874fe7a64856c2fa0dbd6327649ccb414ce7fb (HEAD -> main) squash! commit 2 changed
201238ccd35d33dc481031bc935245cc367d0133 commit 4
13bfa0c56b59b87a40fc2d87b17c9511bf070539 commit 3
e021011d384ddaca6562e16328888304797fa6ef commit 2 changed
ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b commit 1
```

是的，这并没有增加我们的目标承诺，但我们将在下一步中做到这一点。

3.用目标提交挤压新提交

现在我们需要做的是将新的提交移到`commit 2 changed`之后，然后将这两个提交放在一起。挤压就是将两次提交合并为一次提交的行为。

幸运的是，git 可以自动为我们完成这项工作，因为我们使用`commit --squash`标记了自动压缩的提交。我们首先需要在`commit 2 changed`之前获得一些提交的散列。在我们的例子中，我们可以使用`commit 1`。然后我们可以运行`git rebase --autosquash -i <hash_of_commit_1>`。

```
git rebase --autosquash -i ecec4946e855fe26bc88dc8e1e66bd0a26d81b9b
```

这将打开一个编辑器，我们的新提交被移动到`commit 2`附近，而不是原来的位置。这就是我们使用`--autosquash`的原因。

```
pick e021011 commit 2 changed
squash 51874fe squash! commit 2 changed
pick 13bfa0c commit 3
pick 201238c commit 4
```

如果我们没有指定`--autosquash`标志，提交的顺序会有所不同。我们现在可以简单地保存并关闭编辑器。

我们可以通过使用交互式 rebase ( `-i`)而不使用`--autosquash`来手动完成，然后移动提交。不过，`--autosquash`只是更方便而已。

4.让提交发生

Git 现在将遍历列表中的所有提交。当它到达我们挤压的提交时，它将提示我们输入一个提交消息。如果需要，我们可以在这里更改提交消息。

如果我们希望保留原始的提交消息，并且不被提示输入，那么我们可以在步骤 2 中使用`--fixup`而不是 squash。不过，`rebase`还是会用`--autosquash`。这对挤压和修正都有效。没有叫`--autofixup`的旗。

# 参考资料:

*   Git Rebase 文档:[https://git-scm.com/docs/git-rebase](https://git-scm.com/docs/git-rebase)
*   Git 提交文档:[https://git-scm.com/docs/git-commit](https://git-scm.com/docs/git-commit)
*   Git Reflog 文档:[https://git-scm.com/docs/git-reflog](https://git-scm.com/docs/git-reflog)