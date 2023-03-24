# 编写你的同事会喜欢的 Git Commit 消息

> 原文：<https://levelup.gitconnected.com/write-git-commit-messages-that-your-colleagues-will-love-5d890bbadfa0>

Git 提交消息是我们与未来自我交流的方式。他们帮助你理解*为什么*某一行代码被添加到代码库中。这就是为什么知道如何编写一个好的 Git 提交消息很重要。

![](img/55abeee7e64618e8df331442e83f4c8e.png)

我们都经历过。" *Git 迷惑*"，"*我为什么不能直接推到主分支？*、*没有人会读到这条信息*。这些想法是正常的，大多数学习过 Git 的人可能都有同感。您可能不会欣赏好的提交消息，除非您使用已经存在多年的代码库。或者当你回到你的老项目时。

在本文中，我将向您展示如何编写您和您的同事会喜欢的 Git commit 消息。

# 为什么 Git 提交消息很重要

你有没有打开一个旧的项目，用`git log`检查提交消息？如果没有，我建议你现在就这么做。如果它们没有填充“修复样式”或“添加端点”，那么干得好！如果你和我们一样，请继续读下去😄

或者你加入了一家公司，它的代码库已经存在了几年。你试图理解为什么要添加一些代码。换了会摔坏什么东西吗？在这种情况下，一个好的提交消息是无价的。

简而言之，通过编写好的 Git commit 消息，您可以为自己和同事提供未来保障。作为开发人员，我们阅读的代码比编写的代码多得多。

## 阅读与编写代码

鲍勃大叔(*罗伯特·c·马丁——流行编程书籍如《干净代码》*的作者)说:

> “的确，花在阅读和写作上的时间比远远超过 10 比 1。作为编写新代码工作的一部分，我们不断地阅读旧代码。…[因此，]让它易于阅读会让它更易于书写。”[(参考。)](https://www.goodreads.com/quotes/835238-indeed-the-ratio-of-time-spent-reading-versus-writing-is)

有时，为了理解代码，您还需要阅读提交消息。我希望您现在看到，编写一个好的提交消息是值得的！

# 如何编写好的 Git 提交消息

既然你已经确信提交信息是重要的，那么让我们来看看如何用一种你的同事会喜欢的方式来写它们！

提交消息由两部分组成:主题行和正文。这里有一个来自[比特币](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6)回购的例子。

```
commit eb0b56b19017ab5c16c745e6da39c53126924ed6
Author: Pieter Wuille <[pieter.wuille@gmail.com](mailto:pieter.wuille@gmail.com)>
Date: Fri Aug 1 22:57:55 2014 +0200Simplify serialize.h’s exception handlingRemove the ‘state’ and ‘exceptmask’ from serialize.h’s stream
 implementations, as well as related methods.…
```

第一行是主题，新行之后是正文。

## 关注为什么

**这是最重要的规则。**这也是最常见的错误。提交消息应该解释*为什么添加了*代码，而不是*添加了什么*。要查看*什么*我可以直接读取代码。但是要理解*为什么*我只能猜测。

即使我是代码的作者，我最终也会忘记*为什么*。

## 在主题行中使用祈使语气

*祈使语气*意为“*用来要求或要求一个动作的进行*”。这是 Git 默认使用的语气，您也应该这样做。

例如，`git revert`命令将为您创建一个所谓的“恢复提交”。这个提交将有一个带有命令语气的主题行——“恢复‘将字段添加到模式’”。

你可以想一想，你是在告诉系统/app/等。如何做人。一开始可能会很尴尬，但你会习惯的。

> 注意:你可以用任何你喜欢的心情来写提交体。

## 主题行长度限制为 50 个字符

你见过 GitHub 上以`…`结尾的提交消息吗？这是因为 GitHub 会截断超过 72 个字符的主题行。主题行的其余部分移到提交正文。

50 个字符是 GitHub 推荐的。限制的存在是为了使提交更具可读性。而且你不得不以简洁易懂的方式解释你的代码变化。

如果你无法用 50 个字符解释你的代码变化，你可能一次提交了太多的变化。如果是这种情况，您应该将提交分成多个提交。

# 结论

这是在编写 Git 提交消息时要遵循的三个最重要的规则。如果你遵循这些规则，你的同事会很快理解提交的内容。

总结一下:

*   关注为什么
*   在主题行中使用祈使语气
*   主题行长度限制为 50 个字符

要了解更多 git 提交的最佳实践，请查看[这篇优秀的博文](https://cbea.ms/git-commit/)。

*在*[*Twitter*](https://twitter.com/prplcode)*、*[*LinkedIn*](https://linkedin.com/in/simeg)*或* [*GitHub*](https://github.com/simeg) 上与我联系

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)