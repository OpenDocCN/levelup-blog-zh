# 前端代码现在可以在后端运行了？

> 原文：<https://levelup.gitconnected.com/wintercg-community-is-officially-established-front-end-code-can-run-on-the-back-end-now-760174894978>

## 它能实现“一次编写，随处运行”吗？

![](img/3ca49dbf7a395a8906bbe1b5e339aeec.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Bernd Dittrich](https://unsplash.com/@hdbernd?utm_source=medium&utm_medium=referral) 拍摄的照片

最近 [Cloudflare](http://www.cloudflare.com/) 在其官方博客上宣布，它与 Vercel、Shopify 以及 Node.js 和 Deno 的个人核心贡献者合作，正式推出了一个新的社区团体——winter CG。它的全称是 **Web 互操作运行时社区组**，专注于标准化 web APIs 在非 Web 浏览器、基于 JavaScript 的开发环境中的互操作实现。

那么 JavaScript 真的可以“一次编写，随处运行”吗？

# 他们想解决什么问题？

在 Cloudflare Workers 这样的无服务器环境中，或者在 Node.js 和 Deno 这样的运行时环境中，许多要求与 web 浏览器环境中的相同。例如这些 API:[fetch()](https://fetch.spec.whatwg.org/)， [ReadableStream 和 WritableStream](https://streams.spec.whatwg.org/) ， [URL](https:%20//url.spec.whatwg.org/) 等。

[W3C](https://www.w3.org/) 和 [Web 超文本应用技术工作组](https://whatwg.org/)(或 WHATWG)的主要目标是为 Web 浏览器环境提供标准化 API 的制定和维护。并且那些非浏览器运行时已经实现了它们自己的定制的、特别的解决方案。这导致开发人员在切换环境时要密切关注它们。

所以新成立的 WinterCG 将试图改变这种状况。它们提供了一个场所来讨论和倡导所有 web 环境的共同需求，部署在整个堆栈的任何地方。

# 对我们开发者有什么好处？

虽然 Cloudflare Workers、Node.js、Deno 和 web 浏览器互不相同，但它们共享许多共同的功能。例如，它们都提供了发送 HTTP 请求的能力，它们都提供了用于生成加密散列的 API，等等。

想象一下，如果他们都为此实现了相同的通用标准，那么当迁移到不同的环境时，开发人员根本不需要重写它来保持做完全相同的事情。

所以我们开发人员只需要关心我们编写的代码能正常运行，不管代码在哪里运行。这意味着在浏览器中正常运行的 JavaScript 代码可以迁移到后端代码中正常运行。

![](img/cbc7b1c257abf6216c8c7aa79d779306.png)

照片由[马尔特·赫尔姆霍尔德](https://unsplash.com/@maltehelmhold?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 他们要做什么？

WinterCG 不打算发布自己的一套独立的标准 API。WinterCG 产生的新规范的想法被提交到 W3C 和 WHATWG 的现有工作流中进行考虑。如果 Web 浏览器对新规范没有特别的需求和兴趣，WinterCG 将被授权推进自己的规范，但这些规范不会对已建立的 Web 标准产生任何影响。

所以对于我们这些开发者来说，学习成本会更低。

# 他们目前的工作进展如何？

他们目前从事的工作包括:

## 最低通用 Web API

> “最小通用 web 平台 API 是标准化 Web 平台 API 的精选子集，旨在定义基于浏览器和非浏览器 JavaScript 的运行时环境的最小通用功能集。”

这是来自[草案规范](https://common-min-api.proposal.wintercg.org/#intro)中的介绍。换句话说，这些 API 是现有 web APIs 的最小集合，将在 Node.js、Deno 和 Cloudflare Workers 中一致且正确地实现。

这个列表包含了我们常见的[事件](https://dom.spec.whatwg.org/#event)， [URL](https://url.spec.whatwg.org/#url) ，globalThis。 [self](https://html.spec.whatwg.org/multipage/window-object.html#dom-self) ，globalThis。[控制台](https://console.spec.whatwg.org/#namespacedef-console)，globalThis.navigator. [用户代理](https://html.spec.whatwg.org/multipage/system-state.html#dom-navigator-useragent)，globalThis。[setTimeout()](https://html%20.spec.whatwg.org/multipage/timers-and-user-prompts.html#dom-settimeout)/global this。 [clearTimeout()](https://html.spec.whatwg.org/multipage/timers-and-%20user-prompts.html#dom-cleartimeout) ，globalThis。[setInterval()](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#dom-setinterval)/global this。 [clearInterval()](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#dom-clearinterval) 以此类推。更多细节可以在这里找到[。](https://common-min-api.proposal.wintercg.org/#index)

## 网络加密流

浏览器中的[网络加密 API](https://www.w3.org/TR/WebCryptoAPI/) 不支持对称加密算法的流式输入和输出。这严重限制了加密操作的性能和可扩展性。所以 WinterCG 已经开始为网络加密流起草新的规范，该规范将提交给 W3C 考虑。目标是以符合现有标准的方式将流加密操作带到整个网络，包括网络浏览器。

## 服务器 fetch()的子集

Node.js 在`18.x`提供了 WHATWG 标准化 fetch() API 的实现。然而，这些非浏览器运行时和 web 浏览器实现 fetch()的方式有很大的不同(例如，没有 CORS 跨源验证，等等)。).

因此很难就 fetch 标准达成共识，因此 WinterCG 正在为这些不同的需求和约束制定 fetch 标准的子集。这个子集将与浏览器环境的 fetch 标准完全兼容，并且它还将作为如何在这些非浏览器环境中正确实现 fetch 的文档指南。

# 结论

新的 [Web 互操作运行时社区组](https://github.com/wintercg)(或“WinterCG”)试图实现的关键目标是最大化“Web 互操作性”。这意味着以一种*相同或者至少尽可能一致的方式*实现特性，这种方式与那些特性在 web 浏览器中的实现方式相同。

这些 JavaScript 运行时环境之间存在某些差异，这些差异也会影响标准化 API 的设计决策。例如，在后端环境中，没有 CORS 跨源机制和对本地文件系统的访问限制。所以我觉得有一小部分重叠的功能需求可能会被直接重用和实现，那些冲突的地方可能会被精心设计。

但无论如何，对于我们这些开发者来说，我们写的 JavaScript 移植性更强，平台迁移成本更低。这无疑给 JavaScript 增添了新的活力。你觉得怎么样？

# 参考

[1]https://wintercg.org/

[2][https://blog.cloudflare.com/introducing-the-wintercg/](https://wintercg.org/)

[https://www.w3.org/community/wintercg/](https://www.w3.org/community/wintercg/)

[https://deno.com/blog/announcing-wintercg](https://deno.com/blog/announcing-wintercg)

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说很重要——谢谢。