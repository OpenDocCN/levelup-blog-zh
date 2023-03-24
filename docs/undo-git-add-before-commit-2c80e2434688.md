# 如何在提交前撤消“git add”

> 原文：<https://levelup.gitconnected.com/undo-git-add-before-commit-2c80e2434688>

## 在 git 中执行提交之前，撤消 Git 添加

![](img/e134dcec15f020709b827e2c776ca60b.png)

由 [Roman Synkevych](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/git?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 介绍

我们经常错误地使用`git add`将文件添加到下一次提交中。该命令将使用工作树中的内容更新索引，并为即将到来的提交准备暂存的内容。在提交之前可以多次执行`git add`命令，它只添加指定文件的内容(或者您可以使用`.`符号添加自上次提交以来更新的所有跟踪文件)。

在今天的简短教程中，我们将展示如何在执行`git commit`之前撤销一个`git add`命令，以防你错误地包含了一个或多个文件。

首先，让我们假设我们目前正在处理一个 Git 分支，它包含以下跟踪和未跟踪的文件，其中的更改尚未提交:

```
$ git status
Your branch is up to date with 'origin/feature/example-branch'.Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   mypackage/module_1.py
        modified:   mypackage/module_2.py
        modified:   mypackage/module_3.py
        modified:   mypackage/mysubpackage/another_module.pyUntracked files:
  (use "git add <file>..." to include in what will be committed)
        data/my_data_file.txtno changes added to commit (use "git add" and/or "git commit -a")
```

现在假设我们想要将三个文件`mypackage/module_1.py`、`mypackage/module_3.py`和`mypackage/mysubpackage/another_module.py`添加到下一个提交中:

```
$ git add \ 
    mypackage/module_1.py \
    mypackage/module_3.py \
    mypackage/mysubpackage/another_module.py
```

现在，如果我们检查状态，我们可以看到文件包含在要提交的更改中:

```
$ git status
Your branch is up to date with 'origin/feature/example-branch'.Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   mypackage/module_1.py
        modified:   mypackage/module_3.py
        modified:   mypackage/mysubpackage/another_module.pyChanges not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   mypackage/module_2.pyUntracked files:
  (use "git add <file>..." to include in what will be committed)
        data/my_data_file.txtno changes added to commit (use "git add" and/or "git commit -a")
```

## 撤消 git 提交

现在，撤销`git add`的第一个选项是`git reset`命令。现在让我们假设我们不小心存放了`myapckage/module_3.py`文件，我们想恢复这个操作。我们可以使用以下命令来实现这一点:

```
$ git reset mypackage/module_3.py
```

执行上述命令后将报告的消息将通知我们，一旦进行了重置，哪些文件现在未转移:

```
Unstaged changes after reset:
M       mypackage/module_3.py
```

或者，如果您想要**取消之前添加到下一个提交**中的所有文件，您可以简单地运行

```
$ git reset
```

现在，如果您没有在您的存储库中进行任何提交，那么上面的命令将会失败，因为`HEAD`是未定义的:

```
fatal: Failed to resolve 'HEAD' as a valid ref.
(use "git rm --cached <file>..." to unstage
```

如果是这种情况，那么你应该使用`git rm`命令，而不是`git reset`命令。

```
$ git rm --cached mypackage/module_3.py
```

同样地，如果您想要卸载所有文件，您将需要`-r`标志来递归:

```
$ git rm -r --cached .
```

## 最后的想法

在今天的短文中，我们讨论了用于将当前工作树中的内容添加到下一次提交的`git add`命令，以及在错误地包含一个或多个文件的情况下，如何最终撤销该命令。

更具体地说，我们展示了如何使用大多数情况下都有效的`git reset`命令撤销暂存文件，假设您已经对存储库进行了至少一次提交。或者，您应该使用`git rm`命令从下一次提交中删除文件。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读媒体上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](https://betterprogramming.pub/common-git-errors-2e379516dc65) [## 6 个常见的 Git 错误以及如何避免它们

### 探究 Git 中一些最常见的错误

better 编程. pub](https://betterprogramming.pub/common-git-errors-2e379516dc65) [](/delete-local-remote-git-branch-1d8c0870eebc) [## 如何在本地和远程删除 Git 分支

### 展示如何在 Git 中删除本地和远程分支

levelup.gitconnected.com](/delete-local-remote-git-branch-1d8c0870eebc) [](https://betterprogramming.pub/kafka-cli-commands-1a135a4ae1bd) [## 用于日常编程的 15 个 Kafka CLI 命令

### 演示最常用的 Kafka 命令行界面命令的使用

better 编程. pub](https://betterprogramming.pub/kafka-cli-commands-1a135a4ae1bd)