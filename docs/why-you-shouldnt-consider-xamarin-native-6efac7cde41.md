# 为什么您不应该在下一个项目中考虑 Xamarin Native

> 原文：<https://levelup.gitconnected.com/why-you-shouldnt-consider-xamarin-native-6efac7cde41>

## 在过去的几年里，我用 Xamarin 做了很多工作，在本文中，我将解释为什么我在未来的项目中再也不会使用它。

![](img/9bd8e6fac9b35820fdbcf775a96abee3.png)

阿尔瓦罗·雷耶斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

几年前，从我的第一个 Xamarin 项目开始，我就非常热衷于利用的全部功能。NET 生态系统，同时为 Android 和 iOS 开发漂亮的应用程序。拥有一个业务逻辑的代码库会加速我的开发过程，给我更多的时间来实现好的特性。

像往常一样，启动过程中会有一些困难，但没有什么特别的，在几周内，我们有了第一个演示，只有几个屏幕和一个有前途的项目。

几个月过去了，随着项目越来越大，第一个问题开始出现。我的团队的第一个问题是构建时间，这是本地项目的七到八倍。起初，我们认为这是一些糟糕的项目配置，但是即使我们以正确的方式配置了项目，改进也没有接近我们想要的。

即使这是一个令人恼火的问题，我们还是忽略了它，并适应于比平常做更少的构建来提高生产率。接下来发生的事情让我们及时考虑从 Xamarin 切换到另一个跨平台框架，但那是另一回事了。

## Xamarin。安卓几乎落后原生安卓半年

事实上，Android 框架的新功能在 Xamarin Android 上已经存在很长时间了，这使得我们开发定制组件或推迟一些不错的设计的实现。

就拿第二版[材质设计](https://material.io/)的发布来说吧。发布后，设计团队做了一个漂亮的设计，使用了新材料设计组件。尽管如此，在接下来的几个月里，Xamarin 上还没有提供它们，这使得我们决定推迟该设计的实现。

如果你使用的库也必须在你的应用程序中升级，团队将不得不多等一会儿。这里我给大家介绍一下 [MVVMCross](https://www.mvvmcross.com/) 的案例。

[](https://www.mvvmcross.com/) [## MvvmCross

### 使用 MvvmCross 构建完美的移动应用程序开始构建干净、像素完美的原生用户界面。分享行为和…

www.mvvmcross.com](https://www.mvvmcross.com/) 

MVVMCross 是一个很棒的、很有前途的库，可以用在当时 MVVM 架构的应用程序中。后来，当 Android X 可用时，我们无法迁移，直到库迁移，因为应用程序严重依赖它。即使在撰写本文的时候，它也不完全支持 Android X。

不要误解我的意思，MVVMCross 仍然是一个很棒的库，但是额外的延迟让我觉得我不会再考虑它了。

## Xamarin 原生包有时会附带拦截器错误

为了能够构建我们的项目，我们不得不降级我们的 Visual Studio 版本以使用更老的 Xamarin 版本，这种情况并不只有一次。这让我们密切关注更新，并等待每个更新，看看是否有任何错误被报告。

在更新之前等待并不是一件坏事，但是在团队花了大量时间调查之后，陷入一些最终通过 Xamarin 更新解决的阶段错误是一件坏事。

## Xamarin 缺少一些优秀的原生特性

在 Visual Studio 中开发 Xamarin 应用程序会让你时不时错过一些来自 [Android Studio](https://developer.android.com/studio) 或 [Xcode](https://developer.apple.com/xcode/) 的好功能。

这里我最想念的两个是来自 iOS 的[目标](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/Xcode_Overview/WorkingwithTargets.html)和来自 Android 的对应[风格](https://developer.android.com/studio/build/build-variants)。类似的行为确实可以通过一些配置解决方案来实现，但是结果不如本机那样强大和易于维护。

## 并非一切都是坏的。

尽管我在上面列举了这些，Xamarin Native 仍然是一个很好的框架，它拥有。Net 生态系统，它可能很适合其他人的需要。同时，这也是一个好的开始。希望转向手机的网络用户。

在这篇文章接近尾声时，我想澄清一下，我不是在谈论 [Xamarin Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) ，这是一个非常适合您的应用程序的伟大框架。

我希望你能从这篇文章中得到一些启示，帮助你将来做出好的决定。

保持安全和快乐的编码！