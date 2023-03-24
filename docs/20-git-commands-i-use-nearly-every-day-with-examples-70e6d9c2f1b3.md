# 我几乎每天都在使用的 20 个 Git 命令(有例子)

> 原文：<https://levelup.gitconnected.com/20-git-commands-i-use-nearly-every-day-with-examples-70e6d9c2f1b3>

## git 提交，git 推送，git 支付

![](img/81de93d4c783c5e0764dc8d39dd00f92.png)

从 [Unsplash](https://images.unsplash.com/photo-1577375729152-4c8b5fcda381?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=1100&q=80)

版本控制不过是现代技术堆栈中的一个救命稻草。它跟踪个人或团队对软件项目所做的变更，以便您可以在必要时回滚变更，并记录项目如何发展的历史。我甚至无法想象在 2005 年创建 Git 之前，软件工程师们在几十年前必须面对的困难。

今天，版本控制是软件应用程序的必需品，Git 是最受欢迎的选择之一。虽然有各种基于 GUI 的 Git 版本，如 [GitKraken](https://www.gitkraken.com/) ，但我将坚持使用终端基础，因为这是我个人的饭碗。

> 话虽如此，我的一位经理却持相反意见——他用了我最喜欢的一句口头禅:
> 
> *“没有 GUI，就没有 do-ee。”*

以下是我几乎每天都会用到的 20 个 git 命令，以及它们的用例示例。我从最基础的开始，然后逐步提高。

**注意:**这些命令中的每一个都有可以传递的额外参数，我将涵盖最常见的示例，并将进一步的学习留给读者。:)

## **1。git 配置**

在您开始与 git 源代码控制交互之前，您必须配置 git 以了解您是谁。

```
$ git config --global user.name "Darth Vader"
$ git config --global user.email dvader42@deathstar.com
```

您还可以使用查看您的所有设置及其来源:

```
$ git config --list --show-origin
```

## 2.git 初始化

现在您的设置已经配置好了，我们可以初始化我们自己的 Git 存储库了。这将跟踪本地变化，但它还没有连接到 GitHub 内部的远程存储库。因此，如果这个目录被删除，您的 git 版本历史和所有文件都会消失。

```
$ cd path/to/new/project
$ git init
Initialized empty Git repository in path/to/new/project/.git/
```

## 3.git 克隆

如果我们不想初始化我们自己的本地存储库，而是想从 GitHub 克隆一个现有代码的远程存储库，我们可以使用 git clone。有两种协议可供选择，超文本传输协议安全(HTTPS)或安全外壳(SSH)。

> 不需要太多的细节，HTTPS 是一种快速简单的方法来克隆一个存储库。但是，您每次都需要手动输入密码。通过 SSH 克隆需要用 GitHub 设置您自己的私钥对，但是更安全，并且不需要输入您自己的密码。

```
$ git clone [https://github.com/tensorflow/tensorflow.git](https://github.com/tensorflow/tensorflow.git)
$ git clone [git@github.com](mailto:git@github.com):tensorflow/tensorflow.git
```

## 4.git 状态

无论您是初始化了一个本地 git 存储库还是仅仅克隆了一个远程 repo，您都可以运行`git status`来检查您的工作区的状态。此命令将让您知道是否有尚未保存的更改，或者您的本地版本是否比远程版本旧。

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your **local** commits)

Untracked files:
  (use "git add <file>..." to include **in** what will be committed)

	README.txt

nothing added to commit but untracked files present (use "git add" to track)
```

## 5.获取 git

在每个工作日的开始，我总是跑`git fetch`。这个命令将检索新的分支和对存储库的任何更新，但是它还不会改变您的本地工作区。实际更新本地代码的命令是下一节中的`git pull`。

```
$ git fetch
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5)
Unpacking objects: 100% (43/43), done.
From https://github.com/some/repository
 * [new branch]      master     -> master
 * [new branch]      feature    -> feature
