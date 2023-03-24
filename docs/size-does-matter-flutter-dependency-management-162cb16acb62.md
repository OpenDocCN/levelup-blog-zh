# 大小很重要——颤振依赖性管理

> 原文：<https://levelup.gitconnected.com/size-does-matter-flutter-dependency-management-162cb16acb62>

![](img/321179d7b5e6ea1697b1ff8459b1bf10.png)

你好👋我叫 Luke，是一名软件开发人员。在我与 JS 包管理人员一起工作的这些年里，我从来没有想过事情可以用不同的方式来完成。我只是接受了它的工作方式。直到我开始和 Flutter 合作。有东西发出了声音。以下是我的想法👇

> 我将主要比较 Flutter package/dependency management solution 和 npm，因为大多数主要的前端框架都内置了它。

# 1.我们都习惯了什么，它是如何飘动的

大多数前端框架都是围绕像 npm 这样的包管理器构建的，这些包管理器下载并存储每个项目目录下的依赖项，这些目录都是众所周知的、黑暗的整体大小的目录`node_modules.`

![](img/197ce123b267c90b60578777c4e59b68.png)

因此，如果你是一个在多个前端项目上工作的开发人员，你肯定有几个 node_modules 目录。事情是这样的——开发者是习惯性的生物，当涉及到我们使用的软件包时，我们通常有自己的选择。可以肯定地说，这些 node_modulse 中的每一个都有相似的(如果不是相同的)依赖关系。如果 node_modules 不是每次都那么大就不是问题了。

例如:

您从事三个常规的、非抖动的前端项目，其中 node_module 平均重量约为 600 MB，大约为这些依赖项分配了 1.8 GB 的内存。

这就是 Flutter 之前的一切。

## Flutter 如何处理依赖性

Flutter 不像 npm 那样为每个项目单独下载依赖项，而是将它们下载到一个目录中:

```
~/.pub-cache
```

这个目录在你所有的 Flutter 应用之间共享。

这种微妙变化的结果是，我们不必在每个项目中存储相同依赖项的副本。

给你一些数字——我最近做了大约 12 个颤振项目。这些项目的所有依赖项都使用与这三个 node_module 项目大约相同的存储量— **1.8GB，**。

在我看来，Flutter 做得更好。在这种情况下，尺寸确实很重要。

## JS 依赖管理器还有希望吗？

当然有！不仅仅是因为这篇文章更多的是展示 npm 和 Flutter 在处理依赖关系上的不同。Npm 完成了工作，并且已经为它的目的服务了很长时间——这才是最重要的！

当我对经理们做了一些调查时，我和我的朋友聊得很开心，他告诉我关于 [yarn workspaces](https://classic.yarnpkg.com/en/docs/workspaces/) 的事情，它的工作方式类似于 Flutter 在一个地方存储包的方式，但不是开箱即用。这更像是维护 monorepo 包的一种方式，而不是驻留在主机上的所有依赖项。我可能错了，请在这里[确认一下](https://classic.yarnpkg.com/en/docs/workspaces/)。

在这个快速研究中出现的另一个工具是 [lerna](https://lerna.js.org) ，它似乎是 yarn workspaces 提供的更高级版本。

如果你是一个 JS 开发人员，我强烈推荐你看看这两个工具！

看看我所发现的，我确信其他的依赖管理器将会出现，他们肯定会带来新的东西。肯定有希望！

# 不仅如此，文档呢？

我曾经试图创建一个 npm 包。我失败了，但那是很久以前的事了，所以我又试了一次。让我告诉你没那么糟糕…

不过，我觉得 Flutter 做得更好。我想提升我的自尊，并认为我使用不同的工具变得更有效率了😉

除此之外，我仍然认为 npm 文档与 Flutter 的相比有所欠缺。如果你不相信我，自己看看 [npm 文档](https://docs.npmjs.com/getting-started)和[颤振文档](https://flutter.dev/docs/development/packages-and-plugins)。

在我看来，首先谈论付费账户然后谈论特性的文档从一开始就注定要失败。

我问了一些我的开发朋友关于 npm 和他们正在使用的特性。我特别询问了他们关于我在 Flutter 中使用的特性，我知道这些特性在 npm 中是可用的。他们的回答？他们不知道。为什么？他们要么从未看过 npm 文档，要么很久以前就看过。

所以三页的 Flutter 文档在这方面更胜一筹，因为我使用了它描述的几乎所有内容。向颤动队致敬💙

我喜欢为我的插件和包使用 monorepo 来保持一切整洁和愉快🙂

# 摘要

2021 年 Npm 岁了。如果你觉得自己老了，你是对的！是不是说明 npm 不好？一点也不。如果你有这种感觉，请记住，通常新事物感觉更好，但只是因为他们从哥哥那里获得了知识和经验——在这种情况下，npm，然后复制了好的，忽略了坏的。

现在是时候让年长的包管理人员吸取 Flutter 的优点并实现它了！这就是开源的意义所在。让事情变得更好！

Flutter 的做法很棒，希望 JS 包也有类似的解决方案。或者它已经在那里了？如果您知道这样的解决方案，请与班上的其他人分享！写评论！

> 这篇文章的原标题是:“为什么依赖管理在 Flutter 中比其他前端框架更好。”——我简单地抛弃了它，因为我不再那样想了。我的研究不仅改变了我的观点，还让我学到了超出我预期的东西。😂

再次感谢阅读！如果您有任何问题，请在 [Twitter](https://twitter.com/ThatLukeUrban) 上问我，我的 DMs 是开放的。你可以跟着我去那里和这里，在媒体上！

我关于颤振的其他文章:

*   [为什么我不在 Flutter 中使用 Navigator 2.0 我的模式](https://itnext.io/why-i-m-not-using-navigator-2-0-in-flutter-my-pattern-6a88a8625f6c)
*   [Flutter 不再是一个跨平台的框架——它不仅仅是一个平台。](https://itnext.io/flutter-is-no-longer-a-cross-platform-framework-b53c87b14c39)
*   [DoYouEvenFlutter [EP.4]在 2021 年使用这个 Flutter VSCode 设置提高您的编码效率](https://luke-urban.medium.com/doyouevenflutter-ep-4-boost-your-coding-productivity-with-this-flutter-vscode-setup-in-2021-60637f05a5c2)

我还为那些处理多个不同版本的 Flutter 项目的人创建了一个 Flutter 版本管理器。

[https://github.com/lukeurban/flenv](https://github.com/lukeurban/flenv)👈

*让代码与你同在！*

卢克(男子名)