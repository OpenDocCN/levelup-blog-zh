# 像专家一样有效使用 Git 的 6 个技巧

> 原文：<https://levelup.gitconnected.com/6-tips-for-using-git-efficiently-like-a-pro-dc0ab6b81985>

## 高效的命令拯救你的生命

![](img/93cb60c8930a8fca401f8cc09280d481.png)

照片由 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

作为当今最流行的版本管理工具，Git 被绝大多数工程师使用。本文将介绍 6 个简单实用的小技巧供你使用。

# 1.将更改隐藏在一个脏的工作目录中

当你在一个分支机构做了一些工作，但不得不切换到另一个分支机构。此时，您可以使用“git stash”来保存当前未提交的代码。与之相关的常用命令如下:

```
# Staging uncommitted code
$ git stash# Staging uncommitted code and adding comments
$ git stash save comments# List all staging records
$ git stash list# Delete all staging records
$ git stash clear# Apply the last staged code
$ git stash apply# Apply the last staged code and delete the record
$ git stash pop# Delete the last record
$ git stash drop
```

使用`git stash apply`或`git stash pop`或`git stash drop`可以选择指定的记录。就像这样:

```
$ git stash apply stash@{1}
$ git stash pop stash@{2}
$ git stash drop stash@{3}
```

这些记录可以通过`git stash list`列出。

# 2.快速查看最后一次提交

案例:当你写了一个提交，但是还没有`push`到远程。有人打断你，当你再次回来时，你忘记你提交了什么。

然后您可以使用下面的命令来显示当前`HEAD`上最近的提交。

```
$ git show# or
$ git log -n1 -p
```

# 3.快速修改提交消息

修改提交消息中的用户名和电子邮件地址:

```
$ git commit --amend --author "Username Mail"
```

修改提交消息的内容:

```
# Edit with editor
$ git commit --amend --only# Modify directly
$ git commit --amend --only -m 'feat: update'
```

# 4.重置上次提交

在推送之前，您可以使用以下命令重置到上次提交的状态，同时保留临时区域的状态。

```
$ git reset --soft HEAD^
```

但是，如果您已经将提交推送到远程，您可以使用以下命令重置它:

```
$ git reset --hard HEAD^
$ git push --force [remote] [branch]
```

但这会搞乱那些已经从那个分支抽身的人的历史，慎用。

# 5.查看所有提交操作记录

如果使用类似`git reset --hard target`的命令，它会将 HEAD 重置为另一个 commit(目标)。

但是如果这个目标被错误地指定，其他贡献者的提交也将被重置。

然后，您可以使用以下命令进行恢复:

```
# View all commit operation records and find the CommitHash of the wrong operation
$ git reflog  
​
# Reset to the CommitHash of the wrong operation
$ git reset --hard CommitHash
```

# 6.删除分支

删除本地分支:

```
$ git branch -D local-branch
```

删除远程分支:

```
$ git push origin --delete remote-branch# or
$ git push origin :remote-branch
```

*今天就到这里。我是 Zachary，我将继续输出与 web 开发相关的故事。如果你喜欢这样的故事，想支持我，请考虑成为* [*中会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。