# 在你的 Flutter 应用中实现 Firebase 动态链接

> 原文：<https://levelup.gitconnected.com/implementing-firebase-dynamic-linking-in-your-flutter-app-3e9290dd3bc0>

![](img/94db1e40d681b7e1668d643d044f636b.png)

费利西亚·黑山在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

*动态链接*是智能网址，允许你将现有的和潜在的用户发送到你的 iOS 或 Android 应用程序中的任何位置。

而*深度链接*，用最简单的话来说，就是直接链接到你的应用程序内容的能力。不是简单地启动应用程序，让用户停留在主屏幕上，而是点击一个深层链接，将用户带到应用程序中的一个特定屏幕。想想产品页面、简介、新内容或购物车。

在我和 Flutter 合作的一个项目中实现动态链接时，我使用了 Firebase——Google 的移动应用程序开发平台，帮助你构建、改进和发展你的应用程序。

在继续之前，我假设您对什么是 Flutter 以及 Firebase 如何与 Flutter 结合使用有基本的了解。

在弄清楚如何实现这个特性时，我利用了不同的资源和文章来支持我的项目中的动态链接工作。其中一些文章是:

[Firebase + Flutter —动态链接—逐步指南](https://blog.devgenius.io/firebase-flutter-dynamic-links-step-by-step-guide-630402ee729b)

[使用颤振和燃烧基处理动态链接](/handling-dynamic-links-using-flutter-and-firebase-2be5f6a85e1f)

[关于动态链接的 Firebase 文档](https://firebase.google.com/docs/dynamic-links/flutter/receive)

通过这些文章的帮助，我能够在我的应用程序中成功地“打开”动态链接。“打开”是因为在这个时候，点击动态链接只是打开了应用程序，并继续运行应用程序，而没有将用户导向任何点。这在当时是一个双赢的局面，我找不到全面的文章来展示如何在应用程序中处理这些数据。大多数文章都停留在通过动态链接打开应用程序的阶段。在 Firebase 的文档中，这是他们的方法:

为了澄清，后端开发人员负责生成动态链接，而我负责使用应用程序中的链接。为了给这个问题更多的背景，这个应用程序是一个社交媒体平台，有不同的帖子，问题，资源，提要，个人资料等部分……你知道这个想法，因此，我应该在点击动态链接时将用户导航到这些不同的部分。

我通过这些方法解决了这个问题:

**与后端开发者联络**:我与后端开发者联络，以确定要嵌入动态链接的深度链接的类型。Firebase 动态链接可以选择将一个短 URL、长 URL 和 ***深度链接*** 嵌入到动态链接中。这个深度链接应该是一个有效的网址，当 firebase 由于一些原因无法打开你的动态链接时，比如没有安装 flutter 应用程序，firebase 与你的应用程序链接不正确，等等，这个深度链接将用于引导用户到这个网址

**找出处理数据的方法**:在应用程序已经安装并正确链接到 firebase 的情况下，我能够通过操作动态链接中包含的深层链接并提取有用的数据，将用户带到应用程序中的不同位置。

例如，一个动态链接，[*【https://example.page.link/UmjXpFJZcmVnw7FX9】*](https://example.page.link/UmjXpFJZcmVnw7FX9)可能包含这种深度链接，[*【https://example.com/question/6245ae78170fad9eac3143af】*](https://example.com/question/6245ae78170fad9eac3143af,)，使用这种格式，我能够通过以下方式处理到不同屏幕和动态页面的导航:

从上面的代码片段中，我们可以看到数据是如何被获取、格式化和利用来进行不同的 API 调用，然后再推送到相应的屏幕。这个控制器/功能/类应该在最顶端的窗口小部件树中被调用，也就是说，在窗口小部件树中的一个点，它确定用户在执行核心动作/功能之前将要到达。例如，这个特定的控制器在包含我的 BottomNavigationBar 的小部件上被调用，BottomNavigationBar 是我的小部件树中的顶部小部件。因此，我们确信这个方法会被恰当地调用。

好了，这就把我们带到了这篇文章的结尾。如果你觉得这篇文章有用，你可以投一个赞，关注，或者两者都投:)。下次再见👍

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码](https://levelup.gitconnected.com/)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)
*   🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/)