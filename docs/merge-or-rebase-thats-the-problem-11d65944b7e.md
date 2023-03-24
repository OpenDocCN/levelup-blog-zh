# 合并还是改基？这就是问题所在

> 原文：<https://levelup.gitconnected.com/merge-or-rebase-thats-the-problem-11d65944b7e>

![](img/fafc94b0adc3940688ee35740bceb82a.png)

实际上， [git rebase](https://www.w3docs.com/learn-git/git-rebase.html) 和 [git merge](https://www.w3docs.com/learn-git/git-merge.html) 做的是同样的事情，但是方式不同。它们都将一个[分支](https://www.w3docs.com/learn-git/git-branch.html)中的 [git 提交](https://www.w3docs.com/learn-git/git-commit.html)集成到另一个分支中，但是它们是如何实现预期结果的，我们将在本文的范围内讨论！

首先，让我们深入研究这些命令，并找到最适合您的工作流程的确切策略。

## **Git 合并**

Git merge 获取源分支的内容，然后[将其与目标分支](https://www.w3docs.com/snippets/git/best-and-safe-way-to-merge-a-git-branch-into-master.html)集成。因此，只有目标分支被更改，而源分支历史保持不变。换句话说，当您合并时，您的 HEAD 分支生成一个新的 git 提交，维护每个提交历史的祖先。

## 赞成的意见

*   提供简单的用法。
*   保留源分支的原始上下文。
*   保留提交历史记录和时间顺序。
*   将源分支的提交与另一个分支的提交分开。

## 骗局

*   大量的 merge commits 把历史弄乱了，从而使得 Git 库的可视化图表有彩虹状的支线，没有有用的信息。

## **Git Rebase**

Git rebase 将所有的更改堆到一个补丁中，并将这个补丁集成到目标分支中。merge 和 rebase 的一个核心区别是后者清除了历史。在将工作从一个分支转移到另一个分支的过程中，不需要的部分被移除以维护线性项目历史。当您需要要素分支中主分支的最新更新，但主分支的历史必须保持干净时，该命令非常方便。

> *rebase 将一个分支的更改重新写入另一个分支，而不创建新的提交。*

## 赞成的意见

*   使历史可读和线性，并清除复杂的历史。
*   通过使提交成为单个提交来清除提交。

## 骗局

*   不适用于“拉”请求，因为您看不到团队成员做了什么微小的更改。
*   将特性降低到一堆可以隐藏上下文的提交。

## **什么时候合并，什么时候改基？**

您已经区分了这两种策略，现在让我们来讨论用例。

## **调查**

在采取行动之前，仔细调查总是一个好主意。需要注意的是，所选择的策略应该与您的工作流程相匹配。一个工作流策略可能对一个团队更好，而对另一个团队可能是致命的。因此，在选择一个策略之前，要考虑每个策略。

## **个人 vs 团队**

如果您单独工作，选择 rebase 而不是 merge 可能会更方便。如果您共享特征分支，您将从其他成员处获得更改，则重置基础过程将产生不一致性。

> *总是要考虑到合并保存了历史，而 rebase 重写了历史！*

对于基于特性的工作流，git merge 可能是您团队的正确选择。这也有助于你避免[回复](https://www.w3docs.com/learn-git/git-revert.html)或[重置](https://www.w3docs.com/learn-git/git-reset.html)任何东西。

合并的结果是，不同的特性保持隔离，不会干扰现有的提交历史。如果优先考虑的是保持完美的历史，那么 git rebase 是避免不相关提交的更合适的版本。

## **冲突**

[恢复 rebase](https://www.w3docs.com/snippets/git/undoing-a-git-rebase.html) 比[恢复 merge](https://www.w3docs.com/snippets/git/how-to-revert-a-merge-commit-already-pushed-to-the-remote-branch.html) 困难得多，因为 rebase 冲突一次提交一次，而 [merge 冲突](https://www.w3docs.com/learn-git/merge-conflicts.html)一次提交一次。Git 允许预览当你[合并你的分支](https://www.w3docs.com/snippets/git/how-to-preview-a-merge-in-git.html)时会发生什么。

但是，不要乱用 rebase！在你知道自己在做什么之前，不要改变基础。不正确的 rebase 会把你置于一个关键的位置，导致严重的后果。

> 永远记住不要在共享分支上改变基础。

此外，阅读[如何重定 Git 分支](https://www.w3docs.com/snippets/git/how-to-rebase-git-branch.html)。

总之，重定基数和合并通过选择不同的方法提供了相同的结果。每种方法都有其独特之处。对一种情况非常有用的东西，对另一种情况可能是致命的。仔细的调查可以防止混乱。因此，总是要考虑到你和你的团队当前的情况和可能的障碍。此外，查看我们的 [Git 片段](https://www.w3docs.com/snippets/git.html)和 [Git 书籍](https://www.w3docs.com/learn-git.html)来阅读关于命令和例子！