# 使用 Git 命令会让你看起来像一个高级开发人员

> 原文：<https://levelup.gitconnected.com/using-git-commands-that-will-make-you-look-like-a-senior-developer-14247f4aca3b>

![As](img/a31dd58eb32eae63c3d82e41e6cb2205.png) 作为 一个开发者你可能每天都需要 Git。已经有很多关于基本 Git 命令的文章解释得比我更好，例如[kode hauz Inc](https://medium.com/kodehauz/basic-git-commands-with-examples-8c145949b1bd)[的](https://medium.com/u/a231dd2234cb?source=post_page-----14247f4aca3b--------------------------------)和 [Goran Aviani](https://medium.com/u/3ade16c9bf1c?source=post_page-----14247f4aca3b--------------------------------) 的[和](https://medium.com/faun/understanding-git-basics-commands-tips-tricks-da0c05db411f)[Saurabh Kulshrestha](https://medium.com/edureka/git-commands-with-example-7c5a555d14c)的。他们的文章棒极了。看看他们。

然而，在本文中，它是关于高级 git 命令的，如果您正在使用它们，就很容易让别人认为您是 Git 专家。此外，它们还会帮助你提高工作效率。因此，即使您非常习惯使用 Git，您仍然会发现这篇文章很有帮助。

![](img/abe3390af3f3095d4b307b4174a149f4.png)

[杰弗逊·桑托斯](https://unsplash.com/@jefflssantos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

下面是一些不太为人所知但很方便的 Git 命令，可以提高你的工作效率，甚至可能给你的同事留下深刻印象。

## git 结帐-

如果您多次来回切换不同的分支，您将不得不一遍又一遍地编写`git checkout branch-1`和`git checkout branch-2`。既然开发者讨厌重复，你可以使用`git checkout -`

所以，当切换到前一个分支时，你只需要写`git checkout -`。来回切换不同的分支意味着，每当你想切换到另一个分支时，你只需要写一次`git checkout -`。很方便，你不觉得吗？

## git add -p

很可能你以前用过`git add .`。该命令意味着您要将您更改的每个文件一次添加到提交中。但是如果你想更有选择性地选择你想做的事情呢？或者，如果您想在提交之前逐一完成您所做的每一项更改，该怎么办？

为此你有`git add -p`。`-p`代表“补丁”。使用`git add -p`你不需要一次添加所有的修改，而是在小的“补丁”中添加，你可以决定是否要添加到你的提交中。Git 会询问你是否要添加补丁。你可以通过键入`y`表示“是”或`n`表示“否”来做出决定。

## git 平分

如果您必须检查每一个提交，搜索最后一个工作提交可能会很乏味。通过使用二分搜索法，让你很容易找到最后一个工作提交。

为此，您必须通过键入`git bisect start`来激活`git bisect`。在那之后，您键入`git bisect good`进行一个您知道可以正常工作的提交。一旦你这样做了，你输入`git bisect bad`进行一个你知道不再有效的提交(通常这是你最后一次提交)。

当你写`git bisect bad`的时候，Git 会检查一个旧的提交，这个旧的提交在你的第一个好的提交和最后一个坏的提交之间，直到它找到第一个坏的提交。

## git 提交-修改

有时，您想要更改已经推送的提交消息，或者您忘记添加一个小的提交，该提交与您的上一次提交非常相关，以至于您不想创建它自己的提交。在这种情况下，您可以使用`git commit --amend`。

使用`git commit --amend`您可以更改最后一次提交消息，也可以对最后一次提交添加额外的更改。但是，如果您想要推动您的更改，您必须在任何一种情况下使用`git push -f`。

## git rebase -i HEAD~n

`git commit --amend`允许您更改上次提交的消息。如果您想更改在此之前提交的消息，该怎么办呢？`git rebase -i HEAD~n`来救你。通过键入`git rebase -i HEAD~n`，您可以返回到第`n`次提交，并通过键入`edit`将其更改为您想要编辑的提交。

一旦您完成并想要保存您的更改，您必须使用`git push -f`来覆盖更改。

编辑:

感谢[罗伯特·雅各布](https://medium.com/u/84e424a04e56?source=post_page-----14247f4aca3b--------------------------------)的评论，我想补充一点，使用`git push -f`并不总是被推荐的。我想添加这个的原因是因为使用`git push -f`，当多人在同一个代码库工作时，代码会变得不同步。但是我上面提到的例子仍然是有效的*，只要你独自作为唯一作者在你自己的功能分支上工作*。如果您单独在一个功能分支上工作，只有您作为贡献者，没有人会不同步——即使您将您的功能分支合并回主分支。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

你以前用过吗？你对这些 Git 命令有什么看法？你知道更多不太为人所知但很方便的 Git 命令吗？或者你有什么问题吗？下面评论。我非常感兴趣的是，还有哪些 Git 命令对您有用。