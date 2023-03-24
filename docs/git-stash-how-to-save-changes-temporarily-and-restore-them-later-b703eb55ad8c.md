# Git Stash:如何临时保存更改并在以后恢复它们

> 原文：<https://levelup.gitconnected.com/git-stash-how-to-save-changes-temporarily-and-restore-them-later-b703eb55ad8c>

![](img/a89336aee1b63143fe0fffcc90f49c2d.png)

安德鲁·德雷珀在 [Unsplash](https://unsplash.com/photos/ZjJd4jXN_G0) 上拍摄的[照片。](https://unsplash.com/@andalexander)

也许你曾经有过这样的经历:愉快地编写代码，开发一些闪亮的新功能——然后电话铃响了，或者一封电子邮件弹出来，有一个重要的客户请求等不及了，或者，更糟糕的是，一个讨厌的 bug 阻止了一个应用程序的工作。无论如何，是时候中断你正在做的事情，处理当前的情况了。另一方面，您还没有准备好提交尚未完成的更改。你如何能得到一个干净的工作副本？Git 是来帮忙的:见见`git stash`，一个你最喜欢的版本控制系统的剪贴板。

隐藏功能真的很酷。它会暂时保存代码更改，并清空您的工作目录以便重新开始。在以后的任何时候，您都可以恢复或放弃这些更改，您可以将它们重新应用到一个不同的分支，甚至可以基于这些更改创建一个新的分支。本文对`git stash`进行了介绍，并展示了如何避免混乱的 Git 情况——是时候清理一下了。

# 何时使用 Git Stash

那么，什么时候是在 Git 中创建存储库的最佳时机呢？嗯，一般的答案是:当你需要一个干净的工作副本的时候。不过还是具体一点吧。创建一个新的贮藏处是一个好主意，强烈推荐

*   在你检查不同的分支之前
*   在您对本地回购进行远程更改之前
*   在合并或重置分支之前
*   在你挑选提交之前

Git 很聪明，所以它有时会告诉您进行清理，并建议您隐藏您的更改:

```
error: Your local changes to the following files would be overwritten by merge: css/agency.cssPlease commit your changes or stash them before you merge.
```

上面的错误消息告诉您文件 *agency.css* 已经被本地修改，同时，您试图在*中合并的分支*也包含该文件中的更改——典型的合并冲突。因此，让我们解决这个问题，并隐藏本地更改。

# 创建一个新的 Git 仓库

要记录你工作的当前状态，包括 Git 索引，只需输入`git stash push`。或者，如果您想在没有进一步参数的情况下调用命令，您可以使用`git stash`。这将保存所有本地修改并恢复工作目录以匹配头提交:

```
$ **git stash**
Saved working directory and index state WIP on staging: e3c11da Changes date
```

您可以看到 Git 在<分支名称> (“工作进行中”)上将存储保存为 *WIP，但是您可以随意输入您自己的消息，以便在以后记住这些更改:*

```
$ **git stash push -m “Working on a new layout”** Saved working directory and index state On staging: Working on a new layout
```

默认情况下，`git stash push`将保存您工作副本中的所有更改。如果您想保存单个文件，请输入其名称:

```
$ **git stash push -m "modifies the CSS" css/agency.css**
```

***注意:*** *您可以根据自己的需要创建任意多的 stages——没有像操作系统的标准剪贴板那样的限制。此外，一个藏匿点并不局限于某个分支。当您稍后恢复它时，所有的更改都将应用到当前的 HEAD 分支，不管它在哪里。*

创建新的存储时要记住的是:`git stash push`可以选择包含未跟踪的文件，而不必先暂存它们( *-u* 或*-include-un tracked*)。请小心，因为隐藏未跟踪的文件可能会删除被忽略的文件和文件夹。将未跟踪的文件包含在您的存储中会消除明确忽略的文件或文件夹。例如，如果您定义了忽略文件夹的规则( */old-website/** )，则在隐藏未跟踪的文件后，该文件夹将被删除。另一方面，如果规则是 */old-website* ，Git 不会清除它。包含来自*的文件和文件夹。忽略*，使用 *-a* 或*-所有*。

# 列出和检查 Git 仓库

正如我之前提到的，创建一个以上的存储是可能的。您可以使用`git stash list`列出所有现有的仓库:

```
$ **git stash list** stash@{0}: On staging: Adjusts the layout/CSS
stash@{1}: WIP on staging: e3c11da Changes date
```

要检查一个特定的藏匿点并了解更多关于它和它的内容，您可以使用 *show* 选项。默认情况下，`git stash show` 会呈现*藏毒的详情@{0}* ，最新的条目。为了检查另一个，请指定其名称:

```
$ **git stash show stash@{1}**about.html | 4 ++ —
about_en.html | 4 ++ —
index.html | 1 +
index_en.html | 1 +4 files changed, 6 insertions(+), 4 deletions(-)
```

接下来，让我们看看如何恢复或解除您的存储。

# 应用和删除 Git 存储

要重新应用一个存储，即将它的更改恢复到您的工作副本，使用命令`git stash apply`。同样，如果有多个存储，您可以指定一个名称。如果不输入名字，Git 会应用栈顶( *stash@{0}* )。

```
$ **git stash show stash@{3}** about.html | 4 ++ —
about_en.html | 4 ++ —
index.html | 1 +
index_en.html | 1 +4 files changed, 6 insertions(+), 4 deletions(-) $ **git stash apply stash@{3}** On branch staging
Changes not staged for commit:
  (use “git add <file>…” to update what will be committed)
  (use “git restore <file>…” to discard changes in working directory) modified: about.html
    modified: about_en.html
    modified: index.html
    modified: index_en.htmlno changes added to commit (use “git add” and/or “git commit -a”)
```

正如你所看到的，`git stash apply`命令列出了你当前工作副本在应用了存储之后的状态——你回到了开始的地方，可以继续你的工作。

也许你在问自己是否有可能从一个存储中应用一个单独的文件。答案是“是”——使用 *checkout* 命令，定义存储和文件名:

```
$ **git checkout stash@{0} css/alternative.css** Updated 1 path from 4a4847a
```

**注意:**用*应用*命令应用一个存储并不会将其从列表中删除。现在，您有两个选项来处理这个问题:您可以调用 *git stash pop* 而不是`git stash apply`，这将从堆栈中移除 stash 并一次性应用它，或者您可以使用`git stash drop <name>`在事后进行清理。

当你放下一个藏匿点时，确保指定正确的名称。或者，你可以用`git stash clear`彻底清理并删除所有的隐藏。

# 从贮藏处创建新的分支

有时候，为一些隐藏的更改创建一个新的分支是有意义的，例如，当您同时修改了一些文件，然后试图应用一个隐藏时。如果运行`git stash push`的分支有太多的变更，Git 将报告合并冲突。解决这个问题的一个方法是运行`git stash branch`并输入一个名字:

```
$ **git stash branch new-layout**Switched to a new branch ‘new-layout’
On branch new-layout
Changes to be committed:
  (use “git restore — staged <file>…” to unstage)
    new file: css/alternative.cssChanges not staged for commit:
  (use “git add <file>…” to update what will be committed)
  (use “git restore <file>…” to discard changes in working directory)
    modified: about.html
    modified: about_en.html
    modified: index.html
    modified: index_en.htmlDropped refs/stash@{0} (d2861033a9a519a220a309f46fb9ab1ba4556cbe)
```

Git 创建一个新的分支，检查创建存储时的提交，在那里重新应用您的工作，如果一切顺利的话，最后删除存储。结果，原来隐藏的状态被恢复，没有任何冲突。

# 使用 Git 提高工作效率

存储非常有用——这只是 Git 中众多强大特性中的一个！如果你想升级你的 Git 游戏，网上有很多免费资源:

*   Git 的[急救包是一组解释如何在 Git 中撤销错误的视频短片。](https://www.git-tower.com/learn/git/first-aid-kit?utm_source=medium&utm_medium=guestpost&utm_campaign=guide-to-git-stash)
*   Git 备忘单有助于记住最重要的命令和参数。

黑客快乐！