# 我从为开源做贡献中学到的 3 件事

> 原文：<https://levelup.gitconnected.com/3-things-i-learned-from-contributing-to-open-source-8bfd9adf7ef6>

## 这个星期，我一直在为另一个[静态站点生成器](https://github.com/ycechungAI/cmd-ssg) (SSG)做贡献。我挑选了一些可能对第一次开源贡献者有用的东西

![](img/6f46f8d5543c2ceccc044465529f49b7.png)

这个星期，我一直在为另一个[静态站点生成器](https://github.com/ycechungAI/cmd-ssg) (SSG)做贡献。我挑选了一些可能对第一次开源贡献者有用的东西:

*   沟通是你最好的盟友
*   对变化要谨慎
*   尊重作者的编码风格

# 沟通是你最好的盟友

![](img/75900b7c3a0d5bf5d5dbffa5573a8bb7.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [krakenimages](https://unsplash.com/@krakenimages?utm_source=medium&utm_medium=referral) 拍摄

如果没有 SSG 回购公司的所有者尤金的帮助，我不可能起步。我不能在我的机器上安装或运行 SSG，所以我决定为此提交一个[问题](https://github.com/ycechungAI/cmd-ssg/issues/20)。幸运的是，Eugene 回复了我并指出问题的根源:我的 Node.js 版本不符合要求。幸运的是，事件发生后，先决条件的文档得到了更新。在更新了 Node 并检查了 SSG 是否如预期的那样工作后，我准备开始深入代码库。

# 对变化要谨慎

![](img/1124907e3b367e96c11b28f49441142e.png)

当在现有代码基础上构建时，我发现最具挑战性的是对它进行足够的修改。如果不熟悉其奇怪的实现逻辑和代码架构，在其上进行构建简直如履薄冰。我本想[在 SSG 中添加 Markdown 支持](https://github.com/ycechungAI/cmd-ssg/issues/18)，但是我不小心用赋值操作符(`=`)替换了逻辑 OR 赋值操作符(`||=`)从而破坏了它。在这种情况下，你可以依靠的英雄不是别人，而是版本控制先生(比如 Git)。

# 尊重作者的编码风格

![](img/d31fbb35eb8a9afe0d790c2a05ff56ec.png)

在发出拉请求之前检查我的代码时，我意识到我已经按照我的编码风格写了所有的东西。虽然这不是一个主要问题，但我认为它会破坏代码库的一致性，使它不那么简单。例如，我更喜欢用大写字母命名常量变量。然而，在 camel case 中，它们中的大多数已经被命名，所以我试图坚持 Eugene 的命名约定。我知道这可能不是最好的做法，但我觉得尊重别人的做法，总比只是为了证明自己是对的而争论好。

# 幸福的结局

谢天谢地，我的拉请求在我第一次尝试时就被接受了。我的代码不是 100%干净的，所以 Eugene 可能还没有时间彻底检查它。然而，我仍然把它作为对一个开源项目的另一个成功贡献。

至于尤金，他也[为](https://github.com/oliver-pham/silkie/issues/10)[西尔基](https://github.com/oliver-pham/silkie)《我的 SSG》贡献了同样的特写。我注意到他的代码可能需要修复和重构，所以我们在 Slack 和 GitHub 上合作来解决这些问题。考虑到我们的时间限制和 Eugene 缺乏 Python 经验，我们成功地在不破坏现有特性的情况下添加了一个新特性。

*原载于 2021 年 9 月 24 日*[*https://dev . to*](https://dev.to/oliverpham/3-things-i-learned-from-contributing-to-open-source-3lma)*。*