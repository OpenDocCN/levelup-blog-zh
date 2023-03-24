# 为什么我选择学习 Vim

> 原文：<https://levelup.gitconnected.com/why-i-chose-to-learn-vim-5bf430b1bc9>

## 我的 Vim 之旅以及为什么你也应该考虑学习 Vim

![](img/74fc0ff6335d3f14b26f2e536067c6ef.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

## 为什么我选择学习 Vim

自从我进入编码世界以来， [Visual Studio Code](https://code.visualstudio.com/) 一直是我的首选代码编辑器。在过去的 1.5 年里，我已经了解了所有我喜欢的和最有用的扩展。我学到了一些更有效的编码体验的捷径。我用的是他们的集成版本控制管理系统，直接用我的 GitHub 账号连接。VS 代码速度快，用户友好，可以根据你的喜好定制。

我最近开始了一个软件开发人员学徒计划。当我通过邮件(WFH 人寿)收到我的新笔记本电脑时，我完成的第一个任务就是设置我的 VS 代码环境。老实说，我从未想过要换一个编辑——我为什么需要换，对吗？

## 过渡到 Vim

当我决定在学徒期开始学习什么时，我的导师建议我学习 Vim。我想，在之前，我从未使用过 Vim。嗯，事实证明我遇到过这种情况——当您需要修复代码中的合并冲突时，终端中通常会打开一个 vim 编辑器窗口。我经历过，发现自己非常困惑，无法驾驭它！

在承诺之前，我读了一点 Vim。不使用鼠标？听起来很吓人！你需要记住的具体命令和快捷键？在学习一门新的编码语言(Python)的同时，这不会太难吗？我坐在那里，挖掘自己的感受——害怕变化，害怕未知，害怕失败。如果我学不会这个新工具怎么办？如果“太难了”呢？那时我知道我有了答案——学习 Vim 是正确的选择。

> "生活总是始于踏出你的舒适区一步."香农·l·阿德勒

我几乎让自己屈服于留在自己的舒适区——多么无聊的地方！所以我致力于学习 Vim。如果你曾经发现自己想要了解更多，但有点害怕，我会在这里与你分享我的经验…

## 学习 Vim 的理由

*   你已经“掌握”了你的首选代码编辑器(例如 VS 代码),想要一个新的挑战
*   您依赖于鼠标，并且发现自己很难在终端/命令行中导航
*   您依赖于 VS Code 的版本控制管理器，并且不太习惯使用`git`命令
*   准备好应对特定代码编辑器不可用的情况
*   留下深刻印象:-)

## 如何开始学习 Vim

虽然本文不会深入介绍如何设置您的本地 Vim 环境，但是我会提供一些建议来帮助您开始:

1.  下载 Vim(我用的是[自制](https://formulae.brew.sh/formula/vim) — `brew install vim`)
2.  使用 Vim 包管理器，如[vu ndle](https://github.com/VundleVim/Vundle.vim)——有大量有用的插件(如 VS 代码中的扩展),允许您定制您的 Vim 体验
3.  使用 [iTerm2](https://iterm2.com/index.html) 升级您的终端体验

## 我是如何开始学习 Vim 的

学习 Vim 的资源有很多——无论是通过 YouTube，Vim 的内置帮助中心，还是通过快速的谷歌搜索。

下载完所有必要的工具并设置好您的本地环境后，这里有一些链接和想法，可以开始您的 Vim 之旅:

1.  我完成的第一个任务是`vimtutor`。当你在命令行中直接输入`vimtutor`时，你会直接在 Vim 中看到一组教程，这是一个很好的开始！
2.  在 Vim 编辑器中，随时输入`:help`
3.  像玩游戏一样对待你的学习——我被引导到一个名为 [Vim Adventures](https://vim-adventures.com/) 的网站，它允许你学习如何通过视频游戏般的体验来浏览 Vim。
4.  想想你最常用的击键或移动/动作——然后看看 Vim 中是否有同样命令的快捷方式。例如，假设您想要删除 3 行代码。键入`D`将会从你的项目中删除一整行。要删除这 3 行，您可以输入三次`D`。你也可以输入`3D`,用更少的思考和击键，你会得到同样的结果。你会发现你的流动对你来说是最自然的。
5.  你越多地使用你学到的命令，它们就越会成为你的第二天性。致力于专门在 Vim 中编写所有新项目的代码。开始的时候可能会花你更长的时间，但是你用得越多，它就会变得越容易。

## 请记住…

**练习。**通读帮助资源或`vimtutor`课程是一回事。积极利用这些经验是完全不同的事情。对于我学习的每一个新命令，我都会返回到一个打开的 Vim 窗口，并使用这个命令多次。重要的是，你要把手放在键盘上练习，实时观察每个命令做了什么。问问你自己——如果我在一行的开头或结尾，它的行为会是一样的吗？根据光标的位置，命令的行为会有所不同吗？只需一个简单的`u`，你就可以撤销你所做的任何事情，所以玩一玩没有坏处！

**提交。像生活中的大多数事情一样，学习一项新技能需要投入。在 Vim 中编写项目代码可能比在您喜欢的编辑器中要花更长的时间。没关系。给你学习 Vim 的承诺设定时间——我更喜欢[番茄工作法](https://en.wikipedia.org/wiki/Pomodoro_Technique)。你投入的越多，回报就越大。**

**背熟(但不要太多)。**换句话说，先钉基础。尽量不要让自己被所有可能的命令搞得不知所措。在一张便笺上，我写下了 8-10 条我最常用的命令，并把它放在键盘旁边。每当我发现自己想要采取那个特定的行动时，我就在我的记事本上确认捷径。在我意识到这一点之前，我不再需要那张卡片了！随着您的进展，根据需要重复新的有用命令。

**玩得开心！**不，真的！无论是玩 Vim-Adventures 还是编写一个非常简单的项目，学习都意味着乐趣、刺激和挑战。

## 我把这个留给你…

有很多 Vim 备忘单。对我有用的是创造我自己的。我不仅要重新输入命令来帮助巩固它们在我的记忆中，我现在在一个地方就有了我需要的一切，而不是导航到几个不同的网站。**这里** **可以找到我的小抄** [**。**](https://www.craft.do/s/5FNcLxAUdJbab0)

如果你致力于学习 Vim，我很乐意听到它！编码快乐！

**我发现其他有用的 Vim 资源:**

[1][https://gist.github.com/tuxfight3r/0dca25825d9f2608714b](https://gist.github.com/tuxfight3r/0dca25825d9f2608714b)

[http://vimdoc.sourceforge.net/htmldoc/help.html](http://vimdoc.sourceforge.net/htmldoc/help.html)

[3][http://robertames.com/files/vim-editing.html](http://robertames.com/files/vim-editing.html)

[4][https://stack overflow . com/questions/5400806/what-is-the-most-used-vim-commands-keypresses/5400978 # 5400978](https://stackoverflow.com/questions/5400806/what-are-the-most-used-vim-commands-keypresses/5400978#5400978)

[5][http://www . viemu . com/a _ VI _ vim _ graphical _ cheat _ sheet _ tutorial . html](http://www.viemu.com/a_vi_vim_graphical_cheat_sheet_tutorial.html)

[6][https://www.moolenaar.net/habits.html](https://www.moolenaar.net/habits.html)

[7][https://www . barbarianmeetscoding . com/boost-your-coding-fu-with-vs code-and-vim](https://www.barbarianmeetscoding.com/boost-your-coding-fu-with-vscode-and-vim)