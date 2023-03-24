# flutter vs React Native——2021 年哪个最好？

> 原文：<https://levelup.gitconnected.com/flutter-vs-react-native-which-is-best-in-2021-9cf575cbc546>

![](img/f4428065ca3dbf7f6bef4061dcc0f1db.png)

如果你想在 2021 年建立一个跨平台的移动应用，你的两个最佳选择是 Flutter 和 React Native。

这两个框架都越来越受欢迎，并承诺快速开发、接近原生的性能和流畅的 UI——所有这些都来自一个代码库。但是它们有什么不同呢？而 2021 年挑哪个框架最好？请继续阅读，寻找答案。

我将首先简要介绍这两个框架。然后，我将带您直接比较 React Native 和 Flutter。在本指南结束时，您将对为您的下一个项目选择哪个跨平台框架有一个坚实的理解。

# 什么是颤振？

[Flutter](https://flutter.dev) 是一个跨平台的 app 开发框架。Flutter 允许你用一个代码库为 iOS、Android、macOS、Windows 和 web 开发应用。

# 谁创造了颤振？

Flutter 是谷歌在 2018 年创建的。然而，Flutter 是一个开源项目，由不同的开发人员和其他公司的社区维护。

# 有哪些流行的 app 是用 Flutter 做的？

相当多的公司已经用 Flutter 开发了大型应用——尽管它是一个如此年轻的框架。

谷歌正在各种应用程序中使用 Flutter，如谷歌助手、谷歌广告，甚至是大型游戏应用程序 [Stadia](https://flutter.dev/showcase) 。

但其他公司也采用了这一框架，并取得了巨大成功。中国科技巨头，如阿里巴巴、腾讯、百度和字节跳动(抖音应用程序的创始人)至少在他们的一些应用程序中采用了 Flutter。

宝马、易贝、飞利浦、Sonos 和 Groupon 也在各种移动应用程序中使用了 Flutter。他们中的许多人报告说开发速度更快，性能更好。

宝马正在用 Flutter 构建他们的 iOS 和 Android 应用

我最喜欢的一个优秀应用程序的例子是日记应用程序[。它使用了各种自定义界面和动画，向您展示了 Flutter 的可能性。Reflectly 应用的开发者甚至写了一篇文章，解释他们为什么从 React Native 转换到 Flutter。](https://reflectly.app)

以上所有例子都将 Flutter 用于移动应用。桌面和网络支持是 Flutter 的新增功能，因此还没有被广泛使用。

你可以在 [Flutter showcase 网站](https://flutter.dev/showcase)上找到使用 Flutter 的应用程序的更新列表。

# 什么是 React Native？

React Native 是一个用于构建用户界面的跨平台移动框架。它利用 React 框架和 Javascript 的力量来开发 iOS 和 Android 应用程序。

# React Native 是谁创造的？

脸书在 2015 年创建了 React Native。该公司试图找到一种方法，在两个主要的移动平台上分享其部分开发成果。

今天，脸书仍然积极维护和进一步发展该框架。然而，React Native 也是开源的，并且有一个由开发人员和公司组成的大型社区。

# 有哪些流行的 app 是用 React Native 制作的？

自然，许多脸书手机应用程序至少部分是由 React Native 开发的。如果你在体验 React Native 之前使用过脸书、Messenger 或 Instagram。

其他公司的许多其他流行应用程序开始使用 React Native。Wix、Shopify、Discord Tesla 和 Uber Eats 都在他们的一些移动应用程序中使用 React Native。

Shopify 的[商店应用](https://shop.app)是广受欢迎且实现良好的 React 本地应用的最新例子之一。

# 颤振 vs 反应原生:编程语言

Flutter 使用现代的 Dart 编程语言。如果您过去使用过任何 C 风格的语言，Dart 会感觉很熟悉。Dart 是面向对象的，但是包含了许多特性，使它可以作为函数式语言使用。另一方面，React Native 使用优秀的旧 Javascript。

如果你有 Javascript 背景，你会注意到这两种语言之间的许多相似之处。例如，Dart 对异步代码使用承诺(称为未来),并支持 async/await 语法。

当把两种语言放在一起看时，很明显 Dart 的创造者受到了 Javascript 的启发。然而，Dart 改进了 Javascript 的许多薄弱环节。

与动态 Javascript 相比，Dart 是静态类型的。最近兴起的 [Typescript](https://www.typescriptlang.org) (为 Javascript 添加类型)证明了许多开发人员厌倦了由于缺乏类型化代码而导致的可避免的错误。

从 Dart 版本开始，这种语言也是空安全的。这意味着当你定义一个变量时，你需要明确地声明它是否允许空值。以我的经验来看，这个特性非常重要。您多久收到一次 Javascript 中与变量为`null`或`undefined`相关的错误？一整类的虫子就这么消失了。

通过使用 Typescript，可以将一些 Dart 功能引入 React 本机项目。我强烈推荐这种方法，因为它使您的代码库不容易出错，对于新开发人员来说可读性更好。然而，Typescript 不能解决 Javascript 的所有问题。如果只是并排比较语言特性，Dart 可能会胜出。

Javascript(甚至是 Typescript)比 Dart 有巨大的优势——它是世界上最流行的语言之一。Javascript 广泛用于 web、NodeJS 中的后端应用程序，甚至物联网。这意味着围绕 Javascript 有一个更大的社区。

也有更多的 Javascript 开发人员可以雇佣。当决定为您的项目选择 Flutter 或 React Native 时，这可能是一个关键因素。

如果是找工作的开发人员，应该学习哪个框架？目前，React Native 提供了更多的工作。你还将学习网络相关知识，市场上有大量的工作机会。可供 Flutter 选择的工作越来越少，但需求似乎在快速增长:

综上所述，Dart 作为编程语言有很大的特点，比如类型和空安全。然而，Javascript 更受欢迎，并且有一个更大的生态系统。

Dart 有一张王牌，这使得它非常适合开发跨平台应用程序。这是我们接下来要看的。

# Flutter 与 React Native:性能和用户体验

为了真正理解 Flutter 和 React Native 在性能和用户体验方面有何不同，我们需要更深入地了解它们是如何工作的。

先说 React Native。

React Native 利用 Javascript 生态系统和 React 核心库。这意味着您编写的大部分代码都是用 Javascript 编写的。

移动设备本身并不处理 Javascript。那么，如何将您的 Javascript 代码翻译成移动应用程序呢？React Native 使用本机桥在用 Javascript 编写的代码和本机代码之间传递消息。

React Native 的团队花了很多时间来优化*桥*的性能，但从根本上说，它仍然是一个瓶颈。这就是为什么一些 React 本机库，如 React Native Reanimated，使用复杂的架构设置来实现更好的帧速率。

React Native 的核心架构很难针对本机性能进行优化。

您在 React Native 中使用的组件，例如文本输入或按钮，仍然是本机组件。你只需要*通过 Javascript 接口控制*它们。

如果你希望你的应用程序尽可能接近你选择的平台的设计，iOS 或 Android，这是一个好消息。React 本地应用将很好地采用当前平台的设计，看起来非常类似于本地编写的应用。

然而，如今大多数公司都希望在任何设备上实现一致的体验。他们希望他们的应用程序有一致的品牌——颜色、字体和表单语言。在 React Native 中，仅仅因为你的应用在 iOS 上工作和看起来很棒，并不意味着它在 Android 上会看起来一样！

Flutter 使用一种完全不同的方法来创建跨平台应用程序。Flutter 使用 Dart 编译 Javascript(用于 web 上的 Flutter)和机器码。

这意味着 Flutter 应用程序一旦被编译，就可以实现快速的性能。

Flutter 不会在屏幕上呈现本地组件。它使用一个名为 Skia 的渲染引擎，从头开始绘制你的界面和动画。

这使您可以完全控制 UI。您可以实现完全自定义的表单和动画。Flutter 还为 Android(这些是材料设计小部件)和 iOS(称为 Cupertino 小部件)实现了原生组件。

但是，不要被它们看起来与实际组件的接近程度所迷惑。它们是从零开始画在屏幕上的，就像 Flutter 中的其他东西一样。

缺点是 Flutter 团队需要实现他们框架中的每个接口组件。新的组件可能要过一段时间才能进入框架。

虽然你可以让你的应用看起来像 Flutter 中的原生设计规范，但它可能不是这项工作的合适工具。您将编写大量分叉逻辑，但仍可能得不到您想要的结果。

如果你想在两个平台上有一个一致的设计，那么 Flutter 真的很棒。在大多数情况下，你的应用在 iOS、Android 和其他平台上看起来是一样的。

# 在网络上自然地颤动和反应

到目前为止，我们一直在关注移动应用。如果您希望在本地移动应用程序和 web 之间获得一致的体验，该怎么办？

如果你需要你的网站有很好的搜索引擎优化，你可能不会喜欢下面的选项。如果你想优化你的网页，使其对公众开放，最好还是单独构建你的网络体验。

但是，如果你的网站在认证后受到保护，或者你不太关心 SEO，你可以认真考虑 Flutter 和 React Native Web。

[React Native Web](https://necolas.github.io/react-native-web/) 不同于 ReactJS，即最初的 Web 框架。React Native Web 允许您利用移动代码库，只需针对 Web 做一些调整。 [Twitter](https://twitter.com) 一直在使用 React Native Web 提供网站体验，并取得了巨大成功。

React Native Web 已经变得相当成熟，甚至包含在流行的 React Native 开发框架 [Expo](https://expo.io) 中。此时，您可以安全地重用 React Native 中的大部分代码，并在 web 上使用它！

Flutter 在 3 月份的最新版本 Flutter 2 中正式添加了 web 支持。这意味着纤维网的颤动在生产中使用是稳定的。

一个很有前途的 web 应用程序的第一个例子是 Rive 应用程序。Rive 已经重建了他们的动画工具，使之能够在网络和桌面平台上使用 Flutter。结果令人印象深刻。

Dart 就像前面提到的 Javascript 一样，是一个显式的编译目标，这使得它能够生成干净的、高性能的 web 应用程序。

然而，Flutter 应用程序不像典型的 Html 结构。Flutter 利用[网络画布](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)来定制绘制你的 UI。这意味着搜索引擎支持很差，可能永远不会很好。

但它也能带来一些意想不到的好处。web 从来不是为特定类型的复杂数据驱动的应用程序而构建的。例如，Google 最近用 web canvas 实现了他们的 Docs 应用程序，而不是原生的 Html `input`或`textarea`元素。协作编辑器是很难用传统的 web 工具箱构建的东西之一。

如果你想建立遵循 web 标准的可公开访问的网站，你最好的办法仍然是为 web 使用单独的代码库。你可以使用像 NextJS 这样的框架，它有很多加载图片和提高 SEO 的优化。

如果您使用 React Native 并且很好地构建了代码库，您甚至可以重用大多数应用程序逻辑和状态。您只需要替换接口组件层。

如果你使用的是 Flutter，你必须用 Dart 之外的另一种语言维护一个单独的代码库。

因此，在选择 React Native 或 Flutter 之前，请认真考虑您的项目以及 web 应用程序的真正需求。

# 在桌面上自然地颤动和反应

React Native 和 Flutter 项目都可以编译成桌面应用。

在 React Native 的[中，最有前途的库](https://microsoft.github.io/react-native-windows/)是由微软开发的(谁能想到呢？).这是 React——以及更广泛的 Javascript——世界中的常见模式。您经常需要安装额外的库来扩展框架。

React Native for macOS 和 Windows 仍处于早期阶段，尚未发布 1.0 版本。然而，它承诺在 React 生态系统中开发高性能的桌面应用，并且当你选择 React Native 时，它可能会成为一个很好的替代选择。

Flutter 在过去几年中一直致力于桌面支持，并在他们的稳定渠道中发布了它。目前还没有太多的参考应用，但是由于这个框架从一开始就支持任何平台，所以它是本地桌面应用的更好选择。

# 哪个比较好学？反应原生还是扑？

这要看你以前合作过什么。你有 Javascript 方面的经验吗？React Native 将是最容易上手的。但是，Dart 类似于 Javascript。学习 Dart 的基础知识并开始编写 Flutter 应用程序不会花你太多时间。

这两个框架在 Youtube、Udemy、Medium 和其他地方都有无穷无尽的内容，可以帮助您入门和寻找特定主题的帮助。

Dart 和 Flutter 的文档也是很好的开始。它简明地介绍了语言和框架。

您是否正在构建一个需要

*   平滑动画
*   在所有平台上看起来都相似
*   需要卓越的桌面原生体验
*   不需要 SEO 或语义 Html

颤振不会错的！

Flutter 有一个年轻但蓬勃发展的生态系统，并有一个良好的架构基础，让您可以在自己选择的平台上创建任何界面。

你正在开发一个应用程序

*   适应目标平台的设计语言
*   主要在手机上推出
*   是用 ReactJS 网络应用程序开发的
*   是由一个网络工程师团队开发的

挑反应原生！

React Native 用于您日常使用的大型应用程序中。它是久经考验的，有一个广泛的开发者社区和围绕它的软件包生态系统。

*最初发表于*[*【https://johannes.co】*](https://johannes.co/flutter-vs-react-native-in-2021)*。*

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)