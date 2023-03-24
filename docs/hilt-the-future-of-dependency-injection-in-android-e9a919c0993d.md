# hilt——Android 中依赖注入的未来

> 原文：<https://levelup.gitconnected.com/hilt-the-future-of-dependency-injection-in-android-e9a919c0993d>

在 Android 上使用 DI 的标准方式

![](img/b6b762d4b2fa03119e32b76fd2388909.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 依赖注入

依赖注入(DI)是任何编程语言的关键原则之一。DI 是一种用于向依赖类提供依赖关系或对象的技术。也就是说，一个类可能需要其他类对象来做一些工作，所以我们提供这些对象的方式就是 DI。DI 主要以三种不同的方式实现

*   一个类可以在其内部创建其他类的对象(依赖关系)。
*   使用其他来源的对象。就像这样，我们在应用程序中使用上下文。
*   将对象作为参数提供给所需的类。

# ANDROID 中 DI 的历史

来到 Android 中的 DI，我们大多数人都使用 Dagger 作为 DI，因为它是 Google 维护的官方 DI 框架。但是对于从最初版本开始使用 Dagger 的人来说，他们会知道用 Dagger 管理事情有多难。从我的亲身经历来看，我会说从零开始学习匕首到高级水平需要时间和努力。许多开发人员对 Dagger 的复杂性给出了反馈。不仅如此，任何 DI 库都会生成大量样板代码，并且需要手工管理所有的依赖关系。所以为了帮助开发者克服这些问题，Andriod 开发了 Hilt。

# 刀柄简介

刀柄是匕首的简化版。Hilt 提供了一种在 Android 中实现 DI 的标准方法。刀柄是 Jetpack 推荐给 Android 中 DI 的库。刀柄是匕首上的一个抽象层，有更多的好处。它减少了在应用程序中手工 DI 的工作量。Hilt 的目标是:

*   简化 Android 应用程序的 Dagger 相关基础设施。
*   创建一组标准的组件和范围，以简化设置、可读性/理解以及应用程序之间的代码共享。
*   提供一种简单的方法来为各种构建类型提供不同的绑定(例如，测试、调试或发布)。

# 为什么是刀柄

Hilt 的好处是:

*   简化样板文件
*   分离的构建依赖项
*   简化配置
*   改进的测试
*   标准化组件

想知道更多关于好处检查[为什么匕首开发](https://dagger.dev/hilt/benefits)上的刀柄

# 刀柄设置

最初，将`hilt-android-gradle-plugin`插件添加到项目的根级`build.gradle`文件中:

然后，应用 Gradle 插件并在`app/build.gradle`文件中添加依赖关系:

Hilt 使用 Java 8 特性，所以确保你已经在应用级 build.gradle 文件中启用了它:

我们完成了设置。

# 刀柄实现

刀柄是类似 Dagger 的基于注释的框架。让我们一步一步地检查实现，以便更好地理解。

## 第一步:@注射

让我们创建一些类，我们可以在任何需要的地方注入它

为了告诉 Hilt 提供的是哪种类型的实例，在你想要注入的类的构造函数中添加 **@Inject** 注释。Hilt 拥有的关于如何提供不同类型实例的信息被称为**绑定**。

## 第二步:`**@HiltAndroidApp**`

现在，让我们设置应用程序类。`**@HiltAndroidApp**`需要将注释应用到应用程序类，以便生成组件。不需要编写额外的代码来创建组件。

## 第三步:@ AndroidEntryPoint

只需在 Android 组件类上添加**@ androdidentrypoint**就可以完成对这些组件的注入。让我们看看如何将这个 SampleClass 注入到活动中

Hilt 目前支持以下 Android 组件:

*   `Activity`
*   `Fragment`
*   `View`
*   `Service`
*   `BroadcastReceiver`

## 第四步:

@Module 注释用在我们提供依赖关系的类上面。

**@提供了**注释，用在我们创建对象的模块和方法之上，将它们作为依赖项传递。

**@InstallIn** 注释用于指定与@ **模块**一起指定的模块范围。

# 喷气背包的手柄支架

Hilt 目前支持跟随者 Jetpack 组件:

*   `ViewModel`
*   `WorkManage`

但是要使用 Jetpack 的 Hilt，我们需要添加一些额外的依赖项。

## 用 Hilt 注入视图模型对象

将以下依赖项添加到 app/build.gradle

**@ViewModelInject** 是在`ViewModel`对象的构造函数中使用的注释。

接下来，要在活动或片段中获得 ViewModel 实例，我们需要使用`ViewModelProvider`或`by viewModels()` KTX 扩展。

为了获得活动级 ViewModel 实例，我们需要使用`**activityViewModels**()`委托函数来代替`**viewModels**()`。

探索更多关于 Jetpack 手柄支持的信息。

我觉得实现剑柄比匕首容易。所以我想你也有同感，所以开始探索剑柄吧。在你的下一个项目中尝试一下 Hilt，你将永远不会再回到 Dagge，然而，它是 Dagge 上的抽象层，就像 Sqlite 上的房间一样。但是由于 Hilt 还处于 alpha 阶段，所以在产品应用中试用时要小心。

# 参考

[探索更多关于剑柄的信息](https://developer.android.com/training/dependency-injection/hilt-android#setup)

[dagger.dev docs](https://dagger.dev/hilt/)

请让我知道你的建议和意见。

你可以在 [**中**](https://medium.com/@pavan.careers5208) 和[**LinkedIn**](https://www.linkedin.com/in/satya-pavan-kumar-kantamani-61770a9b/)……

感谢阅读…