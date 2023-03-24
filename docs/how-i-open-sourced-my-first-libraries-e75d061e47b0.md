# 我如何开源我的第一个库

> 原文：<https://levelup.gitconnected.com/how-i-open-sourced-my-first-libraries-e75d061e47b0>

![](img/0fe6ac6382d7856447fc13613c763dec.png)

马特·邓肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是我发布开源库的方法。在过去的几个月里，我做了一些个人项目，我决定把一些部分拿出来，作为独立的库开源。

在建立自己的私人项目的同时给予社区价值和贡献是一种奇妙的感觉。我决定花些时间完善我的库，让它们“准备好开源”。当有人使用它们时，我非常高兴，我感到感激和有用:我的工作使别人的生活更容易:)

我是开源社区的新手，我所做的可能是无用的或完全错误的，欢迎反馈！我开发了三个不同的库( [LiveData](https://github.com/Marplex/LiveData) 、 [AdaptiveGrid](https://github.com/Marplex/AdaptiveGrid) 和 [flarebase-auth](https://github.com/Marplex/flarebase-auth) )，但是我使用了相同的策略来准备和“营销”它们。在这篇文章中，我将告诉你我个人是如何接近开源的，从写代码到拥有我的第一颗 GitHub 之星(不是我自己的😀)

# **单据**

你在野外出版你的作品，你的目标是解释你所面临的问题以及你是如何解决的。每个人都应该能够阅读和理解你的代码，所以一定要添加适当的文档。

评论你的代码库。当你打字的时候，解释你脑子里在想什么，这样其他人就能理解你的思维方式。不要用太多的时间，但要写得好像 10 年后有人会读(并理解)一样。

# **测试**

你想出版正确的作品吗？如果它是一个会严重影响其他软件的复杂库，确保为它编写测试。有很多很好的方法来构建经过实战测试的稳定软件，例如 TDD(测试驱动开发)就是其中之一。

如果您想了解更多关于 TDD 的知识，我在这里写了一篇关于它的文章:

[](https://marplex.medium.com/the-one-thing-every-junior-developer-needs-to-know-about-tdd-5755be7b8b3b) [## 关于 TDD，每个初级开发人员都需要知道的一件事

### 为什么测试驱动开发对高质量软件至关重要

marplex.medium.com](https://marplex.medium.com/the-one-thing-every-junior-developer-needs-to-know-about-tdd-5755be7b8b3b) 

更好的是部署一个简单的 CI/CD 管道(例如，使用 GitHub 操作)在提交和发布之前运行测试。

# **自述文件**

自述文件是您的登录页面。这是人们访问您的存储库时看到的第一件事，所以要确保做得正确。以下是我在撰写自述文件时最看重的三件事:

有一个标题，用简短的描述来解释你的库是做什么的。在文本中，请确保包含您的项目所涵盖的标签和主题。例如，我的库 flarebase-auth 可以与 Firebase 和 Cloudflare 一起工作，我已经在描述中添加了这两个词。这将使你的自述页面 SEO 友好，并可能帮助你接触到更多的人。

解释用户在哪里可以找到你的库，以及如何安装。链接到您的软件包仓库，并编写它们必须执行的命令来安装您的库(npm install，dotnet install…).即使这听起来微不足道，但它减少了摩擦，鼓励你的用户试用你的库。

最后但同样重要的是，解释一下如何使用它。包括代码片段和有用的演示，可以复制和粘贴以快速测试您的库。小而实用的例子是解释你的库如何工作的最简单快捷的方法(别说了，给我看代码)

# **包装**

让你的用户容易安装和试用你的作品。将库打包并发布到你喜欢的包库中，我的 Javascript 库在 NPM 和 GitHub 包中。如果你想发展，就用 NuGet。NET，使用 Maven/JitPack 如果你用 Kotlin/Java…

# **营销**

现在，您已经有了一个经过适当记录和测试的库，带有一个很好的自述文件，并发布在流行的包存储库上。问题是，没人用。没有星星，没有交通，什么都没有。

是时候向世界展示你的成就了。有很多方法可以做到这一点，我目前所做的是:

> -在 Reddit 上发布
> 
> -在 Dicord 社区上写关于它的文章
> 
> -撰写关于 dev.to 和我的媒体简介的文章

再看前面的例子， [flarebase-auth](https://github.com/Marplex/flarebase-auth) 是用 JavaScript 编写的，可以与 Cloudflare 和 Firebase 一起使用。所以我在 Firebase 的 Reddit 社区和 CloudFlare 的 Discord 上发布了这个消息。我还在 Medium 上写了一篇文章来解释和介绍这个库。通过这样做，我获得了第一批 14 颗星和早期用户。

## **下一步是什么**

当你开始收到拉动请求、新的讨论和问题时，这意味着是时候维护项目了。我以前从未有过这样的机会，但我相信，尽管这可能会成为一份“第二职业”，但对我个人来说，这肯定是有益的。

我希望你喜欢阅读这篇文章。如果你愿意支持我当作家，可以考虑报名 [*成为中等会员*](https://marplex.medium.com/membership) *。你可以以每周不到 1 美元的价格无限制地使用所有的媒体。*

[*作为媒介会员，你的会员费直接支持我和你阅读的其他作家。你也可以在媒体上看到所有的故事。*](https://marplex.medium.com/membership)

[](https://marplex.medium.com/membership) [## 通过我的推荐链接加入 Medium-Marco ci molai

### 阅读马可·西莫莱(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

marplex.medium.com](https://marplex.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)