# Git CLI —基本命令集

> 原文：<https://levelup.gitconnected.com/git-cli-basic-set-of-commands-ddb91abecb32>

## 一组命令让我走了很长一段路

![](img/24b44bd36313899d68d14c031e34ea90.png)

让我们来解决工作树。照片由 [niko photos](https://unsplash.com/@niko_photos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/tree?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我不记得上次接触 git UI 客户端是什么时候了。一旦你熟悉了 git CLI，这看起来很奇怪。

# 获取有关命令的帮助

```
git command --help
```

也适用于任意子命令:

```
git command subcommand --help
```

# 初始化存储库

在工作目录中初始化 git repo:

```
git init
```

> **注意:**所有 git 的东西都存储在`.git`文件夹中。如果您想重新开始，只需删除`.git`。请注意，与 git 相关的一切都将消失——就好像 git 从未存在过一样。

# 将本地存储库连接到远程存储库

```
git remote add origin repo_url
```

当我开始一个新项目时，我通常的做法是:

1.  在 GitHub 或其他地方创建一个空的存储库
2.  在项目目录中:`git init`
3.  `git add . && git commit -m 'Initial commit'`
4.  `git remote add origin remote_url`(从 GitHub 复制 remote_url)
5.  `git push -u origin master`(仅在第一次推送时需要，`git push`将在所有后续推送中使用)

# 克隆存储库

克隆到当前工作目录:

```
git clone repo_url
```

您可以使用 git hoster(例如 GitHub)提供的 HTTPS 或 SSH 存储库 URL 进行克隆。您选择的那个将被设置为`origin`遥控器的 URL。请参见章节**设置远程 URL** 以便在克隆后在 HTTPS/SSH 之间进行更改。

SSH 的便利之处在于，一旦设置好，就不必一直输入用户名和密码。关于这个话题，我有一篇单独的文章。

[](/ssh-from-cli-common-usages-bcbf7bc98cc9) [## CLI 中的 SSH 常见用法

### 从创建密钥对到复制文件，再到设置端口转发

levelup.gitconnected.com](/ssh-from-cli-common-usages-bcbf7bc98cc9) 

# 获取状态报告

可能是最常用的 git 命令。我在 git 任务之前、之后和之间使用它。它显示了工作树的概要，包括未跟踪的文件、未暂存的变更、暂存的变更、挂起的提交等等。

```
git status
```

短状态:

```
git status -s
```

# 阶段变化

从当前目录递归存放所有更改:

```
git add .
```

在`dir`目录中存放所有更改:

```
git add dir
```

暂存所有以`*.c`结尾的文件:

```
git add *.c
```

# 取消阶段更改

```
git reset HEAD [file_path](https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefpathspecapathspec)
```

# 提交更改

提交阶段性更改:

```
git commit -m 'commit message'
```

暂存修改/删除的文件并一口气提交(不暂存*新的*文件):

```
git commit -am 'commit message'
```

如果您想生成多行提交消息，只需执行`git commit`，这将打开一个编辑器，您可以在其中输入消息。对于较大的提交消息，通常在第一行有一个简短的摘要，然后添加一个空行，并在所有后续行提供更详细的解释。许多 git hosters，如 GitHub、GitLab 或 Bitbucket 解析这样格式的消息，仅在浏览提交历史时显示摘要行，但在进入特定提交时显示详细描述。

# 放弃更改

放弃文件中的更改(即重置为文件的最后提交版本):

```
git checkout -- file_path
```

递归放弃所有文件中相对于工作目录的更改:

```
git checkout -- .
```

您还可以指定文件夹或通配符来选择要丢弃的文件。更多信息见[路径规范](https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefpathspecapathspec)。

# 显示提交

```
git log
```

更改详细程度:

```
git log --prettify=oneline|short|full|fuller
```

图形提交分歧:

```
git log --graph
```

将这一切结合起来:

```
git log --oneline --decorate --graph --all
```

# 推至远程

将所有待定提交推送到远程(使用`git status`列出待定提交):

```
git push
```

要推送到默认遥控器之外的另一个遥控器，`origin`:

```
git push remote_name
```

# 从遥控器拉

从远程获取提交，并将它们合并到当前分支(也将从所有其他分支获取更改，但不合并它们):

```
git pull
```

要从默认遥控器之外的另一个遥控器拉取，`origin`:

```
git pull remote_name
```

# 创建本地分支

```
git checkout -b branch_name
```

从当前分支分叉出一个新分支。

# 创建远程分支

首先创建一个本地分支，然后将其推送到远程，指定远程`branch_name`，如下所示:

```
git push -u origin branch_name
```

如果这个`branch_name`在遥控器上不存在，那么它将被自动创建——不管你把它推到哪个遥控器(GitHub、GitLab、Bitbucket 等等)。)

