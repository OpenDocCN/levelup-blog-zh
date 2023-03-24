# 2017 年前端开发回顾

> 原文：<https://levelup.gitconnected.com/a-recap-of-front-end-development-in-2017-7072ce99e727>

前端工程在 2017 年再次以狂热的速度进化。以下是过去一年中最引人注目的事件。

[](https://gitconnected.com/learn) [## 查找最佳编码教程和课程-学习编码| gitconnected

### 使用我们完整的编码资源列表学习任何编程语言或框架。我们分享、汇总和排名…

gitconnected.com](https://gitconnected.com/learn) ![](img/b0d049bf432fed44da9173aaf9349212.png)

# React 16 和麻省理工学院许可证

React 继续主导前端领域，2017 年提供了迄今为止最受期待的版本之一[版本 16](https://edgecoders.com/react-16-features-and-fiber-explanation-e779544bb1b7) 。它包括支持异步 UI 呈现的纤程架构。这个版本还通过提供错误边界以及[许多其他特性](https://edgecoders.com/react-16-features-and-fiber-explanation-e779544bb1b7)，使得管理意外的应用程序故障变得更加容易。

令人惊讶的是，在过去的一年中，React 最重要的改进不是新特性，而是对其开源许可的改变。脸书放弃了导致公司远离 React 的 BSD 许可，并采用了[用户友好的 MIT 许可](https://code.facebook.com/posts/300798627056246/relicensing-react-jest-flow-and-immutable-js/)。此外，Jest、Flow、Immutable.js 和 GraphQL 也获得了 MIT 许可。

核心团队和顶级贡献者包括[贡献者](https://medium.com/u/2cd0dccd3af3#contributors))包装的 [**glamor**](https://github.com/threepointone/glamor) 库。查看这篇文章，获得许多可用的 CSS-in-JS 选项的摘要。

[](/a-brief-history-of-css-in-js-how-we-got-here-and-where-were-going-ea6261c19f04) [## CSS-in-JS 简史:我们是如何来到这里的，我们将走向何方

### 对正在进行的 CSS-in-JS 故事的非个人化、高水平的描述

levelup.gitconnected.com](/a-brief-history-of-css-in-js-how-we-got-here-and-where-were-going-ea6261c19f04) 

# **静态站点生成**

2017 年，随着静态网站的回归，时间发生了扭曲。像 [Gatsby](https://www.gatsbyjs.org/) 这样的框架让你能够使用 React 和其他现代工具来构建静态网站。不是每个网站都需要或者应该是一个复杂的现代网络应用程序。静态站点生成为您提供了服务器端呈现的好处和无与伦比的速度，因为您提供的是预构建的标记。如果你正在寻找一个好的例子，[官方的 React 文档](https://reactjs.org/)本身就是使用 Gatsby 构建的。

静态网站生成引发了另一种被称为 [JAMStack](https://jamstack.org/) 的趋势:“JavaScript、API、标记”。JAMStack 使用相同的静态预建 HTML 文件以及可重用的 API 和 JavaScript 来处理请求/响应周期中的任何动态编程。对于免费开始使用 JAMStack 和静态托管来说，Netlify 是一个很好的选择。 [Brian Douglas](https://medium.com/u/8cae2b42cc85?source=post_page-----7072ce99e727--------------------------------) 写了一篇优秀的文章[，通过构建黑客新闻克隆，比较了 JAMStack 与服务器端渲染应用](https://www.netlify.com/blog/2017/06/06/jamstack-vs-isomorphic-server-side-rendering/)。

[](https://www.gatsbyjs.org/blog/2017-09-18-gatsby-modern-static-generation/) [## 用盖茨比生成现代静态站点

### 在这篇文章中，我将谈论静态站点生成器——它们是如何发展的，以及为什么我从 Ghost powered…

www.gatsbyjs.org](https://www.gatsbyjs.org/blog/2017-09-18-gatsby-modern-static-generation/) 

# GraphQL 爆炸，让我们重新思考 API 构造

GraphQL 似乎正在快速占领 REST 的地盘，而[萨梅尔·布纳](https://medium.com/u/c64c4b529a5d?source=post_page-----7072ce99e727--------------------------------)甚至声称 [REST 已经死了](https://medium.freecodecamp.org/rest-apis-are-rest-in-peace-apis-long-live-graphql-d412e559d8e4)。GraphQL 不需要管理多个端点和获取不必要的数据，而是允许客户端声明性地定义它需要的数据，并从单个端点检索所有数据。

它变得如此流行，以至于 [GitHub](https://medium.com/u/8df3bf3c40ae?source=post_page-----7072ce99e727--------------------------------) 已经用 GraphQL 编写了其 API 的[最新版本，许多公司正在开发产品使其对所有开发者开放，例如由](https://developer.github.com/v4/) [Johannes Schickling](https://medium.com/u/ff75ba3095a6?source=post_page-----7072ce99e727--------------------------------) 开发的流行的 [Graphcool](https://medium.com/u/122d169c63c0?source=post_page-----7072ce99e727--------------------------------) 框架。

[](http://graphql.org/) [## GraphQL:一种 API 查询语言。

### GraphQL 在您的 API 中提供了完整的数据描述，使客户能够准确地要求他们…

graphql.org](http://graphql.org/) 

# 反应路由器 4

由 Ryan Florence[和 Michael Jackson](https://medium.com/u/162352c45b6e?source=post_page-----7072ce99e727--------------------------------)[设计的 React 路由器](https://medium.com/u/7f9567e0d71c?source=post_page-----7072ce99e727--------------------------------)从仅仅是 React 的一个路由器发展成为真正的 React 路由器——一个简单使用 React 组件实现的声明式路由器。这是 React 团队认可的第一个版本。API 已经稳定，并且 [React 培训](https://medium.com/u/643a8bc3f0f?source=post_page-----7072ce99e727--------------------------------)团队已经声明在项目的生命周期内不会看到任何大的突破性变化。

# Angular 发布了 v4，紧接着是 v5

在臭名昭著地跳过版本 3 来维护 SEMVER 之后，Angular 4 于 3 月 23 日正式发货。在版本 4 中，Angular 团队采用了社区项目 Angular Universal——它提供了一种服务器端渲染 Angular 应用程序的方法——作为 Angular 项目的官方部分。Angular Animation 包也是从`@angular/core`中提取的，仅在需要时导入，并且视图引擎中的提前编译已针对性能进行了重构，“在大多数情况下，为您的组件生成的代码大小减少了约 60%。”

第 5 版采用了更多期待已久的改进。由于新的`@angular/service-worker`包，用 Angular v5 创建渐进式 Web 应用程序现在比任何早期版本都容易得多。Angular 编译器也得到了改进，在开发过程中支持更快的构建/重建，Angular 路由器现在公开了所有新的生命周期挂钩，包括 ActivationStart、ActivationEnd、ResolveStart 和 ResolveEnd。

# 打字稿和流程

TypeScript 在许多 JavaScript 开发者中赢得了狂热的追随者，而 [Flow](https://flow.org/) 提供了一种更灵活的方式来引入类型，而无需激进的重构。很多人抱怨 JavaScript 缺少类型。TypeScript 是由微软创建的，是 Angular 新版本中的一个要求。“心流”是脸书的创意。

# gitconnected 为开发者创建社区

gitconnected 推出了为开发人员和软件工程师创建社区。它提供了与其他开发人员协作、共享文章和创建讨论的能力。此外，您可以在您的个人资料页面上无缝显示您的项目和投资组合。不要错过与和你有共同兴趣的人联系的机会，互相帮助学习和成长。

[](https://gitconnected.com) [## git connected——开发者和软件工程师社区

### 分享文章和参与讨论——git connected 让你与其他开发者和一切保持联系…

gitconnected.com](https://gitconnected.com) 

# 2018 年有什么期待

*   当我们想出在基于组件的应用程序中处理样式的最佳方法时，CSS 之争愈演愈烈。
*   更多的公司采用具有统一代码库的移动解决方案，如 [React Native](https://facebook.github.io/react-native/) 或 [Flutter](https://flutter.io/) 。
*   随着离线功能和无缝移动体验的出现，网络变得更加本地化。
*   WebAssembly 可以取得巨大进步，提供更好的网络体验。
*   GraphQL 继续挑战 REST。
*   React 加强了它的地位(是的，甚至更多),因为它不再担心许可证了。
*   Flow 和 TypeScript 占据了更强的优势，给了 JavaScript 更多的结构。
*   集装箱化对前端架构的影响变得更加普遍。
*   虚拟现实使用像 [A-Frame](https://aframe.io/) 、 [React VR](https://facebook.github.io/react-vr/) 和 [Google VR](https://developers.google.com/vr/?hl=en) 这样的库向前迈进。
*   人们使用区块链和 [web3.js](https://github.com/ethereum/web3.js/) 构建一些非常酷的应用程序(作者是[马雷克·科特维奇](https://medium.com/u/257f634897bf?source=post_page-----7072ce99e727--------------------------------)和[法比安·沃格斯泰勒](https://medium.com/u/4b59b3ef14a2?source=post_page-----7072ce99e727--------------------------------))。

如果我错过了什么重要的东西，请留下评论，我一定会补充的！

*如果您觉得本文有帮助，请点击*👏*。* [*关注我*](https://medium.com/@treyhuffine) *了解更多关于 React、Node.js、JavaScript 和开源软件的文章！你也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*

```
Recommend & share..
```