```

## 6.git 拉

> 用`git fetch`检查更新后，您可以在对代码做任何更改之前运行`git pull`。特别是如果你要从一个主要的 master 或者 develop 分支中分支，重要的是你的新分支要使用最新的版本，以防止合并冲突或者意外的问题。

使用`git pull`，您基本上是在运行`git fetch`，然后将新的更改合并到您的本地工作区。您可以指定要提取的分支，也可以不给出其他参数来更新您所在的当前分支。

```
$ git pull
```

## 7.git 添加

对分支进行本地更改后，您需要为提交准备这些更改。您可以使用`git add`通过包含文件名作为参数来单独暂存更新的文件，或者您可以包含标志`-a`来将所有更改的文件添加到暂存中。

```
$ git add -a
```

## 8.git 提交

既然您已经准备好了变更，那么您可以添加一条提交消息来描述变更的目的。一个有用的标记是使用`git commit --amend`，它允许您在之前的提交中添加额外的文件或更改提交消息。

```
$ git commit -m "did X to fix Y"
```

## 9.git 推送

一旦您用提交消息添加了您的更改，就该推送您的代码了！这将采用您的本地更改并更新远程存储库。如果这是您第一次推送到远程分支，通常建议您设置上游——这意味着您需要指定您想要将您的更改推送到的分支。

```
$ git push --set-upstream origin new-branch # first time
$ git push                                  # now can just push
```

## 10.git 差异

如果您想要查看两个分支之间或者您未分级的变更和当前分支之间的差异，您可以使用`git diff`。

```
$ git diff <commit_a> <commit_b>      # diff between two commits
$ git diff <commit_a> <commit_b> file # diff b/w file in 2 commits
$ git diff HEAD # diff between working directory and last commit
```

## 11.git rm

Git 存储库将识别何时对它所跟踪的文件执行了常规 shell `rm`命令。它将更新工作目录以反映删除。它不会用删除来更新暂存索引。必须对删除的文件路径执行额外的`git add`命令，以将更改添加到暂存索引中。

`git rm`命令作为一种快捷方式，它将使用删除更新工作目录和暂存索引。

```
$ git rm README.txt
```

## 12.git 分支

分支是版本控制最重要的功能之一。通过使用分支，你可以委派职责，比如一个分支负责修复 UI，另一个分支负责增加测试，或者添加新的特性等等。注意，当您运行`git branch`时，它将从您当前的分支克隆。因此，如果您想从主分支开始新的分支，请确保在分支时您在主分支上。

```
$ git branch <new branch name>
```

## 13.git 检验

在创建一个新的分支之后，您不会自动切换到它——您需要签出那个分支。您还可以签出特定的提交，以进入代码的旧状态。如果你想获得最新信息，只需运行`git checkout HEAD`。

```
$ git checkout <new branch name>
$ git checkout <commit name>
$ git checkout HEAD
```

## 14.git 合并

合并两个分支将它们的集体变化合并在一起。您所在的分支将成为目标分支。将 master 合并到您的特性分支中可以更新团队所做的任何更改，而将您自己的新特性分支合并到 master 中会将您的分支的更改添加到主源分支中。

```
$ git merge master       # merge master into new-feature
$ git checkout master    # checkout the master branch
$ git merge new-feature  # merge new-feature changes into master
```

## 15.git 日志

如果您想要快速查看最近的提交，您可以使用添加了标志`--oneline -N`的`git log`，其中`N`是您想要列出的提交数量。如果你想要更多的细节，你可以去掉`--oneline`标志。

```
$ git log --oneline -10
a920b394 (HEAD -> database) query database for user creds
27228a0d (origin/database) add database connections
75a9a3be stubbing out code
26d06eb2 (origin/develop, origin/HEAD, develop) Merge branch 'user-store' into 'database'
```

您还可以按提交的作者、时间等进行过滤。

```
$ git log --author <name>
$ git log --after 2.days.ago
$ git log --before 3.days.ago
```

## 16.git show

为了更深入地了解存储库的变化，您可以使用`git show`。您可以列出一次提交中更改的所有文件(第一个命令)，或者显示给定提交范围内的所有提交(第二个命令)。

```
$ git show --pretty="" --name-only ab83kf92
$ git show commitA...commitE
```

## 17.git 贮藏

如果您已经对一个分支进行了更改，并且需要保存它们以便您可以在其他地方工作，您可以使用`git stash`。如果您的更改还没有准备好提交，这是一种保存更改的便捷方式。

```
$ git status
On branch main
Changes to be committed:

    new file:   hashing.go

Changes not staged for commit:

    modified:   main.go

$ git stash
Saved working directory and index state WIP on main: 305f82m new hashing algorithm
HEAD is now at 305f82m new hashing algorithm

$ git status
On branch main
nothing to commit, working tree clean
```

如果您想让您的更改回到您的工作区，您可以使用`git stash pop`或`git stash apply`。弹出会清除保存在您的存储中的任何内容，而应用会保持存储可用，即使您已经将更改应用回您的工作中。

```
$ git stash pop
On branch main
Changes to be committed:

    new file:   hashing.go

Changes not staged for commit:

    modified:   main.go

Dropped refs/stash@{0} (dk3b28fm990mlm453m5m10al0029bf3e380cc6a)

$ git stash apply
On branch main
Changes to be committed:

    new file:   hashing.o

Changes not staged for commit:

    modified:   main.go
```

## 18.git 重置

如果您做出了不想保留的更改，您可以使用`git reset`将您的工作空间恢复到之前的提交。您可以传递标志`soft`、`mixed`或`hard`，这取决于您希望复位的强制程度。

对于软件，转移的快照和工作目录不会更改。Mixed 将更新对转移快照的指定提交，但工作目录不受影响。硬重置将更新快照和工作目录，并且可能有潜在的危险，因为您将不再能够访问以前的任何更改。

```
$ git reset HEAD-2 hashing.go # reset to 2nd-to-last commit
```

## 19.git 遥控器

`git remote`命令允许您修改到其他 git 存储库的连接。您可以将远程连接视为书签，因为它们不是真正的直接链接，而是对其他 URL 的引用。

您可以列出 git repo 的远程配置，添加另一个 repo 作为连接，然后通过指定的名称引用该连接。

```
$ git remote -v
$ git remote add <name> <url>
$ git remote rm <name>
$ git remote rename <old-name> <new-name>
```

我最喜欢的远程命令之一是当我有一堆本地分支不再在远程存储库中时使用的。以下命令将删除任何不再位于远程存储库中的本地分支。

```
$ git remote prune origin
```

## 20.干净利落

最后，我们有`git clean`，它可以高度补充`git reset`和`git checkout`。您可以使用 git clean 来修改工作区中未被跟踪的变更(也就是在您使用`git add`之前)。第一个命令将列出 git clean 时要删除的文件。然后，您可以使用`git clean -f`来删除文件。请注意，除非 clean.requireForce 配置设置为 false，否则必须使用-f 标志。

```
$ git clean -n
Would remove untracked_file
$ git clean -f
Removing untracked_file
```

我的名单到此为止！你认为这里应该有什么 git 命令吗？如果有，请在下面留下评论！感谢阅读。=)