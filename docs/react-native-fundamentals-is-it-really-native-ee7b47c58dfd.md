# 反应原生基础——真的是“原生”吗？

> 原文：<https://levelup.gitconnected.com/react-native-fundamentals-is-it-really-native-ee7b47c58dfd>

为什么你的应用感觉比网站更好

![](img/25f355dca44e800f6aef3697aae483e5.png)

[Unsplash](https://unsplash.com/s/photos/ios-developer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [AltumCode](https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

今天早上，我像往常一样开始了新的一天。醒来，煮咖啡，在沙发上找个座位，在 iPad 上浏览一些新闻。

我正在阅读的故事嵌入了一个视频，所以我点击了播放，但看到它通过应用内浏览器打开了 YouTube，我感到很恼火。我马上开始寻找“在应用程序中打开”按钮，但是唉；它没有出现。再加上一个 15 秒钟的无遗漏广告……算了吧。

这个小小的不便让我思考:我为什么要在乎？为什么会有人在意？为什么脸书、推特、谷歌和其他公司都在维护移动应用，而他们的网络应用运行得非常好？

相信我，当我作为一名**应用程序开发人员开始工作之前，这是一个令人不安的想法。**也就是说，我完全支持事情变得更快、更简单，所以如果不需要原生应用，那就让我们摆脱它们吧。

这就把我们带回了主要问题:“本土”是什么意思？React Native 甚至真的是“本土”吗？。这是我在学习 React Native 时的主要困惑点之一，我相信理解这一点是真正了解该平台的优势和劣势的关键。

# 定义原生。

为了帮助我们解开“本地”反应本地到底是什么，我们需要定义一些术语。我们通常认为应用程序属于以下三类之一:

![](img/4737cef3ff7938d026d92fffcdad7981.png)

电路板由 [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 放在 [Unsplash](https://unsplash.com/s/photos/motherboard?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上。美国宇航局在 [Unsplash](https://unsplash.com/s/photos/web?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的全球照片

**Native:** *专门编程在目标操作系统上运行，在没有附加软件层的设备上运行。*

**Web:** *主要依靠浏览器技术——只是显示网页。*

混合: *使用操作系统特定代码和平台无关代码的组合来创建一个应用程序，通常在一个代码库中用于多个平台。*

> *注意:许多人用“本地”这个词来指代任何可安装的应用程序；任何不是网站的东西。正因如此，人们有时会将* ***原生*** *应用称为* ***纯原生*** *或* ***裸原生*** *。*

# RN 是什么？

React Native 是一个创建混合应用的框架。

我们不是用两三种不同的语言编写应用程序，而是全部用 JavaScript 编写。然后我们捆绑所有的 JavaScript，捆绑我们的应用程序，并在设备上运行。为了进行广泛的简化，React 本机框架负责:

*   运行 JavaScript
*   JavaScript 运行时和主线程之间的通信
*   与本机 API 通信
*   **创建本地 UI 元素**

最后一点才是 React 原生应用比 Web 应用感觉更好的真正核心: *React 原生创建了* ***原生视图*** *它们天生快速且响应迅速，因为* *它们不会受到一些附加软件层的阻碍。*尽管我们用(相对)缓慢的 JavaScript 编写业务逻辑和创建 UI，但 UI 本身是 100%原生的。

对于在浏览器上访问的“网络”移动应用和普通网站，只有一个本地视图:实际的浏览器窗口。里面的一切都是用 HTML、CSS 和 JavaScript 绘制的，这比原生视图的性能差得多。出于这个原因，苹果一直在反对这种风格的应用程序，经常拒绝他们，因为他们违反了[应用程序商店审查指南](https://developer.apple.com/app-store/review/guidelines/#beta-testing)的“最低功能”条款:

> 4.2 最小功能
> 你的应用应该包括功能、内容和用户界面**，使其超越一个重新包装的网站。如果你的应用程序不是特别有用、独特或“像应用程序一样”，它就不属于应用程序商店。**

# PWAs 呢？

![](img/89a8823a9d9f7e89707bf36e52f5f0dc.png)

黑仔注册护士？

pp 经常被宣传为创建可安装的移动应用程序的一种简单方式。PWA 是一个网站，[包含一些附加信息](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Installable_PWAs)，允许移动设备“安装”它。一旦应用程序被安装，它可以像一个真正的应用程序一样打开和多任务，发送推送通知，甚至离线工作。

对许多人来说，这似乎是一个快速而简单的替代反应原生，尤其是如果你已经有很好的移动网络产品。然而，该应用程序在主屏幕上有一个图标的事实并没有解决上述基于网络的应用程序的任何弱点。主要是:**他们不能提出本土观点。他们对本地 API 的访问也非常有限。出于这个原因，我认为 PWAs 基本上与基于网络的应用程序没有什么不同。**

# 那么是本土的吗？

React Native 占据了 Native 和 Web 之间微妙的中间地带。它不是一个简单的 Web wrapper，但它肯定不是一个纯粹的原生 app。

普遍的共识是，精心制作的 React 本机应用程序提供了与纯本机应用程序没有区别的体验，同时将开发成本和复杂性减半。

从纯功能的角度来看，PWA 或基于网络的应用程序可以被认为是一个很好的注册护士替代方案，但为什么这应该是我们唯一关心的问题呢？用户知道什么时候一个应用是以“足够好”的态度放在一起的。他们可能不会直接抱怨，他们当然也不会有技术洞察力去了解你的应用是建立在什么基础上的，但是他们会注意到。React Native 旨在弥合业务效率和卓越用户体验之间的差距，因此我们的妥协保持在最低限度。

很明显，React 原生应用和纯原生应用之间存在技术差异，但从用户的角度来看:**它们通常是相同的。**尽管 React Native 带来了自己的挑战，但同时为许多平台创建**原生体验**的能力是无与伦比的。