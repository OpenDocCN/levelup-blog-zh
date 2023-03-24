# 快速的最佳实践:为自定义颜色常量扩展 UIColor

> 原文：<https://levelup.gitconnected.com/swift-bite-sized-best-practices-extending-uicolor-for-custom-color-constants-280fe9597d8a>

![](img/9b3a841110c2906bbec3d5ff9232c7ad.png)

克里斯·劳顿在 Unsplash[拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

*Swift 小型最佳实践是一系列快速阅读，向您展示如何编写干净、可维护、可测试、可读的 Swift 代码。现在读一分钟，以后节省一小时。*

大多数应用程序使用自定义的特定色调的调色板，这在内置的 UIColor 常量中是找不到的。

为您的应用程序存储这些自定义颜色常量的最佳方式是在 UIColor 的扩展中。例如，假设您想要在应用程序中使用以下三种自定义颜色:

Swift 扩展允许您向现有类型添加计算属性。您可以通过在 UIColor 上添加三种颜色作为计算类型属性来利用此功能，如下所示:

现在，您可以使用熟悉的`UIColor.colorName`语法，在任何需要使用内置 UIColor 的地方使用您的颜色:

这种方法为您在 Swift 应用程序中存储和分发颜色常数提供了一种简单、干净和惯用的方式。

您认为这种最佳实践可能会更好吗？请在评论中告诉我。

*关注更多快速的最佳实践，祝您愉快！*