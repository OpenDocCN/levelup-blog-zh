# React 性能提示:构建 React 应用程序时需要了解的 15 项技术

> 原文：<https://levelup.gitconnected.com/15-performance-tips-which-you-need-to-know-when-building-react-js-application-441eb76da779>

![](img/7039c80bed3de28b21ab43f663b3f5d3.png)

[Duomly —编程在线课程](https://www.duomly.com)

构建应用程序有时很有挑战性，尤其是当我们需要构建一个快速且高质量的产品时。

拥有一个表现良好的应用程序可以提高 SEO 排名，还可以增加用户对应用程序的参与度和交谈率。

为了帮助你做到这一点，我创造了一些提示和技巧，你可以用它们来让你的应用程序运行得更快。

## 1.使用 Preact

您可以做的一件事是使用 Preact，这是一个小得多的反应替代方案。

如果我们比较大小，React 大约为 130kb，Preact 大约为 16kb。这带来了巨大的不同，尤其是当我们将要 gzip 我们的库的时候。gzipped React 大约 40kb，gzipped Preact 大约 4kb(大约小 10 倍！).

代价是 Preact 没有完全的兼容性，所以您必须了解库之类的东西(例如，redux-forms)。

## 2.使用 React Lazy 将你的应用分成几个部分

我们可以将代码分成更小的块，而不是将整个应用程序作为一个文件加载。然后在第一次加载之后，我们将只下载我们需要的组件。

为此，我们需要通过以下方式导入组件:

```
const componentName = React.lazy(() => import(‚../componentName’));
```

就我而言，`React.lazy`给了我巨大的好处。在第一个视图中，我们只加载了大约 100kb 的文件(而不是最初的 800kb ),我们的 FCP(第一个内容绘制)大约是 1.8-2s。

## 3.使用 CDN

内容交付网络允许我们从离客户端(我们的用户)最近的位置加载静态文件，这有助于我们避免延迟。亚洲和美国之间的延迟有时可能高达 5 秒。例如，我们可以使用相对容易配置的 Cloudflare，您可以使用免费帐户。Cloudflare 不仅为我们提供了一个 CDN，而且它还具有 DDOS 保护、代理(这使得潜在攻击者很难获得我们服务器的 IP)、SSL 证书、缓存等功能，甚至可以缩减我们的代码。

## 4.S3 的主持人

你知道吗，你可以很容易地在文件托管服务上托管你的前端，比如 S3？

在 S3 存储静态资产非常便宜。你可以最大限度地降低被攻击的风险，如果你把 S3 和 CDN 结合起来，向客户端(用户)发送前端文件简直快如闪电。

## 5.删除不用的代码(如何检查)

如果您使用 Semantic 或 Bootstrap 之类的库，并且意外地加载了整个库，那么通常会包含 300-400 kb 的未使用代码。这段代码是不需要的，如果删除它，可以显著提高您的速度。

要找到未使用的代码，你可以打开 Chrome developer tools，进入“Sources”选项卡，向下进入“Coverage”部分，开始记录(就像在网络选项卡中一样)。然后重新加载您的网站，您应该会看到哪些文件包含最大量的未使用代码。

你可以手动删除这些代码，或者通过像 babel-plugin-remove-dead-code 这样的插件来删除。

## 6.仅从包中导入函数

当您只需要其中的一部分时，导入整个库可能会降低性能。

例如，当我们导入整个`lodash`库时，它重 71kb (24kb gzipped)。然而，如果我们只加载`get`方法，它的重量将是 8kb (2kb gzipped)。

为此，我们需要导入选定的函数，如:

`import get from 'lodash/get'; // good`

不加载整个库:

`import lodash from 'lodash'; // bad`

## 7.删掉你的班级名字

如果我们把我们的类变得更小，我们可以减少很多包的大小。

例如，我们并不总是需要像`className=’red-rounded-purchase-button’`那样命名元素的 CSS 类。说`className=’red-buy-btn’`或者使用 Webpack 插件就足够了，这些插件可以把它改成类似于`className='c73'`的东西。

在某些情况下，它可以为我们节省 60%的捆绑包大小。

## 8.不要让你的应用程序过于复杂

如果你构建一个简单的应用，你不需要 Redux/GraphQL 和 Apollo 甚至 CSSModules。有些应用程序在这方面非常有用，但总的来说会让你的应用程序大几百 kb。

在许多情况下，您可以轻松地使用内置功能，如上下文或挂钩。

## 9.正确配置 Webpack

您可以配置 Webpack 来创建块，缩小您的代码(CSS 和 JS)，甚至删除 console.log、注释和死代码，这非常有用。

## 10.缩小代码

缩小是从代码中删除不必要字符的过程。它可以为我们节省大量的大小和提高执行时间。

## 11.避免过多渲染

我们的应用程序每次渲染都需要额外的执行时间。在很多情况下，我们会在不需要的时候多次渲染组件。我们可以通过在列表中正确使用`React.memo`、`shouldComponentUpdate`或`React.PureComponent`、`key`来防止附加，不使用 props 作为初始状态，用 React 上下文传递数据。

## 12.使用 React。碎片

如果我们使用`<React.Fragment></React.Fragment>`而不是 div，我们可以减少 DOM 元素和包的大小。每个 div 都需要一个来自 React 的`createElement`调用，并且可能需要更多的 DOM 访问，这非常慢。

## 13.优化图像

巨大的图像、字体和图标可能是 web 开发人员的噩梦。

我们可以通过使用压缩器将图像缩小 80%。

## 14.不要用图标加载整个字体

不用加载 200kb 的图标，我们可以选择几个我们需要的图标，并用它们创建一个字体。

在我们的案例中，它帮助我们从大约 250kb 减少到 1.5kb

## 15.使用性能监视器

如果我们想要监控我们的应用程序，我们首先需要检查组件的渲染速度。我们想看看使用 react-addons-perf 浪费了多少时间。

我们可以使用的另一个工具是 why-did-i-update，它将向我们显示哪些组件被重新呈现，以及我们应该在哪里关注重构。

对我来说最有用的工具之一是 webpack-bundle-analyzer，它显示了我的块有多大。我可以决定如何使它们变得更小，以及如何规划我的代码结构以避免双重依赖。

## 结论

我希望你喜欢我的内容，这些提示将有助于你构建和优化你的应用程序！

感谢您的阅读，来自 Duomly 的 Radek

![](img/2bebe9fe48fb99c5d1c4456e97533030.png)

[Duomly —编程在线课程](https://www.duomly.com)

本文最初发表于:
【https://www.blog.duomly.com/react-js-performance-tutorial/ 