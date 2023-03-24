# 使用密封类轻松管理图像类型

> 原文：<https://levelup.gitconnected.com/easily-manage-image-type-with-sealed-classes-4e361c6f4db9>

![](img/32be556ada322795372834998802eff5.png)

由[帕布罗·阿罗约](https://unsplash.com/@pablogamedev?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

随着 Kotlin 中密封类的引入，它开辟了一种轻松处理受限层次结构的方法。

密封类最常见的用例是处理 API 响应的`Result<T>`。

…..但是密封类的应用比你想象的要多得多。我将在这里讨论其中之一，即在 android 中处理图像类型。

图像的来源可以是**远程**，即 URL，也可以是**本地存储**。因为资源只能来自这两种类型，所以它是密封类的完美用例。

您可以在项目中的任何地方使用该图像，并轻松引用这两者中的任何一个。

示例:

*此外，您可以像* ***Glide*** 一样使用图像加载库轻松加载图像

可以配合 **Jetpack Compose** 使用。

你可以创建一个使用 UiImage 的可组合组件，应用内代码最终是远程的，但是对于预览，你可以提供一个可绘制的 res，这样我就可以看到完整的行为

例子

# 结论

当你有一个严格的类层次结构时，Kotlin 密封类真的很强大。对于图像，您甚至可以将这种用法扩展到文本，因为它可以作为字符串或字符串资源的引用直接传递。

特别感谢 [Adam McNeilly](https://medium.com/u/8e903f5daef5?source=post_page-----4e361c6f4db9--------------------------------) 的反馈和建议。

非常感谢您阅读这篇文章。我希望你喜欢它。有什么反馈吗？在下面评论或者在推特上联系我。