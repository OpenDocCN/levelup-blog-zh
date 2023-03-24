# NRG 堆栈的崛起:Node.js、React 和 GraphQL

> 原文：<https://levelup.gitconnected.com/the-rise-of-the-nrg-stack-node-js-react-and-graphql-6dfba468ba8a>

![](img/2491a3d17dd42aa344ae399c61f5a922.png)

虽然所有的框架和语言都有各自的优势，但 Node.js 在企业开发中已经变得非常流行，并且现在大多数财富 500 强公司都在使用它。

自从脸书将 React 的 BSD 许可改为 MIT 之后，这个库似乎被全球开发社区大量采用。

此外，脸书发布了 GraphQL 查询语言，这是构建 API 的一种革命性的新方法。许多公司也从 RESTful 转向内部使用的 GraphQL APIs。诸如 IBM， [Twitter](https://about.sourcegraph.com/graphql/graphql-at-twitter) ，[沃尔玛实验室](https://medium.com/walmartlabs/open-sourcing-lacinia-our-graphql-library-for-clojure-96a4ce5fc7b8)，[纽约时报](https://open.nytimes.com/react-relay-and-graphql-under-the-hood-of-the-times-website-redesign-22fb62ea9764)， [Intuit](https://quickbooks-engineering.intuit.com/graphql-on-the-edge-12b6d60064b0) ， [Coursera](https://speakerdeck.com/jnwng/introduction-to-graphql) 等等，不一而足。其他公司不仅将其内部的 ***外部的***API 转换为 GraphQL。其中一些公司是 [AWS](https://docs.aws.amazon.com/appsync/index.html#lang/en_usl) 、 [Yelp](https://www.yelp.com/developers/graphql/guides/intro) 、 [GitHub](https://developer.github.com/v4) 、[脸书](https://developers.facebook.com/docs/graph-api)和 [Shopify](https://help.shopify.com/en/api/custom-storefronts/storefront-api/getting-started) (等等)。GitHub 甚至没有考虑 REST API，因为他们的 v4 只支持 GraphQL。

随着 GraphQL 越来越受欢迎，React 成为前端首选框架，10 年后 Node.js 证明了它的存在，NRG 堆栈似乎正在成为当今 web 应用的标准。

# 为什么是 NRG 堆栈？

这些技术中的每一种都能够独立存在，并且有许多优点已经在网上很好地介绍过了，我们将很快重点介绍几个。

## 更容易学习和雇佣

由于 Node.js 和 React 都是 JavaScript，对于开发者来说，最大限度地缩小了前端和后端技能的差距。这意味着他们可以是全栈开发人员，而不必精通其他语言。这也意味着团队的每个成员都可以很容易地对应用程序的内部运作有更多的了解。

## 服务器端呈现(SSR)

服务器端呈现是一种流行的技术，用于在服务器上呈现单页应用程序(SPA ),然后将完全呈现的 HTML 页面发送给客户端。然后，客户端的 JavaScript 包可以接管，SPA 可以照常运行。想法是最初在服务器上呈现应用程序，然后在客户端利用 SPAs 的功能。

*SSR + SPA =通用 App(或者网上一些文章所说的‘同构 App’)*

服务器端渲染的主要好处是大大改善了用户体验。如果您不是在 Node.js 环境中构建东西，那么 SSR 极具挑战性。

## 高阶组件(HoC)

高阶函数是可以将另一个函数作为参数并返回一个函数作为结果的函数。高阶分量是接受一个分量并返回一个新分量的函数。当使用 React 和 GraphQL 时，GraphQL 客户端利用[高阶组件](https://reactjs.org/docs/higher-order-components.html)来获取数据，并使其可用于它们的`props`中的 React 组件。

# 数据库在哪里？

NRG 堆栈是数据库无关的，GraphQL 不是数据库的查询语言，而是 API 的查询语言。

> [“数据库只是一个你不需要马上搞清楚的细节。”—鲍伯·马丁叔叔](https://blog.cleancoder.com/uncle-bob/2012/05/15/NODB.html)

传统的 REST 端点返回固定的数据结构，通常带有客户端不需要的额外信息，需要多个端点(和 HTTP 调用)来获取所有必要的信息。相比之下，GraphQL 没有。

使用 GraphQL，客户端可以准确地指定它想要的数据，并且(只)从单个端点获取数据，而不管数据来自哪个数据库。

# 我该如何开始？

了解 NRG 堆栈或其他任何东西的最好方法是用它来构建一些东西。

## [火神](http://vulcanjs.org/)

Vulcan 是一个框架，它为您提供了一套快速构建基于 React & GraphQL 的 web 应用程序的工具。开箱即用，它可以处理数据加载，自动生成表单，处理电子邮件通知，等等。就我个人而言，我已经与 VulcanJS 一起工作了几个月，这是一次令人惊奇的经历，促使我最终撰写了这篇文章。这最适合 web 应用程序。

## [**盖茨比**](https://www.gatsbyjs.org/)

Gatsby 是一个基于 React 的免费开源框架。在我看来，最适合网站。

## [反应商务](https://blog.reactioncommerce.com/your-retail-tech-stack-matters-part-two/)

将您的商业愿景变为现实，将遗留系统和千篇一律的平台抛在身后。Reaction Commerce 是一个实时、开放的商务解决方案，专为雄心勃勃的企业零售商的规模和增长而打造。正如你可能已经猜到的，它是最好的电子商务解决方案。

## [**阿波罗通用入门套件**](https://apollokit.org/)

一个尖端的入门项目，帮助创建高效的 GraphQL
客户端、服务器和移动应用。

## [react-full stack-graph QL](https://github.com/graphql-boilerplates/react-fullstack-graphql)

React & Node.js 的 Fullstack GraphQL 样板为您的 GraphQL 服务器提供了完美的基础，无论您是刚刚开始使用 GraphQL 还是打算构建一个成熟的应用程序。

## [Next.js](https://nextjs.org/)

一个流行的 React 框架，侧重于可以与 GraphQL 一起使用的 SSR。

# 最终想法…

软件栈是一组工具，不应该与软件架构混淆。假设你正在用 NRG 堆栈(**或任何其他堆栈**)构建一个应用程序，就像你正在用铁和橡胶建造一辆汽车一样。这没有告诉我们任何关于汽车发动机设计的信息。使用这组推荐的工具将解决一组特定的挑战，但不是所有的挑战。

感谢您的阅读！别忘了评论，也请分享你的想法。

🐦 [**在 Twitter 上关注我！**](https://twitter.com/yair_tal)