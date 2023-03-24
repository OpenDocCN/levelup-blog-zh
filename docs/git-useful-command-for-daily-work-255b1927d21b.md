# 每天都可以使用的有用的 Git 命令

> 原文：<https://levelup.gitconnected.com/git-useful-command-for-daily-work-255b1927d21b>

![](img/c69cc4b291c187dbdb442c93fb52e069.png)

[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> 这是我日常使用的 Git 命令的集合。我会继续添加它们，因为我发现其他有用的。

作为承诺，我会更新一些有用的命令，请跟进:

*   修订版 1(2020 年 2 月 10 日):更新了第 4 项的注释，为第 13 项增加了两个命令，增加了新的第 16、17、18 项
*   修订版 2(2020 年 2 月 19 日):增加新项目 19、20、21

## 1.放弃本地文件修改

```
# discard specific file
git checkout -- <file_path># discard all unstaged change
git checkout -- .# discard unstaged changes since <commit>.
git checkout <commit>
```

## 2.撤消本地提交

```
# discard staged and unstaged changes since the most recent commit.
git reset HEAD~1
git reset --hard HEAD~1
```

## 3.编辑提交消息

```
# add your staged changes to the most recent commit
git commit --amend
git commit --amend -m "New message"
```

## 4.删除本地分支

```
git branch -d branchname # Delete branchname if it is already merged
git branch -D branchname # Force delete branchname
```

## 5.删除远程分支

```
git push --delete origin branchname
git push origin :branchname
```

## 6.还原推送的提交

```
# reverts the commit with the specified id
git revert c761f5c

# reverts the second to last commit
git revert HEAD^

# reverts a whole range of commits
git revert develop~4..develop~2

# undo the last commit, but don't create a revert commit 
git revert --no-commit HEAD = git revert -n HEAD
```

## 7.避免重复的合并冲突

```
git config --global rerere.enabled true
```

## 8.在合并后找到中断某些东西的提交

在一次大的合并之后，追踪引入一个 bug 的提交可能是相当耗时的。幸运的是`git`以`git-bisect`的形式提供了一个很棒的二分搜索法设施。首先，您必须执行初始设置:

```
# starts the bisecting session
git bisect start# marks the current revision as bad
git bisect bad# marks the last known good revision
git bisect good revision
```

在此之后，Git 将自动检出一个介于已知“好”和“坏”版本之间的修订。现在，您可以再次运行您的规范，并相应地将提交标记为“好”或“坏”。

```
# or git bisec bad
git bisect good
```

这个过程一直持续到引入 bug 的提交。

## 9.过滤支路

```
git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch secrets.txt' \
--prune-empty --tag-name-filter cat -- --all
```

这将从每个分支和标签中删除文件`secrets.txt`。它还将删除由于上述操作而为空的任何提交。

## 10.显示提交历史记录

```
git log                 # show log
git log --oneline       # show log in one line
git log -n1             # show log of last commit
git log -p <file_name>  # show log for a file
```

## 11.显示配置列表

```
git config --list           # see all setting of your git
git config --local --list   # see only local setting
git config --global --list  # see only global setting
```

## 12.未设置配置

```
git config --unset key
git config --unset --global key
```

## 13.在本地查看所有文件更改

```
git diff
git diff <file_name>    # Show changes for only one file# Displays changes between the working tree and the dev branch
git diff dev# Displays changes between the branches master and dev
git diff master..dev
```

## 14.对于文件名的每次更改，查看谁进行了更改以及更改发生的时间

```
git blame <file_name>
```

## 15.显示本地存储库头的更改日志

```
git reflog
```

16。删除所有已经合并到 master 或 develop 的本地分支

```
[alias]
  cu = !git branch --merged | egrep -v \"(^\\*|master|develop)\" | xargs git branch -d
```

然后键入“git cu”，而 **cu** 代表清理

**17。添加本地文件修改**

```
# Add file/folder to staging area
git add <file/folder># Add all files/folders in all the folders of the repository
git add . or git add -A# Add only tracked files/folders
git add . -u
```

18。Git 分支

```
git branch                    # List all local branches
git branch -r                 # List all remote branches
git branch -a                 # List all local/remote branches# Create new branch with <branch_name> based off of current branch
git branch <branch_name># Rename branch from <old_name> to <new_name>
git branch -m <old_name> <new_name># Rename the current branch to <new_name>
git branch -m <new_name>git checkout <branch_name>    # Switch to <branch_name>
git checkout -b <branch_name> # Create new <branch_name> and switch to it
```

**19。克隆 git 存储库**

```
# Clone repo into to specific directory
git clone <repo_url> <to_directory># Clone specific branch
git clone <repo_url> — branch <branch_name># Clone a certain level of history’s depth
git clone -depth=<depth_level> <repo_url>
```

**20。使用遥控器工作**

```
# List remote connections with URL
git remote -v# Change the remote url
git remote set-url <remote_name> <new_remote_url># Add another remote connection
git remote add <new_remote_name> <new_remote_url># Rename connection
git remote rename <remote_old_name> <remote_new_name># Delete connection
git remote rm <remote_name>
or
git remote remove <remote_name>
```

**21。通过抓取**下载最新的代码库

```
# Fetch all remote repositories
git fetch — all# Fetch <remote_name> repository, usually origins
git fetch <remote_name># Fetch a branch from a remote repository
git fetch <remote_name> <branch_name>
```

快乐 Gitting~