稍后会有更多关于遥控器的内容。

# 开关支路

```
git checkout branch_name
```

# 显示当前分支

```
git branch
```

# 列出遥控器

一个远程只是一个存放在其他地方的库的 URL，比如在 GitHub 上。一个本地存储库可以有来自许多主机的许多远程设备。例如，您可以通过为 GitHub、GitLab 和 Bitbucket 各添加一个遥控器，从同一个本地存储库中推送它们。

然而，通常的情况是有一个遥控器，它叫做`origin`。当您克隆一个存储库时，`origin` remote 将被自动创建，URL 被设置为克隆的存储库。当您`pull` / `push`或者执行任何其他与远程相关的任务而没有明确指定远程时，git 将默认使用`origin`(如果它存在的话)。这意味着`git pull`与`git pull origin`相同。

您可以通过以下方式列出遥控器:

```
git remote
```

# 添加遥控器

```
git remote add remote_name repo_url
```

例如，当您想要将远程存储库迁移到另一个地方时，这很有用。添加新的遥控器，使用`git push new_remote`推送所有内容，但保持旧的路径/遥控器有效。这样，您仍然可以从旧位置获取提交，并将它们推送到新位置，直到迁移完成。

# 获取远程 URL

```
git remote get-url remote_name
```

# 设置远程 URL

```
git remote set-url remote_name new_url
```

例如，当远程存储库被迁移到另一个地方时，这很有用。或者您想将远程 url 从 HTTPS 改为 SSH，反之亦然。

# 解决冲突

当您从远程位置拉取更改时会产生冲突，在远程位置编辑的行与您在本地编辑的行相同——在这种情况下，git 不知道如何合并这些行，因此需要手动参与。

如何在文本级别上处理 git 冲突比许多人想的要简单得多，因为他们被不可预知的和神奇的 UI 客户端弄糊涂了，这些客户端使冲突处理看起来像一个复杂的例程。

对于每个相交的变更(即远程和本地变更的行)，git 插入一个文本，如( [source](https://git-scm.com/docs/git-merge#_how_conflicts_are_presented) ):

```
<<<<<<< yours:sample.txt
Conflict resolution is hard;
let’s go shopping.
|||||||
Conflict resolution is hard.
=======
Git makes conflict resolution easy.
>>>>>>> theirs:sample.txt
```

在第一部分中，在`<<<`和`===`之间，你会找到当地版本的台词。在第二部分，在`===`和`>>>`之间，你会发现台词的远程版本。

你的工作是编辑这些线条，直到它们正确为止。背后没有魔法。您可以使用任何类型的文本编辑器处理冲突。当文本/代码具有正确的形状时，您只需像往常一样提交/推送您的更改——这就是冲突处理的全部内容。

让我总结一下这个过程:

1.  `git pull`从远程获得冲突通知，使用`git status`查看冲突文件。
2.  打开这些文件，找到`<<<` `===` `>>>`章节；修改它们，直到文件看起来像它应该看起来的样子。
3.  准备变更，例如 git `add .`，提交它们`git commit -m ‘resolved conflicts’`，最后`git push`它们。

# 参考

*   [git 初始化](https://git-scm.com/docs/git-init)
*   [git 克隆](https://www.git-scm.com/docs/git-clone)
*   [git 遥控器](https://git-scm.com/docs/git-remote)
*   [git 状态](https://git-scm.com/docs/git-remote)
*   [饭桶添加](https://git-scm.com/docs/git-add)
*   [git 复位](https://git-scm.com/docs/git-reset)
*   [git 提交](https://git-scm.com/docs/git-commit)
*   [git 结账](https://git-scm.com/docs/git-checkout)
*   [git 日志](https://www.git-scm.com/docs/git-log)
*   [git 推送](https://git-scm.com/docs/git-push)
*   [git pull](https://www.git-scm.com/docs/git-pull)
*   [git 分支](https://git-scm.com/docs/git-branch)
*   [使用遥控器](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
*   [冲突是如何呈现的](https://git-scm.com/docs/git-merge#_how_conflicts_are_presented)