# 针对初学者的 Git 命令行基础

> 原文：<https://levelup.gitconnected.com/git-command-line-basics-for-beginners-2ae2e0768ec8>

## 对于那些过度依赖 Git GUI 客户端和害怕命令行的人也是如此

![](img/f903386373fbefc5d5c20bf48e5c7dff.png)

[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Git 命令行界面(CLI)让我害怕。几个神奇的命令拥有无缝融合和构建美妙作品的力量。在这一套神圣的话语中，还蕴藏着抹去许多人的血汗和眼泪的力量。

我不熟悉不同的 Git 命令，更不用说每个命令可以接受的可选标志了。终端上基于文本的输出对于像我这样的初学者来说并不太舒服。像许多其他人一样，我像躲避瘟疫一样避开它，并采取了更简单的方法。

或许这也是对你的描述——这里没有评判，只有成长的空间。通往命令行战士的道路充满了无尽的困惑和危险的命令。不要害怕从你的 GUI 后面走出来，迈出第一步。

第一步，或者说第一个命令，总是最难的。

我对常用的 Git 命令进行了编译和分类。对于软件工程师日常工作中的大多数用例来说，这应该足够了。

# 建立

我假设您已经设置好了 Git 和 Github 帐户。否则，你可以参考[这里的](https://help.github.com/en/github/getting-started-with-github/set-up-git)了解更多详情。

## 1)创建新的回购并推送到远程

```
1 $ git init 
2 $ git add .
3 $ git commit 
4 $ git remote add origin git@github.com:<GitHub username>/<repo name>.git
5 $ git push -u origin master 
```

步骤 1–3 创建一个新的 Git 存储库，存放所有文件，并提交它们。然而，这些都是在您的本地存储库上完成的。您需要在 Github 上创建一个远程存储库。这可以在第 4 步完成——用您实际的 Github 用户名和您想要的库名替换带有<>的部分。

```
e.g git remote add origin git@github.com:JeremyAw/my-new-repo.git
```

最后，在第 5 步，推动您当地的分支机构。`-u`标志将远程`master`分支设置为上游分支。

## 2)从远程回购克隆回购

```
$ git clone git@github.com:<GitHub username>/<repo name>.gite.g git clone git@github.com:JeremyAw/my-new-repo.git
```

不言自明——用您实际的 Github 用户名和目标存储库名称替换带有<>的部分。

# 创建要处理的分支

在合并之前，从现有分支创建一个新分支来处理您的要素是很常见的。

## 1)从当前分支中分支出来

```
$ git checkout -b <branch name>
```

这将从您当前所在的分支创建一个新分支。但是，它不设置上游分支。

```
$ git push --set-upstream origin <branch name>
```

如果您忘记了这一点并简单地运行`git push`，您将被提示运行带有`— -set-upstream`标志的这个命令。这会将分支推送到远程存储库，并添加一个上游引用。后续调用只需要`git push`。

## 2)从远程分支分支

```
$ git checkout -t <remote>/<remote branch name>
```

这从远程分支创建了一个新分支，并且`-t`标志设置了上游配置。这为你省去运行`git push`的麻烦。

```
e.g
$ git checkout -t origin/my-new-branch
$ <do something>
$ git push
```

如果您希望从远程分支而不是当前所在的分支签出，这可能是最方便的选择。

## 3)从具有不同名称的远程分支中分支出来

```
$ git checkout -b <branch name> <remote>/<remote branch name>
```

这将创建一个新分支，其名称与指定的远程分支不同。它也不设置上游分支。

当您第一次运行`git push`时，它会提示您检查您希望推送至哪个远程分支。

要推送到远程的上游分支:

```
$ git push origin HEAD:<remote branch name>
```

要使用新名称创建一个远程分支并将其推送到那里:

```
git push origin HEAD
```

随后的`git push`命令将提示您这样做，除非您永久设置任一选项。看`git help config`里的 push.default。

# 日常编码要点

这些命令将是你的面包和黄油。

## 1)获取并更新您的分支

```
$ git fetch
```

当未指定遥控器时，默认情况下将使用`origin`遥控器，除非为当前分支配置了上游分支。

在大多数情况下，您当前所在的分支可能配置了一个上游分支。当您运行`git fetch`时，它将只下载那个分支的新变更。

如果您希望从所有远程分支获取更改:

```
$ git fetch origin 
```

如果你碰巧在`origin`旁边有不止一个遥控器:

```
$ git fetch --all
```

获取新的更改后，记得应用它们:

```
$ git pull
```

或者，您也可以跳过`git fetch`并直接运行`git pull`来获取并更新您当前的分支。

## 2)准备您的代码变更

```
$ git add <file path>
```

这为下一次提交准备了特定文件的更改。专业提示:在键入前几个字母后，使用`TAB`按钮来完成您的文件路径。

为了精确控制和微调您希望存放的代码片段:

```
$ git add -p <file path>
```

`-p`标志将您对该文件的代码更改分割成更小的代码块，并让您决定下一次提交应该存放哪个代码块。您将能够看到您的代码添加(绿色)和删除(红色)。终端中也会提示您:

```
$ Stage this hunk [y,n,q,a,d,s,e,?]?
```

在`[]`之间有八个选项，您可以从中选择。只需输入`?`就可以看到这些选项的有用列表。

```
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
s - split the current hunk into smaller hunks
e - manually edit the current hunk
? - print help
```

输入`y`暂存给定的块，或者输入`n`拒绝它。如果给定的块太大，你希望对它有更好的控制，输入`s`将它分割成更小的块。要退出，只需输入`q`。

或者，如果您希望一次性将它们全部转移:

```
$ git add .
```

使用此选项时要小心，因为您可能会意外地存放和提交某些与提交无关的文件！请记住，提交应该很小，而不是跨 20 个文件的代码更改转储。

## 3)提交您的更改

```
$ git commit
```

这将打开 vim 或 nano 编辑器。编写提交消息和描述，并保存以提交。如果您不想提交，只需退出而不保存。

```
$ git commit -m <commit message>
```

为了避免使用编辑器，您可以使用`-m`标志来编写提交消息。不要忘记用单引号或双引号将提交消息括起来，例如`'type something’`或`"type something"`。可以多次调用`-m`标志。每个提交消息将是一个单独的段落。

```
e.g
$ git commit -m "Implement feature" -m "- Added tests" -m "- Updated README"commit 395e90de8g4808726e8a8a5b7786314eaedd7f8c
Author: Jeremy Aw <jeremyinelysium@gmail.com>
Date: Fri Jun 12 00:05:15 2020 +0800 Implement feature - Added tests - Updated README
```

如果您记得前面有用的`git add -p`命令，您会很高兴地发现`git commit`也有一个等价的命令。它允许你做`git add -p`做的事情并立即提交。

```
$ git commit -p
```

`-p`标志将所有文件中的代码变更分割成更小的代码块，供您挑选。输入`y`暂存给定的块，或输入`n`拒绝它。卡住了？输入`?`查看选项的作用。

```
$ git commit -a
```

当然，如果您希望跳过 staging 部分，直接提交所有的更改，那么`-a`标志就是为您准备的。它将为您暂存所有文件更改。但是，这只适用于被修改或删除的跟踪文件。对于未被跟踪的文件，`-a`标志不起任何作用。请注意这一点，尤其是当您创建新文件时！

## 4)推动你的改变

```
$ git push
```

您离发布您的本地代码变更还差两个字。

如果没有上游分支集，只需执行以下操作:

```
$ git push --set-upstream origin <branch name>
```

# 效用

## 1)检查当前分行信息

```
$ git status
```

这将显示您所在的分支。它还向您展示了本地版本和当前 HEAD commit 之间的差异。这还将显示未跟踪文件的列表以及已转移/未转移的文件。

运行这个来快速检查您的分支和文件更改。你不会想被意外推到`master`的。

## 2)存储并删除您的本地更改

```
$ git stash
```

当您尝试切换分支时，您会注意到如果有局部更改，您将无法这样做。但是，您也希望保留这些更改。这就是`git stash`大放异彩的地方。它会将你当前的分支恢复到最近一次提交的状态，并保存你所有的修改。

```
$ git stash list
$ git stash apply <index>
```

您可以根据需要多次重复这个保存更改的过程。当你想应用你的任何改变时，只需做`git stash list`来查看你的存储列表，然后用期望的存储索引运行`git stash apply`。

```
$ git stash pop <index>
```

这类似于运行`git stash apply`,但它会在应用你的更改后移除隐藏。使用这个的时候一定要小心！

## 3)列出可用的分支

```
$ git branch -a
```

这将列出本地和远程分支。

```
$ git branch --list
```

这将只列出本地分支。

```
$ git branch -r
```

这将只列出远程分支。

# 打扫

## 1)修剪局部树枝

```
$ git fetch -p
```

`-p`标志将有助于从已删除的远程分支中清除您的本地分支。如果您没有在您的变更被合并到`master`之后删除您的本地分支的习惯，这是非常方便的。

## 2)删除本地分支机构

```
$ git branch -d <branch name>
```

`-d`标志允许您删除分支。此命令仅适用于设置了上游分支的本地分支。如果未设置上游，请改为运行以下命令:

```
$ git branch -D <branch name>
```

`-D`标志将对本地分支进行强制删除。

## 3)删除远程分支

```
$ git branch -d -r <remote>/<remote branch name>
```

删除远程分支类似于删除本地分支。不同之处在于`-r`标志，它告诉 Git 删除远程分支。

# 关闭

当我获得 Git 的图形用户界面(GUI)替代品时，这是一个显而易见的选择。当您可以对相互交织的分支、提交和更改进行可视化表示时，为什么要将自己局限于终端上的文本输出呢？

我在那里，在 Windows 10 上，愉快地使用 GUI 为我做繁重的工作。我很幸运地不知道最初的 Git 戒律。直到我为了工作不得不切换到 Linux。噗，我熟悉的 Git GUI 不见了。当然，在 Linux 上也有多种选择。

> 但是，为什么不借此机会再尝试一下命令行呢？这是在逆境中成长的机会。

我可能不是最熟练的 Git CLI 用户，但我离掌握日常所需的基本知识又近了一步。我也离摆脱对 Git GUI 的依赖更近了一步。你呢？

我希望这篇文章为你提供了信心和知识，让你**迈出第一步，走出你的 GUI** 。

如果你有任何建设性的反馈或者你发现了我犯的任何错误，请在评论中告诉我！

# 参考

[](https://git-scm.com/docs) [## 参考

### 快速参考指南:GitHub 备忘单|可视化 Git 备忘单

git-scm.com](https://git-scm.com/docs)