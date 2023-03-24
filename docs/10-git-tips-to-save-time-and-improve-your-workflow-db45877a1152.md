# 节省时间和改善工作流程的 10 个 Git 技巧

> 原文：<https://levelup.gitconnected.com/10-git-tips-to-save-time-and-improve-your-workflow-db45877a1152>

![](img/5d84feb0f32d7ed1923b5afe7add791e.png)

[乔·塞拉斯](https://unsplash.com/@joaosilas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/history?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Git，*愚蠢的内容追踪器*根据它的[联机帮助页](https://manpages.debian.org/stretch/git-man/git.1.en.html)，有很多特性，有些可能令人生畏。所以我们一遍又一遍地使用我们能记住的几个命令，而不是利用赋予我们的所有能力。

这篇文章也有中文版本:[https://www.infoq.cn/article/tgdOxMBqYHoVslvlZqxT](https://www.infoq.cn/article/tgdOxMBqYHoVslvlZqxT)

# 技巧#1:改进您的配置

Git 在全球、用户和本地级别上都是高度可配置的。

[https://git-scm.com/docs/git-config](https://git-scm.com/docs/git-config)

## 查找顺序

每个设置都可以被覆盖:

```
$CWD/.git/config
    ▼ ▼ ▼
$HOME/.gitconfig`
    ▼ ▼ ▼
$HOME/.config/git/config
    ▼ ▼ ▼
/etc/gitconfig
```

## 更改设置

使用您最喜欢的编辑器或使用 CLI 编辑任何文件:

```
# GLOBAL SETTINGS
git **config --global *<keypath> <value>***# LOCAL SETTINGS
git **config *<keypath> <value>***
```

如果值包含任何空白字符，则需要用引号引起来。

## 显示当前设置

```
# SHOW CURRENT SETTINGS AND THEIR ORIGIN
git **config --list --show-origin**
```

## 有用的配置设置

```
# SET YOUR IDENTITY
git **config --global user.name *"<your name>"***
git **config --global user.email** *<your email>*# PREFERRED EDITOR
git **config --global *core.editor vim***# CREDENTIALS CACHE
# WINDOWS
git **config --global *credential.helper manager***# LINUX (timeout in seconds)
git **config --global *credential.helper "cache --timeout=3600"***# MACOS
git **config --global *credential.helper osxkeychain***
```

[https://git-scm.com/docs/gitcredentials](https://git-scm.com/docs/gitcredentials)

# 技巧 2:别名

要保存常用的 git 命令，请创建一个别名:

```
# CREATE AN ALIAS
git **config --global *alias.<alias-name> "<git command>"***# USE AN ALIAS
git ***<alias-name> <more optional arguments>***
```

## 有用的别名

```
# UNDO LAST COMMIT
git **config --global *alias.undo "reset --soft HEAD^"***# AMEND STAGED CHANGES TO LAST COMMIT (keep commit message)
git **config --global *alias.amend "commit --amend --no-edit****"*# COMPACTER STATUS OUTPUT
git **config --global *alias.st "status -sb"***# COLORED LOG WITH GRAPH
git **config --global *alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'"***# REMOVE ALL MERGED BRANCHES
git **config --global *alias.rmb "!git branch --merged | grep -v '*' | xargs -n 1 git branch -d"***# RANK CONTRIBUTERS
git config --global alias.rank "shortlog -n -s --no-merges"
```

# 技巧#3:查找提交和变更

## 通过提交消息

```
# BY COMMIT MESSAGE (all branches)
git **log --all --grep=*'<search term>'***# BY COMMIT MESSAGE (include reflog)
git **log -g --grep=*'<search term>'***
```

## 通过改变

```
# BY CONTENT OF CHANGES
git **log -S *'<search term>'***
```

## 按日期

```
# BY DATE RANGE
git **log --after=*'DEC 15 2019'* --until=*'JAN 10 2020'***
```

# 技巧 4:添加大块

参数`--patch / -p`将交互地存放块，而不是仅仅用`git add <filepath>`添加文件的所有更改。

```
# Patch Commandsy = stage hunk
n = don't stage this hunk
q = quit
a = stage this and all remaining hunks of the current file
d = don't stage this and all remaining hunks of the current file
/ = search for hunk (regex)
s = split into smaller hunks
e = manually edit hunk
? = print helpg = select a hunk to go to
j = leave hunk undecided, see next undecided hunk
J = leave hunk undecided, see next hunk
k = leave hunk undecided, see previous undecided hunk
J = leave hunk undecided, see previous hunk
```

[https://git-scm.com/docs/git-add#Documentation/git-add.txt 一号](https://git-scm.com/docs/git-add#Documentation/git-add.txt--i)

# 技巧 5:不提交就隐藏变更

存储是当前更改的临时搁置。在他们的帮助下，我们可以返回到索引的当前状态，并可以在以后重新应用隐藏的更改。

默认情况下，只有当前跟踪文件中的更改将被隐藏，新文件将被忽略。

可以独立创建和应用多个 stashes。

[https://git-scm.com/docs/git-stash](https://git-scm.com/docs/git-stash)

## 创造

```
# CREATE NEW STASH
git **stash**# CREATE NEW STASH (include untracked changes)
git **stash -u/--include-untracked**# CREATE NEW STASH WITH NAME
git **stash save *"<stash name>"***# INTERACTIVE STASHING
git **stash -p**
```

## 列表

```
# LIST ALL STASHES (providing "n" for other commands)
git **stash list**
```

## 看法

```
# VIEWING STASH CONTENT
git **stash show**# VIEWING STASH DIFFS
git **stash show -p**
```

## 应用

```
# APPLY LAST STASH (delete stash)
git **stash pop**# APPLY LAST STASH (keep stash)
git **stash apply**# APPLY SPECIFIC STASH (n = stash list number)
git **stash pop/apply stash@*{n}***# CREATE NEW BRANCH FROM STASH (n = stash list number)
git **stash branch *<new branch name>* stash@*{n}***# APPLY SINGLE FILE FROM STASH (n = stash list number)
git **checkout stash@*{n}* -- *<filepath>***
```

## 清洁

```
# DROP SPECIFIC STASH (n = stash list number)
git **stash drop stash@*{n}***# DROP ALL STASHES
git **stash clear**
```

# 技巧 6:预演

许多 git 操作可能极具破坏性。例如，`git clean -f`将删除所有未被跟踪的文件，没有任何机会恢复它们。

为了避免这种灾难性的结果，许多命令支持*模拟运行*以在结果实际发生之前检查结果。遗憾的是，使用的选项并不总是相同的:

```
git **clean -n/--dry-run**
git **add -n/--dry-run**
git **rm -n/--dry-run**# GIT MERGE PSEUDO DRY-RUN
git **merge --no-commit --no-ff *<branch>***
git **diff --cached**
git **merge --abort**
```

要知道`git commit -n`根本不是*的预演*！其实就是选项`--no-verify`，忽略任何`pre-commit` / `commit-msg` [githooks](http://schacon.github.io/git/githooks.html) 。

# 技巧 7:更安全的用力

通过处理旧的提交、创建新的头等等，很容易搞乱分支。远程改变*可以用`git **push --force**`覆盖*，但是它们*不应该*！

`git **push --force**`是一个 ***破坏性且危险的*** 操作，因为它无条件地工作，并且破坏其他提交者已经推送的任何提交。这对他人的储存库不一定是致命的，但改变历史和影响他人不是一个好主意。

更好的选择是使用`git **push --force-with-lease**`来代替。

git 不是无条件地覆盖上游远程，而是检查任何本地不可用的远程更改。如果是这样，它会失败，并显示一条“陈旧信息”消息，并希望我们先执行`git **fetch**`。

[https://git-SCM . com/docs/git-push # Documentation/git-push . txt-force-with-leaseltrefnamegt](https://git-scm.com/docs/git-push#Documentation/git-push.txt---force-with-leaseltrefnamegt)

# 技巧 8:更改提交消息

提交是不可变，且不能被改变。但是我们可以用新的消息来修改现有的提交。这将替换原来的提交，所以不要在已经推送的提交上使用它。

```
git **commit --amend -m *"<new commit message>"***
```

[https://git-SCM . com/docs/git-commit # Documentation/git-commit . txt-amend](https://git-scm.com/docs/git-commit#Documentation/git-commit.txt---amend)

# 技巧 9:更多地改变历史

更改存储库的历史不会在最后一个提交消息时停止。使用`git rebase`可以更改多个提交:

```
# RANGE OF COMMITS
git **rebase -i/--interactive *HEAD~<number of commits>***# ALL COMMITS AFTER <commit-hash>
git **rebase -i/--interactive *<commit hash>***
```

在已配置的编辑器中会显示一个提交的逆序列表。大概是这样的:

```
# <command> <commit hash> <commit message>
pick 5df8fbc revamped logic
pick ca5154e README typos fixed
pick a104aff added awesome new feature
```

通过更改编辑器的实际内容，我们可以为 git 提供一个蓝图，告诉它如何重新构建基础:

```
# p, pick   = use commit without changes
# r, reword = change commit message
# e, edit   = edit commit
# s, squash = meld into commit
# f, fixup  = like "squash", but discard commit message
# x, exec   = run command (rest of the line)
# d, drop   = remove commit
```

保存编辑器后，git 将运行这个蓝图重写历史。

`e, edit`将暂停 rebase，以编辑存储库的当前状态。最后，运行`git rebase --continue`。

如果出现任何问题，比如合并冲突，我们想重新开始，总会有`git rebase --abort`。

[https://git-scm.com/docs/git-rebase](https://git-scm.com/docs/git-rebase)

# 提示#10:存档跟踪的文件

我们可以用不同的格式(`zip`或`tar`)压缩特定 ref 的跟踪文件:

```
git **archive --format *<format>* --output *<filename> <ref>***
```

`<ref>`可以是分支、提交散列或标签。

[https://git-scm.com/docs/git-archive](https://git-scm.com/docs/git-archive)

# 额外提示:单个破折号

有一个代表先前使用的分支的快捷方式:单个破折号`-`

```
git checkout my-branch
# Current branch: my-branch
<do some git operations, e.g. adding/commiting>git checkout develop
# Current branch: develop
git merge -
# Merges my-branch into develop
```

单破折号等于`@{-1}`。

[https://git-SCM . com/docs/git-check out # Documentation/git-check out . txt-ltbranchgt](https://git-scm.com/docs/git-checkout#Documentation/git-checkout.txt-ltbranchgt)

# 结论

git 还有很多主题，这只是皮毛。在另一篇文章中，我想展示如何用`git **bisect**`有效地找到中断的提交，或者用`git **reflog**`使用任何`git`操作的完整历史。

# 资源

*   [Git SCM 文档](https://git-scm.com/docs)
*   [Pro Git Book](https://git-scm.com/book/en/v2) (免费)
*   [GitHub Git 备忘单](https://github.github.com/training-kit/downloads/github-git-cheat-sheet.pdf) (PDF)
*   [互动备忘单](https://ndpsoftware.com/git-cheatsheet.html)