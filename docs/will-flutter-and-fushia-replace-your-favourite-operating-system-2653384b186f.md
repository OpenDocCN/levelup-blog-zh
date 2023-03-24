# Flutter 和 Fuchsia 会取代你喜欢的操作系统吗？

> 原文：<https://levelup.gitconnected.com/will-flutter-and-fushia-replace-your-favourite-operating-system-2653384b186f>

## Fuchsia 不仅仅是 Android 的替代品，它还有一个总体规划。

![](img/016a8af515c1a8a68e175675cafcafa2.png)

克里斯汀·休姆在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Fuchsia 是谷歌正在开发的新操作系统。大多数人都知道 Fuchsia 是众所周知的 Android 操作系统的替代品。谷歌已经开发和改进了两个操作系统:Chrome OS 和 Android。正如我们所看到的，两个操作系统都很稳定，并且运行良好。那么，谷歌为什么要开发新的操作系统呢？Fuchsia 团队甚至从内核级别构建它。

Chrome OS 基于 Linux。另一方面，Android 也使用 Linux 内核。由于所需的内核级特性，Google 显然为 Linux 内核修改维护了单独的开发分支。此外，他们经常用新实现的特性对原始存储库做出贡献。Linux 内核几乎为所有 web 提供动力，现在非常稳定。然而，谷歌想要的几个重要特性在 Linux 内核架构中缺失了。根据我的观察，谷歌建立一个新的操作系统的原因如下。

*   Android 操作系统没有一个符合谷歌预期的精心设计的设计。Android 最初有基于 JIT 编译的 [Dalvik](https://en.wikipedia.org/wiki/Dalvik_(software)) 运行时来执行应用程序。后来，他们引进了基于 AOT 编译的[艺术](https://en.wikipedia.org/wiki/Android_Runtime)。尽管如此，Android 需要用额外的工具从 APK 文件中生成受支持的二进制文件。同样，进一步的改进将使 Android 系统变得臃肿、粗糙和复杂。
*   谷歌需要摆脱 Java。Oracle 的新许可模式让 Java 社区很不高兴。谷歌有自己的编程语言 Dart。
*   如果谷歌有一个新的操作系统，他们对设备有更多的控制权。无声的内核级升级不会有什么大不了。
*   Linux 内核遵循单一的设计模式。换句话说，整个操作系统核心运行在一个具有堆叠模块的进程中。如果一个模块崩溃，整个内核都会崩溃。

# 倒挂金钟有一个微内核。

如前所述，Linux 内核是一个运行在内核空间上的大型进程。因此，不可能容易地自动升级模块。此外，单片内核设计是一种旧的不太安全的方法，因为设备驱动程序也在内核空间中工作。另一方面，微内核模式将操作系统的模块分解成孤立的服务，称为服务器。每个服务器都可以通过进程间通信(IPC)通道与其他服务器通信。与整体内核设计不同，如果一个服务器出现故障，整个微内核不会出现故障。

微内核设计支持快速升级，因为每个内核模块都是一个独立的组件。微内核方法是解决 Android 碎片问题的好方法。Android 碎片化指的是不同手机制造商创造的各种 Android 风格的存在。在这种情况下，谷歌无法直接为所有 Android 设备发送内核级升级。微内核设计解决安卓碎片化。然而，微内核也有几个缺点。主要问题是微内核的运行速度比单片内核慢。这种缓慢是由于客户端-服务器架构的通信通道造成的。由于这个问题，Windows 和 XNU (Mac)内核遵循单片和微型模式，称为混合模式。

也许，谷歌选择微内核模式是出于现代操作系统的需要。比如加强安全性，内核动态更新，稳定性。此外，他们可能有不同的概念来优化微内核实现的通信方法。

# 从零开始学习。

毫无疑问，谷歌拥有世界上最优秀的工程师。他们与每一个流行的操作系统和每一个操作系统 API 密切合作。因此，紫红色将是世界上最优秀的头脑与他们的经验的结果。记住，他们为高性能计算场景开发了 Golang。Golang 提供了一种对人类友好的语法，具有很好的性能，不同于任何其他现有的语言。例如，C/C++语言有很好的性能，但缺乏开发人员友好的语法。另一方面，类似 Python 的语言具有开发人员友好的语法，但不会表现出良好的性能因素。

同样，所有现有的操作系统都有几个痛点——没有完美的操作系统。Windows 是一个广泛使用的操作系统，但在 Windows XP 版本之后确实显得臃肿。下面的故事解释了现代 Windows 版本中存在的关键问题。

[](https://medium.com/swlh/why-i-switched-to-linux-after-using-windows-for-10-years-247de78058ef) [## 用了 10 年 Windows 之后换了 Linux

### 我是 Windows 98、2000、XP、7 和 10 的粉丝。但是，我最终决定永远用 Ubuntu。

medium.com](https://medium.com/swlh/why-i-switched-to-linux-after-using-windows-for-10-years-247de78058ef) 

对于开发者和用户来说，macOS 有太多不必要的限制。GNU/Linux 很棒，但它是许多开发者构建的不同组件的集合——没有明确定义的标准。事实上，谷歌拥有所有这些知识。因此，他们可以通过最小化现代操作系统中存在的这些问题来构建操作系统。这些原因使得 Fuchsia 有更大的机会成为有史以来最成功的操作系统。

# Flutter 帮助 Fuchsia 受欢迎。

Flutter 是现在流行的框架。它首先进入跨平台移动应用程序开发市场。之后，[也通过进入跨平台桌面应用开发市场来警告电子](https://medium.com/swlh/goodbye-electron-welcome-flutter-22b3dc10d2f3)。谷歌表示，Flutter 也为 Fuchsia 编译应用程序。但是我们并没有那么重视。我的观点是，Flutter 是作为 Fuchsia 的主要应用程序开发工具包而构建的，就像。NET framework for Windows。也许，Flutter 团队最初专注于 Android 和 iOS，以解决他们当前的移动应用开发问题。此外，瞄准 Android 和 iOS 是接触开发者社区的一个好方法——因为还没有人知道 Fuchsia 到底是什么。

当每个人都倾向于用 Flutter 制作他们的应用程序时，当谷歌发布 Fuchsia 时，这些应用程序将原生地与 Fuchsia 一起工作。

# 与其他操作系统的竞争

显然，Fuchsia 将成为谷歌设备的默认操作系统:Chromebook、谷歌眼镜、Pixel 和 Nest(谷歌的家庭自动化产品)。Fuchsia 是像 Linux 一样的开源产品。同时，它也是世界科技巨头的产品。所以很多人会试用倒挂金钟。另一方面，Chromebooks 和 Pixels 等设备可能会比苹果设备更受欢迎，因为谷歌设备将成为自己的操作系统。

然而，世界上几乎所有的人都不是科技极客。因此，他们不会从高度技术性的角度去检查为什么 Fuchsia 更好。Fuchsia 的成功取决于它如何解决用户的问题。Linux 确实比 Windows 好，但是仍然有 87%的人使用 Windows。原因是 Windows 比 Linux 更好地解决了一个典型的人的问题。让我们等到 Fuchsia 制造一些噪音。