# 用 Expo 构建本地应用的乐趣

> 原文：<https://levelup.gitconnected.com/react-native-with-expo-is-a-joyride-671d706b7a19>

## 为需要 XCode 和 Android Studio 在 React 原生应用上工作而烦恼？希望只需一个按钮就能与其他用户分享您的手机应用程序？让我们看一看世博会。

![](img/4d1221fbf3ab20c6999cb267b50282ab.png)

*TL；DR* [*Expo*](http://expo.io) *为你的移动应用开发提供扩展的 SDK 和托管服务，无需再次接触 XCode 或 Android Studio(大多数情况下)。*

# 反应原生是开始

React Native 仍然是一个非常年轻的项目，但它肯定会越来越受欢迎，并且已经成为创建移动应用程序(以及其他应用程序)的首选统一方式。当你可以只学习 JavaScript 并达到相同的目标时，为什么你需要两种不同的语言(Android 和 iOS)？好了，甜言蜜语说够了，我们走吧。

![](img/8ad0c4c8d977b2cc3632e23a6ef5754e.png)

该框架涵盖了一些重要的 API，用于你在移动应用中可能需要的东西，比如用于制作动画的[Animated](https://facebook.github.io/react-native/docs/animated.html)(*非常强大的*)、用于操作系统剪贴板内容的 [Clipboard](https://facebook.github.io/react-native/docs/clipboard.html) ，或者用于以表演方式呈现列表的 [FlatList](https://facebook.github.io/react-native/docs/flatlist.html) 。如果没有 React Native，您将不得不用一种本地语言自己编写大部分内容。

不过 React Native 有一个相当大的痛点:如果你打算支持这两个平台，你需要同时安装 XCode ( *仅适用于 Mac 用户*)和 Android Studio。每当你想在模拟器中看到你的应用程序的结果时，你仍然需要使用这些(可怕的)开发工具。

# 世博会就像一种止痛药，可以缓解你天生的疼痛

![](img/5d1eba37084d52b1813344409f9bd0cc.png)

Expo 是 React Native 的补充工具包。它让你可以访问额外的 API，包装本地代码来完成常见任务，这样你就可以继续使用你心爱的 JavaScript，包括[读取手机联系人](https://docs.expo.io/versions/latest/sdk/contacts.html)、[访问手机陀螺仪](https://docs.expo.io/versions/latest/sdk/gyroscope.html)，或者[用脸书](https://docs.expo.io/versions/latest/sdk/facebook.html)为用户登录。所有这些功能通常被称为 Expo SDK，并且它的[仍在增长](https://expo.canny.io/feature-requests)。

> 有了 Expo，你就不会有任何可怕的 ios/android 文件夹，就像你可能在 pure React Native 上看到的那样。这是完全正常的，因为你不需要这些。更多详情，请点击查看指南。

除了 SDK，Expo 平台还有另一个非常重要的部分:构建**和**分发你的应用。只需按下一个按钮(在 expo.io 上注册一个帐户)，几分钟之内，你就可以向你的朋友或同事发送公开网址，实时演示你的应用程序！一个小缺点是，每个人都需要在他们的设备上安装 Expo 应用程序( [Android](https://play.google.com/store/apps/details?id=host.exp.exponent) 和 [iOS](https://itunes.com/apps/exponent) )。

> 您可以直接从您的电脑上共享您的应用程序开发版本，其他人可以查看您所做的更改，而无需任何发布步骤！

当你用 Expo 发布一个应用时，JavaScript 代码被捆绑并发送到 Expo 服务器。公共页面([像这样的](https://expo.io/@community/native-component-list))会为你创建。任何人都可以轻松安装并试用你的应用。没错。除非你想与公众分享你的应用，否则你不需要为 Google Play 或 Apple Store 及其严格的政策而烦恼。这给了你一个很好的机会来演练想法，而不会产生任何假设或对应用程序产生不好的评论，供未来的消费者阅读。

> 这个过程在技术上与[代码推送](https://microsoft.github.io/code-push/)或[展示](https://rollout.io/)非常相似，在官方商店看来是完全合法的。你不需要在那里更新应用程序，除非你改变应用程序的范围或更新 SDK 版本。

# 世博会可能是一剂良方

你可能认为本地开发现在很好很容易，但是你可能不太喜欢你的用户不得不安装一些他们从未听说过的*怪异* Expo 应用程序，只是为了运行你的应用程序。有时候很难向习惯只从官方商店安装 app 的人解释这背后的原因。他们甚至担心这个 Expo 应用程序会损害他们的设备。那为什么还要为世博会费心呢？

这就是独立应用的用武之地。Expo 提供构建服务器，能够为您远程创建 APK/IPA 文件，然后您可以将其分发给您的用户或上传到官方商店。你的用户不会再需要 Expo app 了(虽然还能用)。您的应用程序将照常安装，带有自己的图标或定制的加载程序屏幕(查看[您的所有选项](https://docs.expo.io/versions/latest/guides/configuration.html))。你仍然不需要 Android Studio 或 XCode 来做这些。那不是很美吗？

*注意，如果你正在尝试构建 iOS 独立 app，你将不得不支付* [*苹果开发者计划*](https://developer.apple.com/programs/) *。这是必需的，以便 Expo 可以使用 Apple 提供的证书签署您的应用程序。没有它，甚至不可能创建一个 IPA 包。Android 应用程序可以获得自签名证书。*

这款独立应用的工作方式与 Expo 应用几乎相同，*除了*没有 Expo 用户界面，用户必须通过它来启动应用。它下载与之相关的已发布的 JS 包并运行它。就是这样。

![](img/162707b8662d2d6668c33ad1737121ce.png)

[克里斯托佛罗拉](https://unsplash.com/photos/PC_lbSSxCZE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

# 稍后更新应用程序

因此，有一个独立的应用程序和一个 JS 包。谁知道什么时候更新什么？又是怎么做到的？

只要你只使用 JavaScript 代码，你就不需要担心你的用户已经安装了一个独立的应用程序。只需使用 XDE(或 [exp 实用程序](https://docs.expo.io/versions/latest/guides/exp-cli.html))来发布包。下次用户打开你的应用时，它将被更新。

每当你需要从 *app.json* 文件、**中改变设置，包括**更新 SDK 的版本，那么你需要重新构建一个独立的 app 并再次分发。

不过，有一个小问题可能会让你大吃一惊。尤其是在第一次开发运行期间。当用户打开应用程序时，JS 包会在后台下载。在应用程序的使用过程中，不会有即时更新。用户将需要实际关闭应用程序，并在重新打开时，更新将被应用。

*如果你需要确保用户始终使用最新版本的应用程序，有一个* [*要点*](https://gist.github.com/FredyC/b79b9d09c72e60bd1e89238e25e23387#file-app-js-L10-L13) *可以帮助你做到这一点。根据您的需要，您可能希望对其进行扩展:*

# 结论

Expo 仍然是一个年轻的项目，需要解决一些棘手的问题，但当它完成时，它可能会成为移动开发的主要工具。

本文仅涵盖理想情况，即您能够仅使用 React Native + Expo API 来实现应用程序的功能。一旦你想从中得到更多，你将不得不[分离到 ExpoKit](https://docs.expo.io/versions/latest/guides/detach.html#1-install-exp) 或者完全离开世博会的安全水域。我已经在我的后续文章[编译 React Native with Expo kit easy](https://medium.com/@DanielKrejci/compile-react-native-with-expokit-easily-46e9e94abb97)中详细介绍了这一点。