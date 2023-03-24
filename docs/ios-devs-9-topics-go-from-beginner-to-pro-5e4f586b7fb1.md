# iOS 开发:从初学者到专业人员的 9 个主题

> 原文：<https://levelup.gitconnected.com/ios-devs-9-topics-go-from-beginner-to-pro-5e4f586b7fb1>

## 一旦掌握了 iOS 开发的基础知识，就应该关注什么。

![](img/1079b48b75c5e39e383aa9bc3770407a.png)

T·K·哈蒙兹在 [Unsplash](https://unsplash.com/s/photos/hero?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

于是你完成了几个教程，创建了你的第一个 app，牛逼！但你现在关注的是什么？

无论你是在改进一个现有的应用程序还是开始一个新的项目，这里有一些关键因素，你一定要考虑，以提高你的技能和知识。本文涉及的几乎所有要点对 UIKit 和 SwiftUI 都有效。

# 深入软件设计模式

触及设计模式的主题可能是危险的，因为似乎没有一个开发人员在最佳方法上达成一致。然而，几乎所有的模式都有一个共同点:正确地应用它，你会得到一个结构更好的应用程序。您的代码将更容易维护、重构和测试。

您可以从熟悉最常见的模式开始:MVC、MVP、MVVM 和 VIPER。

[](https://medium.com/@chetan15aga/ios-design-patterns-f478abd78132) [## iOS:设计模式

### 设计模式是软件设计中常见问题的可重用解决方案。这些模板旨在帮助您…

medium.com](https://medium.com/@chetan15aga/ios-design-patterns-f478abd78132) 

# 了解如何本地化您的应用程序

乍一看，这似乎并不重要，但是应用程序的本地化不仅仅是支持多种语言和地区。这也是强迫你自己更好地构建你的代码，防止字符串分散在你的代码库中。你们中那些在想:“那我只是把一套魔术弦换成了另一套”；查看关于代码生成的第 4 点。

只要你坚持不懈，不要偷工减料，给你的应用添加本地化功能并不一定很难。本地化你的应用不仅仅是翻译，格式器的正确使用也绝对不应该被忽视。

[](https://medium.com/lean-localization/ios-localization-tutorial-938231f9f881) [## iOS 本地化教程

### 本地化是使您的应用程序支持其他语言的过程。在许多情况下，你用英语制作你的应用程序…

medium.com](https://medium.com/lean-localization/ios-localization-tutorial-938231f9f881) 

# 尽可能使用正确的格式化程序

除了语言，本地化的另一个关键方面是正确的文本格式。许多国家在货币符号的位置、如何使用引号、数字中的点和逗号等方面都有自己的规则。

为了帮助我们，系统提供了一组很好的格式化程序。如果已经做了，为什么还要做所有的艰苦工作，对吗？知道如何使用这些在开始时看起来像是额外的工作，但是从长远来看会有回报的。从检查这些关键格式化程序开始:

*   [日期](https://developer.apple.com/documentation/foundation/dateformatter)
*   [数字](https://developer.apple.com/documentation/foundation/numberformatter)
*   [货币](https://developer.apple.com/documentation/foundation/numberformatter/style/currency)(实际上，这是数字格式化程序的一部分，但对于货币来说，如果使用得当，理解有多简单也无妨)。

# 尽你所能创造一切

写代码可能很有趣，重复同样的任务通常不那么有趣。生成代码可以帮助您最小化这种情况，同时还可以防止打字错误和复制粘贴错误。

一个好的起点是 [SwiftGen](https://github.com/SwiftGen/SwiftGen/tree/develop) :一个生成 Swift 代码来引用你的资源的工具，比如 XCAssets 文件、本地化等等。

另一个有趣的领域是生成测试存根和模拟。为了帮助隔离您正在测试的组件，模拟可以帮助您实现这一点。Cuckoo 是一个为你的类生成 mock/stub 来帮助你的框架。在您的测试套件中很容易设置。

# 组织用户界面组件

很快你的应用程序的代码库开始增长，设计也在发展，所以考虑一下你设置和重用设计风格的方式是有好处的。

根据您的喜好，有几种方法可以处理这个问题。一种方法是利用[IBDesignable/IBInspectable](https://nshipster.com/ibinspectable-ibdesignable/)，另一种方法是使用内置的 [UIAppearance API](https://developer.apple.com/documentation/uikit/uiappearance) 来设置组件的默认外观。

就我个人而言，我更喜欢另一种方法，它允许你在保持灵活性的同时从一个预定义的集合中组合风格。

[](/how-to-style-your-ios-app-less-boilerplate-more-convention-4c7574b73de2) [## 如何设计你的 iOS 应用:更少的样板，更多的惯例

### 设置你的风格，以防止重复你自己，并使重构更容易。

levelup.gitconnected.com](/how-to-style-your-ios-app-less-boilerplate-more-convention-4c7574b73de2) 

# 使用浪子自动化构建步骤

没有人喜欢重复自己，特别是当涉及到无聊的任务时，比如运行你的测试套件，创建一个新的测试版本或者等待它被上传到 TestFlight。

除了节省您的宝贵时间之外，自动化这些任务还有几个好处。更多的构建意味着更快的测试反馈，来自单元测试和测试团队。有几种入门方式，最常用的有 [Xcode Server](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/xcode_guide-continuous_integration/) 、 [BuddyBuild](https://www.buddybuild.com) 、 [BitRise](https://www.bitrise.io) 和[浪子](https://fastlane.tools/)。

就我个人而言，我建议先看看浪子，它高度可定制，并且以非常透明的方式直接与原生 Xcode 工具链一起工作。此外:它是免费的，文档也非常广泛。

为了帮助你做好准备，以下是我一路走来学到的一些经验:

[](https://medium.com/better-programming/6-lessons-i-learned-from-using-fastlane-cb35f53021b3) [## 我从使用浪子中学到的 6 个教训

### 这是一个神奇的工具，但有些事情可以解释得更好一点

medium.com](https://medium.com/better-programming/6-lessons-i-learned-from-using-fastlane-cb35f53021b3) 

# 测试，测试，测试

尽管开发人员普遍认同测试的重要性，但在实践中这样做可能是一项困难的任务。可能很难找到一个好的起点，甚至更难向现有代码添加测试，尤其是如果大规模视图控制器看起来更像一个纠结的圣诞彩灯球。

以下是一些帮助你上路的基本指南:

*   尽量坚持你选择的设计模式。
*   如果你的班级变得太大，如果可能的话，把责任转移出去。
*   如果你的视图/视图控制器变得太大，试着把它们分开。
*   模拟出任何不应该成为你测试一部分的东西。

如果你仍然不确定，一个更好地了解如何处理这个问题的好方法是将 TDD(测试驱动开发)应用到你的下一个任务中。基本上，你将首先编写测试，然后编写测试成功所需的代码，扩展你的测试，使代码不会通过，等等。这里有一篇关于这个话题的好文章。

如果你正在寻找一种更好的方法来指定你的测试，并使它们更具可读性，请查看 [Quick/Nimble](https://www.swiftbysundell.com/articles/mocking-in-swift/) 。这组库允许您定义测试规范，并使用匹配器来验证结果。

# 发现轮子没问题！

使用库是有意义的；为什么要自己建造它呢？

然而，使用库有一个很大的缺点:如果你不知道你所使用的工具是如何工作的，你怎么能期望正确地使用它们，更不用说在它们坏了的时候修理它们了？也；简单地堆积库将会使你的库变得更大更笨重。

一个很好的例子是 AlamoFire，一个非常流行的网络库。这是一个很棒的软件，但是很多项目真的不需要它。许多开发人员可以利用从实现一个简单的网络库中学到的经验。当然，这并不一定适用于大型项目(有更多的开发人员)，在大型项目中，选择一个广泛使用的库而不是一个定制的解决方案可能是一个明智的选择。

# 及时了解时事通讯和播客

iOS 社区非常棒，那里有很多很棒的资源可以阅读。我建议尝试所有的方法，坚持你最喜欢的方法。

我最喜欢的一些资源包括:

## 时事通讯

*   安托万·范·德·李
*   [iOS 开发周刊](https://iosdevweekly.com)，作者戴夫·维尔
*   [Swift 每周简报](https://swiftweekly.github.io)，作者 Bas Broek
*   马里乌斯·康斯坦丁内斯库
*   由约翰·桑德尔创作的斯威夫特

## 播客

*   亚历克斯·布什和安德鲁·罗恩
*   大卫·史密斯和马可·阿门特
*   约翰·桑德尔的《斯威夫特》
*   [JP·西马德和杰西·斯奎尔斯的斯威夫特拆封](https://spec.fm/podcasts/swift-unwrapped)

感谢阅读！知道其他不应该错过的好题材吗？让我知道！