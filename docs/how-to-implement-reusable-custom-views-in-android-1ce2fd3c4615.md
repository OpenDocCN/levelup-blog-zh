# 如何在 Android 中实现可重用的自定义视图

> 原文：<https://levelup.gitconnected.com/how-to-implement-reusable-custom-views-in-android-1ce2fd3c4615>

![](img/dd0db0b5e1b7b4903fe23f9aae296c5c.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

用户界面不仅是用户与产品互动的方式，也是展示一个公司或个体企业家品牌的工具。

在 Android 中，我们有工具来实现这一点，比如自定义视图。除了使用 Android 项目中可用的默认视图，您还可以自定义一切，为您的 Android 应用程序提供独特的外观和感觉。

我们希望这些自定义视图可以重用。例如，您可以创建一个库并在几个应用程序中使用它，缺点是您需要小心，因为对库的任何更改都会以意想不到的方式影响每个应用程序。

我们将通过定制一个开关滑块按钮来看看如何做到这一点。如果你对下面的帖子感兴趣，你可以查看关于如何调整该按钮的一些设置的介绍。

[](/how-to-customize-the-switch-slider-in-android-860bb42efe86) [## 如何自定义 Android 中的开关滑块

### 关于如何定制这种按钮以及如何实现它的想法

levelup.gitconnected.com](/how-to-customize-the-switch-slider-in-android-860bb42efe86) 

## 如何构建自定义视图

自定义视图基本上由三部分组成。

1.  视图膨胀的 Kotlin 或 Java 类
2.  作为视图源的 XML 文件
3.  用于定义自定义视图属性的属性枚举

在这个例子中，我们将为一个`[ConstraintLayout](https://developer.android.com/develop/ui/views/layout/constraint-layout)`充气，它作为开关和一个`TextView`的容器。这意味着这个类，我们新的自定义视图，需要从`ConstraintLayout`继承。

这就是属性枚举发挥作用的地方。我们将在我们类的构造函数中使用它。我们希望这样做，因为这也将允许我们从 XML 文件配置自定义视图。例如，我们的定制开关将具有这些属性，它们被定义在`res/values`文件夹中一个名为`attrs.xml`的文件中。

现在在自定义类中，我们需要覆盖构造函数，在那些带有`AttributeSet`变量的类中，我们将像这样读取前面定义的属性。

请注意对`recycle()`的调用，它创建了这些属性的缓存，以便在另一个`CustomView`对象膨胀时提高性能。

现在，在使用视图的 XML 文件中，我们可以像使用任何其他视图一样使用它。

有了它，我们可以用 Kotlin、Java 和通过 XMLs 以编程方式使用视图。

## 将工厂模式应用于我们的自定义视图

为了使我们的定制视图的这些变化可重用，我们将应用[工厂模式](https://www.tutorialspoint.com/design_pattern/factory_pattern.htm)。我们将以下面的课程结束。

**CustomSwitchManager:** 这个接口定义了每个`CustomView`实现将需要的方法。我们的`CustomSwitchFactory`将负责提供这位经理。

**CustomSwitchType:** 这是一个`sealed class`，用于对每个`CustomSwitch`实现的类型进行建模。如果我们添加另一种类型，如果我们不在`when`中更新一个新的分支，编译器将会出错，所以我们被这种类型的运行时错误所覆盖。

**CustomSwitchFactory:** 使用类型返回我们要求的`CustomView`的任何精确实现。

**CustomSwitch:** 这是我们之前说过的继承自`ConstraintLayout`的类。如果你仔细看了代码，你一定见过一个叫`inflateView`的函数。这是`CustomSwitchManager`将要调用每个创建的`CustomSwitch`的实现的地方，这样就应用了视图绑定并完成了视图的配置。

现在我们唯一剩下的类是我们的`CustomSwitchManager`的不同实现。这是其中一个例子。

仅此而已！现在，我们有了可以在 XML 文件中配置的自定义视图，以及可用的 Android Studio 预览。

如果你想看的话，这是[回购](https://github.com/molidev8/custom-view-sample)。

如果你想阅读更多这样的内容，并支持我，不要忘记检查我的个人资料，或给媒体一个机会，成为会员，以获得我和其他作家的无限故事。这只是 5 美元一个月，如果你使用这个链接，我会得到一小笔佣金。

[](https://medium.com/@molidev8/membership) [## 通过我的推荐链接加入 Medium—Miguel

### 阅读米格尔的每一个故事(以及媒体上成千上万的其他作家)。你的会员费直接支持米盖尔…

medium.com](https://medium.com/@molidev8/membership)