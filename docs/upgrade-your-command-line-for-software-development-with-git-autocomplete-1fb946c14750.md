# 使用 Git Autocomplete 升级软件开发的命令行

> 原文：<https://levelup.gitconnected.com/upgrade-your-command-line-for-software-development-with-git-autocomplete-1fb946c14750>

## 添加 git 自动完成和自定义快捷方式

![](img/09f84d8bed87b6034813fcc56dd4ed85.png)

照片由 [Sai Kiran Anagani](https://unsplash.com/@_imkiran?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/automation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

花时间配置您的环境以提高效率会带来回报。你需要考虑的愚蠢的事情越少，你就有越多的精力去解决难题。

本着这种精神，我们将涵盖 3 个黑客来改善您的命令行。

# Git 分支自动完成

在我目前工作的地方，我们使用详细的分支名称，包括问题编号和功能描述。因此，支持邀请朋友的 API 的分支可能看起来像`712_invite_friend_api`。

当你想检查这个分支时，打字很麻烦。甚至点击`git branch`来获得一个分支列表，然后复制/粘贴它也是令人分心的。

我宁愿打`git checkout 712` + `tab`。让我们开始吧！

运行这个命令来克隆 [git 完成 bash](https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash) 。运行它的时候不要担心你在什么目录下。`~/`将确保它被复制到您的个人目录。它将被称为`.git-completion.bash`。

```
curl https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash -o ~/.git-completion.bash
```

现在在您的主目录中打开`.bash_profile`。

```
cd
vim .bash_profile
```

把这个贴在底部。

```
test -f ~/.git-completion.bash && . $_
```

现在关闭并重新打开终端，你就可以自动完成 git 分支了。

# 总是显示当前 Git 分支

如果你经常跳转分支，没有什么比写代码然后意识到你在错误的分支上更糟糕的了。

为了避免这种情况，请始终将当前分支机构的名称放在前面。将分支名称添加到我们的命令行提示符中。

在您的主目录中打开`.bash_profile`。

```
cd
vim .bash_profile
```

并粘贴以下内容。

```
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

export PS1="\u@\h \[\033[32m\]\w - \$(parse_git_branch)\[\033[00m\] $ "
```

瞧啊。当您打开新的终端时，将显示当前的分支名称。

# 自定义命令行快捷方式

冗长晦涩的命令是效率的敌人。为什么不写我们自己的快捷方式或别名。

这允许您设置自定义命令，以便运行更长的命令。

打开`.bash_profile`并尝试添加一个你不喜欢输入的命令和它的新别名。使用以下语法。

```
alias <alias_name>="<command>"
```

这是我设置的一个。

```
alias jup="jupyter notebook --NotebookApp.iopub_data_rate_limit=1.0e10"
```

这让我可以打开具有高数据速率限制的 jupyter 笔记本，用`jup`代替那条长得吓人的命令。

# 结论

正如 Perl 的创造者拉里·沃尔所说:

> 程序员的三大美德是:懒惰、急躁和傲慢

这些定制应该可以帮助你变得更懒一点，这样你就可以为解决更难的问题节省脑力。