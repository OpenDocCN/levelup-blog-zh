# 在 Node.js — 2020 版中进行 HTTP 请求的 5 种方法

> 原文：<https://levelup.gitconnected.com/5-ways-to-make-http-requests-in-node-js-2020-edition-19d142a96681>

![](img/3810eeae6c7913d2e1f1f73de6c3da5a.png)

学习如何发出 HTTP 请求可能会让人感到不知所措，因为有几十个可用的库，每个解决方案都声称比上一个更有效。一些库提供跨平台支持，而另一些库侧重于包大小或开发人员体验。在本帖中，我们将探索在 Node.js 中实现这一核心功能的五种最流行的方法。

代码演示将使用指环王主题的 API，[一个 API 来统治所有的](https://the-one-api.dev/)，用于所有的交互——仅仅是因为我上周末无意中看完了这个精彩的系列。

# 先决条件

确保您的机器上安装了 [npm 和 Node.js](https://nodejs.org/en/download/) ，您就可以开始了！

更喜欢提前跳？这篇文章将涵盖:

*   [HTTP(标准库)](https://www.nexmo.com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition#http)
*   [SuperAgent](https://www.nexmo.com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition#super-agent)
*   [Axios](https://www.nexmo.com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition#axios)
*   [节点获取](https://www.nexmo.com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition#node-fetch)
*   [拿到了](https://www.nexmo.com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition#got)

# HTTP(标准库)

标准库配备了默认的`http`模块。该模块可用于发出 HTTP 请求，而无需添加外部包。然而，由于该模块是低级的，它不是对开发人员最友好的。此外，您需要使用[异步流](https://nodejs.org/api/stream.html#stream_streams_compatibility_with_async_generators_and_async_iterators)来分块数据，因为 HTTP 请求的异步/等待特性不能用于这个库。然后需要手动解析响应数据。

下面的代码演示了如何使用标准的`http`库发出一个`GET`请求来检索指环王系列中的书籍名称:

# 超级特工

[SuperAgent](https://github.com/visionmedia/superagent) 是一个小型 HTTP 请求库，可用于在 Node.js 和浏览器中发出 AJAX 请求。SuperAgent 有[几十个插件](https://github.com/visionmedia/superagent#plugins)可用来完成诸如防止缓存、转换服务器有效负载或前缀或后缀 URL 等任务，这一事实令人印象深刻。或者，您可以通过编写自己的插件来扩展功能。SuperAgent 还可以方便地为您解析 JSON 数据。

> 可用于浏览器的 SuperAgent 缩略版只有 6KB(缩略并压缩),在开发人员中非常受欢迎。

在终端中输入以下命令，从 npm 安装 SuperAgent:

以下代码片段展示了如何使用 SuperAgent 发出请求:

# Axios

[Axios](https://github.com/axios/axios) 是一个基于 promise 的 HTTP 客户端，用于浏览器和 Node.js。与 SuperAgent 一样，它可以方便地自动解析 JSON 响应。让它更加与众不同的是它对`axios.all`发出并发请求的能力——例如，这将是同时从《指环王》电影*和*中检索引文的有效方式。

在终端中输入以下命令，从 npm 安装 Axios:

以下代码片段展示了如何使用 Axios 发出请求:

# 节点获取

[Node Fetch](https://github.com/node-fetch/node-fetch) 是一个轻量级模块，它将 Fetch API 引入 Node.js。通过 Fetch(在浏览器中或通过 Node Fetch ),您可以混合使用`.then`和`await`语法，以便更好地将可读流转换为 JSON——因此`data`,如下面的代码片段所示，拥有 JSON 而不需要一个笨拙的中间变量。此外，请注意，重定向限制、响应大小限制、用于故障排除的显式错误等有用的扩展可用于节点获取。

在终端中输入以下命令，安装从 npm 获取节点:

以下代码片段展示了如何使用节点获取来发出请求:

# 得到

[Got](https://github.com/sindresorhus/got) 是 Node.js 的另一个直观而强大的 HTTP 请求库。它最初是作为流行的[请求](https://www.npmjs.com/package/request)(现已废弃)包的轻量级替代而创建的。要了解 Got 与其他库相比如何，请查看这张[详细图表](https://github.com/sindresorhus/got#comparison)。

与 Axios 和 SuperAgent 不同，Got 在默认情况下不解析 JSON。请注意，`{ json: true }`是作为参数添加到下面的代码片段中的，以实现该功能。

> 对于现代浏览器和 [Deno](https://deno.land/) 用法，背后的人得到了生产 [Ky](https://github.com/sindresorhus/ky) 。Ky 是一个基于浏览器获取 API 的小型 HTTP 客户端，没有任何依赖性。

在终端中输入以下命令，从 npm 安装 Got:

以下代码片段展示了如何使用 Got 发出请求:

# 包扎

这篇文章演示了如何使用 Node.js 中目前最流行的一些库来实现 HTTP 请求功能。

其他语言也有无数的库来处理 HTTP 请求。你希望我们接下来写什么语言？让我们知道！我们很乐意在 [Twitter](https://twitter.com/VonageDev) 或 Vonage [开发者社区 Slack](https://developer.nexmo.com/community/slack) 上听到你的想法或回答任何问题。

*最初发布于*[*https://www . NEX mo . com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition*](https://www.nexmo.com/blog/2020/09/23/5-ways-to-make-http-requests-in-node-js-2020-edition)