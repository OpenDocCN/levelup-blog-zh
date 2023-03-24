# 使用自定义参数构建您的 Android 导航

> 原文：<https://levelup.gitconnected.com/compose-your-android-navigation-with-custom-arguments-20d4467b5dfd>

![](img/debd56da6eae3bcb1a22390e93b2ff8e.png)

即使在使用多个撰写应用程序后，使用 Jetpack Compose 导航也很容易变得混乱。尤其是，因为还没有一种方法可以做到这一点。

随着每个项目的建立，我学到了如何构建导航的一个新方面。在我当前的项目中，我需要在导航时提交**自定义对象。**

## 带有基本参数的导航

在 Android 官方文档[中很好地解释了带有基本参数的导航，如字符串、整数、布尔值等。](https://developer.android.com/jetpack/compose/navigation#nav-with-args)

很自然地，我很想将我的自定义参数的所有相关字段都转换成这些基本类型，并向目的地添加一个扩展的参数列表。

这种方法实现起来很快。不幸的是，它也迅速升级。

因此，请继续阅读，学习如何干净快速地使用自定义参数。

## 带有自定义参数的导航

对于使用自定义对象作为参数的导航，首先必须确保您的自定义参数是可打包的。让我们以下面的*产品*类为例。

然后你需要为你的类定义一个`NavType`。让我们为我们的*产品*类这样做。

实施`NavType`时，您有几种选择:

*   注意，我已经实现了作为伴随对象的`NavType`。它帮助我保持我的`NavTypes`结构化，因为它们通过伴随对象被分配给各自的数据类。你也可以把它实现为独立的类。
*   注意，我使用`Gson`将对象转换成 JSON 字符串。但是你可以使用你喜欢的转换器。
*   如果您的参数是可选的，则将`isNullableAllowed`设置为*真*。

此时，我们已经告诉 Compose 我们的新导航类型应该是什么样子。接下来就是让`NavHost`知道这件事了。

这一步类似于添加基本类型的参数。更多详细信息，请查看 Android 的文档。

最后但同样重要的是，在导航时，您必须解析 JSON 的自定义参数。意思是，你要用下面的方式调用`navigate`:

![](img/b2a917eef1faaa188b837987cd65947f.png)

## 关于导航逻辑的思考

所描述的代码片段被简化，以展示如何使用自定义参数进行导航。

当在更大的应用程序中实现 Jetpack Compose 导航时，我通过使用[嵌套导航图](https://developer.android.com/jetpack/compose/navigation#nested-nav)和远离 NavHost 的抽象路线、目的地和参数，以更高级的方式构建我的导航。此外，我添加了另一个层来防止在 UI 或 ViewModel 层中使用`navController`。

您想了解更多关于嵌套导航逻辑的知识吗？乔·伯奇的这篇好文章[是一个很好的起点。](https://hitherejoe.medium.com/nested-navigation-graphs-in-jetpack-compose-dc0ada1d4726)

*你喜欢这个帖子吗？您想了解更多关于 Jetpack 撰写导航的信息吗？请在评论中或通过回应让我知道👏🏻这个故事。*

*快乐编码！🧑‍💻*