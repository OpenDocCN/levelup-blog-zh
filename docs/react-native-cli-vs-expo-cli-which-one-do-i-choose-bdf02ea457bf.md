# React Native CLI 与 Expo CLI —我应该选择哪一个？

> 原文：<https://levelup.gitconnected.com/react-native-cli-vs-expo-cli-which-one-do-i-choose-bdf02ea457bf>

## 我没有意见了，我做了自己的研究！

![](img/a9bda7aeb23db8cddd399a4556197502.png)

爱丽丝·山村在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 react-native 社区中，许多开发人员在 React Native CLI 和 Expo CLI 之间的选择上意见不一。我们通常倾向于根据我们的开发伙伴的意见来做决定，而不是验证他们。

因此，我研究、分析并收集了一些有价值的信息，并在今天与大家分享，这些信息是关于两个框架之间的实际比较的！

# 反应本机 CLI

[React Native CLI](https://reactnative.dev/docs/environment-setup) 是一个内置特性，可以帮助您在本地控制项目的管理。您可以创建和运行您的应用程序。您可以通过简单地使用这个命令来创建一个项目。

```
npx react-native init <ProjectName>
```

要运行项目，可以运行以下命令。

```
// run on all platforms
npx react-native start// run on android
npx react-native run-android// run on iod
npx react-native run-ios
```

## 额外津贴

我们都知道 Android 应用是用 Java 或者 Kotlin 开发的，IOS 应用是用 Objective-C 开发的，有了 React Native CLI，你可以 ***添加用 Java/Objective-C*** 编写的原生模块，我相信这是最强的特性。

一旦创建了项目，您就可以访问并 ***控制构建*** 并根据您的需求进行相应的调整。因此，您可以在 Android 和 IOS 平台上很好地控制您的应用程序。

## 限制

最明显的缺点打击了 Windows 用户。要使用 React Native CLI 构建跨平台的移动应用程序，您需要 Andriod 和 IOS 模拟器。这意味着你需要有 ***Andriod Studio 和 XCode*** 来运行你的项目，这些只有在 ***Mac*** 上才有。

但是，并不是不能在 Windows 上开发。使用 Windows，您可以开发 react 应用程序，并在 Andriod Studio 仿真器上运行它们。但是，对于 IOS 来说，还是有变通办法的。

用 React Native CLI 启动项目可能会 ***耗时*** 。此外，这是一个非常敏感的过程，你需要全神贯注。

这主要是因为 ***复杂的设备配置。您需要设置环境变量，并确保您的应用程序运行所需的正确 SDK。有关[环境设置](https://reactnative.dev/docs/environment-setup)的更多详情，请参考此处。***

既然您已经在模拟器上运行了应用程序，调试可能会非常繁忙。尤其是当你的应用有需要物理电话的功能时。在这种情况下，你需要进行 ***USB 调试，*** 这需要你通过 USB 线 ***将手机连接到 pc。***

React Native 这个词本身就说明了这一点，它构建了使用本机元素运行的应用程序。但是在使用不同字体时，需要 ***手动将*** 导入到 XCode 中。有些字体不在这两种字体中。

共享应用程序可能会很麻烦。这是因为你需要建立一个 ***完整的 APK 或者 IPA 文件。*** 所以，每次都要给一个 APK 或者 IPA 文件。

# 世博 CLI

Expo CLI 构建于 react native 之上，这是在 zoom 中设置 React Native 项目的最快方式！您只需创建项目并开始编码。您可以通过 npm 在全球安装 Expo CLI:

```
// install expo-cli globally
npm install -g expo-cli
```

使用 Expo 创建和运行 React 本地应用程序非常简单:

```
// create a project
expo init <Project Name>cd <Project Name>
npm start # you can also use: expo start
```

用 Expo 写代码的时候，你写的还是 React 原生代码。世博会有两个部分。

*   Expo CLI —开发人员 CLI，用于创建、运行、发布等您的应用程序。
*   Expo 客户端应用——一款可在 [Andriod](https://play.google.com/store/apps/details?id=host.exp.exponent&hl=en&gl=US) 和 [IOS](https://apps.apple.com/us/app/expo-go/id982107779) 上使用的移动应用，可在真实设备上直观运行应用。

福利:

使用 Expo CLI，您可以 ***轻松设置*** 您的 React 项目。您的应用将在 ***分钟*** 内启动并准备运行！

使用 Expo 运行 React 本地应用程序不需要仿真器或 Mac。你可以简单地为 [Andriod](https://play.google.com/store/apps/details?id=host.exp.exponent&hl=en&gl=US) 和 [IOS](https://apps.apple.com/us/app/expo-go/id982107779) 下载 ***Expo 客户端 app*** ，然后在手机上运行项目。

一旦你使用`expo start`或`npm start`命令运行你的应用程序，基于浏览器的 DevTools 用户界面会打开一个二维码。因此，你可以分享这个二维码，你也可以 ***在整个开发过程中实时分享你的应用*** 。因此，我们可以避免使用 APK 或国际音标文件。

但是，这并不意味着你不能建立 APK 或国际音标文件。Expo 提供 ***建立 APK 和 IPA 文件*** 用于分发到商店(苹果商店和 Play 商店)。

与必须在运行 Andriod 和 IOS 应用程序之前构建它们不同，使用 Expo，在运行应用程序之前不需要构建*。*

*Expo 为一个标准项目集成了自己的 ***基本库*** :推送通知、资产管理器等。有关库的更多信息，您可以参考官方[文档](https://docs.expo.dev/workflow/using-libraries/)。*

*假设你改变了主意，你可以 ***将其弹出到 ExpoKit*** 。这将有助于您继续体验世博会的一些特色。但是，并非所有功能都可以使用。*

***限制:***

*Expo 有一些限制，因为你 ***不能添加本地模块*** 。与 React React Native CLI 不同，您 ***不能使用使用 Objective-C/Java 的本机代码库*** 。*

*我不认为这很重要，但是一个基本的 Hello World 应用程序大约有 25MB，因为它是用集成库构建的。这可能很快，但会稍微占用空间。*

*正如我上面提到的，当你使用 expoKit 弹出你的应用程序时，你就 ***限制了你的应用程序使用 Expo 的所有特性*** 。最主要的是 ***无法通过二维码*** 分享你的应用。*

*集成面部检测器或 ARKit 或支付需要您将其弹出到 ExpoKit。因此，整合一些功能会让你失去另一个。总有一个 ***特性的取舍*** 。*

# *反动派的裁决*

*[React Native](https://reactnative.dev/docs/environment-setup) 如果你已经熟悉移动应用开发，建议使用 React Native CLI。但是，如果您是移动应用程序开发的新手，并且希望快速建立项目，那么建议使用 Expo CLI。*

# *结论*

*给出两个框架的好处和限制的详细比较，我建议筛选需求，选择最适合您的应用程序的一个。*

*我将从理解应用程序的未来和可伸缩性开始。然而，除了可伸缩性之外，请随意探索其他方面。*

*希望这个故事已经为你拨开了乌云！分享知识是获得知识的唯一途径。对于那些可能从这个故事中受益的人，请随意添加并通过评论做出贡献。*

*玩得开心！享受编码！*