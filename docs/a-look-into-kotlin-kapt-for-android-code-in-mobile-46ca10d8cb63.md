# 面向 Android 的科特林·KAPT 研究——手机代码

> 原文：<https://levelup.gitconnected.com/a-look-into-kotlin-kapt-for-android-code-in-mobile-46ca10d8cb63>

## 科特林注释处理工具。这是什么？什么时候用？让我们来了解一下这个 Android 工具。

![](img/5833a91a815301f1df95124ffb9d9530.png)

Kotlin 被引入 Android 世界已经三年了。像许多其他编程语言一样，注释是一个必需的强大工具，但令人惊讶的是 Kotlin 与 Java 的注释处理工具不兼容。因此，让我们深入了解什么是 Android 的 KAPT。

![](img/2c8c2de110c998a9cdce15f49f5573af.png)

# 何时使用 KAPT？

当谈到 Android 中的注释处理时，我们通常想到的第一个问题是，你是否应该使用 KAPT，尤其是在 Java 背景下。答案是，KAPT 是 Kotlin 中进行注释处理的官方工具，因此答案总是。这是只要你需要标注处理，否则为什么要加？

# 包括 KAPT 在一个安卓项目中

要开始在 Android 项目中使用 KAPT，你应该像使用任何其他外部工具一样，将它添加到应用程序 gradle dependencies 中。使用下面一行来包含它:

```
apply plugin: 'kotlin-kapt'
```

就是这么简单，但是在将这个依赖项添加到项目中时会出现一个常见的错误。这个错误说“Kotlin 插件应该在‘kot Lin-kapt’之前启用”，解决这个问题的方法很简单，但是当你有大量的代码行时，这可能会令人困惑。这个错误的意思是，当将上面的行添加到 Gradle 文件中时，您需要像这样首先添加 Kotlin 插件:

```
apply plugin: 'kotlin-android' 
apply plugin: 'kotlin-kapt'
```

如果您切换第 1 行和第 2 行，您将得到上面的错误。

![](img/77d47c2298f8b3f8a5361f628913647e.png)

# KAPT 是用来做什么的？

这个问题的答案很简单，它用于 Kotlin 中的注释处理。但是如果您对此感到疑惑，这可能意味着您不熟悉注释处理。

我们先来学习一下什么是注释。批注是您在文件和方法顶部看到的带有“@”符号的单词。您可能已经见过的最常见的例子是@Override 注释。但是还有更多的，其中一些可以被 Kotlin 直接解释，那些被称为内置注释。其他人需要在代码中使用“注释处理器”，也称为 KAPT。

Kotlin 文档中注释的定义如下:

> 注释是将元数据附加到代码上的方法。
> 
> [T3【https://kotlinlang.org/docs/reference/annotations.html】T5](https://kotlinlang.org/docs/reference/annotations.html)

如果你想了解更多关于注释的知识，上面的文档会很有帮助。

![](img/9c5fb547827a815b2f0f9d3be48930e1.png)

# 好的，但是……注释处理器是做什么的？

注释处理器可以追溯到 Android 基于 Java 的时候。Java 中有很多关于注释的教程，因为在过去的某个时候，注释变得非常强大(事实也的确如此)并且是流行的工具。

无论是在 Java 还是 Kotlin 中，注释处理器都是在编译时运行代码并为应用程序生成代码的工具。这解释了为什么它们如此强大，如果你知道如何使用注释，它们可以通过自己生成代码来为你节省时间。

KAPT 的一个优点是，如果你有一个同时包含 Java 和 Kotlin 文件的项目，它可以兼顾两者。反之则不然，因为 APT (Java 注释处理工具)不能解释 Kotlin 注释。

![](img/530add3b67e4729182839c7b212f2dcd.png)

# 包扎

现在你应该对 Android 的 KAPT 有了更清晰的认识。随着您深入注释的世界，您将了解到您可以创建自己的注释，并使您的编码更加实用和安全。

如果你有任何疑问，不要犹豫留下评论，我会尽快回复。

下次见！

埃娃娜·马尔甘·普伊格