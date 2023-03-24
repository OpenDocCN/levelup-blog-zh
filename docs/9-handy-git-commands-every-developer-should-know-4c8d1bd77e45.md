# 每个开发人员都应该知道的 9 个方便的 Git 命令

> 原文：<https://levelup.gitconnected.com/9-handy-git-commands-every-developer-should-know-4c8d1bd77e45>

## 使用这些 Git 命令使您的开发之旅变得简单

![](img/87861f35b3dd21b77c405721077f67ad.png)

Matthieu Bühler 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Git 是最常用的版本控制系统之一(VCS)。Git 允许开发人员在一个类似的项目上同时工作，并跟踪任何一个开发人员所做的代码更改。

在 Git 的帮助下，我们可以在应用程序开发生命周期中跟踪代码中的更改，恢复代码中以前所做的更改，并分析代码。这就是 Git 对开发人员的帮助。

在本文中，我们将讨论一些最常用的 Git 命令，每个开发人员或任何与编程相关的人都应该知道这些命令。我们开始吧！

# 1.Git 克隆

此`git clone`命令用于克隆(复制)远程/目标存储库。您可以使用此命令将存储库从远程位置克隆到您的本地计算机或任何其他远程位置。

> 语法:git 克隆

# 2.Git 初始化

这个`git init`命令允许您创建一个新的空白存储库。`git init`命令将当前工作目录代码转换成 Git 存储库。

> 语法:git init 或 git init

这个命令有两种用法-

i. `git init`:这将把您当前的工作目录转换成一个 Git 存储库。
二。`git init <directory>`:这将给定路径中的一个目录转换成 Git 存储库。

# 3.Git 检验

这个`git checkout`命令允许您在存储库中的分支之间切换。我们可以使用`git fetch && git checkout <branchname>`在分支之间切换，并将给定分支的代码提取到您的本地系统或您正在工作的任何其他远程位置。

`git checkout`和`git clone`的区别在于，clone 用于从远程存储库中获取代码，而 checkout 用于在本地系统上已经存在的两个代码版本之间切换。

> 语法:git checkout<branch name=""></branch>

# 4.获取 Git

这个`git fetch`用于从目标分支获取更新到当前分支。`git fetch`命令从目标分支获取不存在于您的分支中的提交，并将它们存储在您的本地存储库中。

但是，它不会将这些更新的更改与您当前的分支合并。该命令使您的分支与目标分支保持同步。

> 语法:git fetch <branch url=""><branch name="">或 git fetch origin master</branch></branch>

i) `git fetch <branch URL><branch name>`:从特定分支获取内容。

ii) `git fetch origin master`:从主分支获取内容。

# 5.Git 拉

这个`git pull`命令用于从远程存储库中获取数据/代码，然后在本地系统中更新它。该命令分两步执行:

I)它首先运行`git fetch`命令，并从远程存储库下载数据。然后，它执行`git merge`命令，用远程存储库的内容更新本地存储库的内容。

> 语法:git pull

# 6.Git 添加

使用`git add`命令，我们可以将文件内容添加/移动到 git **暂存区**。staging area 是 git 中的一个位置，在将文件内容提交到本地存储库之前，您可以在这里为 git 准备文件。

在将内容提交到本地存储库之前，可以多次使用该命令。当代码/内容准备好后，我们将它提交给分支机构。

> 语法:git add

我们可以使用`git add <File name>`命令添加选择文件。

或者我们可以使用`git add -all`命令将所有文件添加到暂存区。

# 7.Git 状态

`git status`命令用于显示当前工作目录的状态。这个命令显示了工作存储库和登台区的状态。

显示`git add`和`git commit`命令之间的状态。使用这个命令，我们可以看到文件中被跟踪和未被跟踪的变更。

> 语法:git 状态

当分支中没有要提交的更改时，我们会看到上面的消息。

# 8.Git 提交

这个`git commit`命令用于捕获存储库中变更的快照。在 git 中，术语**提交**用于保存更改。该命令不会将更改保存到远程存储库，而是保存到本地存储库。

> 语法:git commit -m "提交消息"

在 git 中，`commit`命令紧接在`git add`命令之后使用。Commit 命令用于将更新的文件内容从临时区域提取到存储库。

# 9.Git 推送

这个`git pull`命令用于将内容从本地存储库发布到远程存储库。使用这个命令，我们可以将提交从本地存储库转移到远程存储库。

> 语法:git 推送

# 结论

这就是这篇文章的全部内容。我们已经讨论了一些在应用程序开发生命周期中常用的 Git 命令。开发人员和程序员可以在处理项目时使用这些命令来协作处理代码。

您可以在 Git 中使用这些命令协作处理一个项目并跟踪代码变更。

感谢阅读！