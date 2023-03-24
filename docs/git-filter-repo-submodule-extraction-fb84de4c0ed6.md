# Git 滤波器-Repo 子模块提取

> 原文：<https://levelup.gitconnected.com/git-filter-repo-submodule-extraction-fb84de4c0ed6>

使用 git-filter-repo 将文件夹提取到子模块中，促进代码重用

![](img/3f4d370ca64642b15b4d9d9b2153b8eb.png)

通过提取文件夹和创建子模块来重用代码(📷: [cigdem](https://www.shutterstock.com/image-illustration/filing-cabinet-single-yellow-folder-open-259260773)

*“我总是选择一个懒惰的人去做一件困难的工作，因为一个懒惰的人会找到一个简单的方法去做。”—* [老弗兰克·吉尔布雷斯](https://quoteinvestigator.com/tag/frank-gilbreth-sr/)

# 管理🗂

管理有重复代码和混乱文件系统的项目是一件愚蠢的差事。临近的截止日期和完成任务的压力会导致偷工减料和仓促决策。现在的懒惰往往会导致未来成倍增加的工作，而一盎司的批判性思维和策略可以让你从大量的艰苦工作中解脱出来。

# 战略🤔

在开发一个新的 C++项目时，我们的两位工程师注意到了遗留 C++项目和我们的新项目之间的一些相似之处。与旧项目相比，新项目具有基本的架构差异，但是共享一些功能。我们希望只使用我们已经在使用的工具在项目之间共享模块，并决定将代码片段提取到独立的项目中，并将这些项目提取到 [Git 子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules)中，这是我们情况下的最佳策略。子模块是 repos 内部的 repos，允许代码共享，但可以独立于其父 repos 进行修改和跟踪。此外，通过使用 git-filter-repo，我们能够提取并保存我们感兴趣的文件夹的所有提交。

# 提取子模块🏗

子模块提取的推荐工具是 [git-filter-repo](https://github.com/newren/git-filter-repo) 。Git-filter-repo 是 Git 默认不附带的工具，但是在 Git 的[官方文档](https://git-scm.com/docs/git-filter-branch#_warning)中推荐用于这种类型的操作。

要使用 git-filter-repo，您需要执行以下步骤:

1.  确保你已经安装了 [Python 3](https://www.python.org/downloads/) 。在新版 Windows 上，通过 [Windows Store](https://www.microsoft.com/store/productId/9PJPW5LDXLZ5) 安装 Python 是为`git-filter-repo`配置 Python 的最简单方法。
2.  下载 [git-filter-repo.py](https://raw.githubusercontent.com/newren/git-filter-repo/main/git-filter-repo) 但是将文件保存为`git-filter-repo`(无扩展名)如果你在 macOS 或 Linux 上，你需要运行`chmod +x git-filter-repo`来使其可执行。
3.  将包含`git-filter-repo`的文件夹添加到路径中( [Windows](https://www.computerhope.com/issues/ch000549.htm) 、 [macOS](https://apple.stackexchange.com/a/358873/266217) 、 [Linux](https://linuxize.com/post/how-to-add-directory-to-path-in-linux/) )。

一旦你安装了 git-filter-repo，你会想要克隆一个你计划从中提取文件夹的 repo 的新副本。您可以将新回购的名称指定为 git clone 的最后一个参数，以提醒自己正在做什么，以防被打断或分心:

```
git clone [https://github.com/user/repo](https://github.com/company/repo) <<name of new repo>>
```

将您的工作目录设置为您刚刚克隆的 repo 的根文件夹。这个例子使用了`main`分支，但是您可以从任何分支上的提交中过滤出一个回购:

```
cd <<name of new repo>> && git checkout main
```

在执行任何回购过滤之前，我们希望移除`origin`遥控器，这样我们就不会意外地对父回购进行任何破坏性的更改。

```
git remote rm origin
```

接下来，运行 filter repo，传递`--subdirectory-filter`参数，后跟您想要用来创建子模块的文件夹。该命令将删除与`--subdirectory-filter`指定的文件夹无关的任何文件夹和提交，并将指定路径中的文件移动到 repo 的根目录。注意，Windows 用户会希望在路径中使用正斜杠`/`而不是传统的反斜杠:

```
git filter-repo --subdirectory-filter path/to/folder
```

注意，我们在上面的命令中使用了`--force`标志。需要`--force`标志来覆盖关于回购不干净的警告。警告发生是因为我们移除了`origin`遥控器。我们运行带有`--force`标志的`git filter-repo`来安全地覆盖由于远程不匹配引起的警告。

现在，您已经过滤了 repo，我们可以将内容推送到子模块 repo。在 GitHub 中创建一个新的 repo，并将 url 添加为`origin` remote:

```
git remote add origin https://github.com/user/submodule
```

最后，将当前分支推送到新的回购:

```
git push --set-upstream origin main
```

# 使用子模块📦

至此，您已经成功地将文件夹提取到一个子模块中！要使用子模块，请切换到父 repo 并运行以下命令:

```
git submodule add https://github.com/user/submodule-repo
```

就是这样！您已经成功地将文件夹提取到子模块中。现在，您可以在子模块中修改文件，并在多个存储库中提取更改。

您可以通过以下资源了解有关提取 git 子模块的更多信息:

*   将子文件夹拆分到新的存储库中( [GitHub](https://docs.github.com/en/get-started/using-git/splitting-a-subfolder-out-into-a-new-repository) )
*   从一个文件夹中创建一个子模块存储库，并保存它的 git 提交历史记录([堆栈溢出](https://stackoverflow.com/questions/17413493/create-a-submodule-repository-from-a-folder-and-keep-its-git-commit-history))
*   如何 Git 克隆包括子模块([堆栈溢出](https://stackoverflow.com/questions/3796927/how-to-git-clone-including-submodules))

感谢阅读！

```
**Want to Connect?**If you found the information in this tutorial useful please subscribe on [Medium](http://bobbyg603.medium.com/), follow me on [Twitter](https://twitter.com/bobbyg603), and/or subscribe to my [YouTube](https://www.youtube.com/c/bobbyg603) channel.
```

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
升一级就是变身科技招聘👉 [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)