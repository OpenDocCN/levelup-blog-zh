# Expo vs React 原生 CLI:引导新 React 原生应用的指南

> 原文：<https://levelup.gitconnected.com/expo-vs-react-native-cli-a-guide-to-bootstrapping-new-react-native-apps-6f0fcafee58f>

![](img/d8d560920da296ad9afb1a058cdf3ce2.png)

*这是我的书《反应本地烹饪书》的节选，第二版，由帕克特出版社出版，将于今年冬天出版。*

React Native 已经成为一种非常流行的主要使用 JavaScript 代码构建跨平台原生应用程序的方式。已经有不少好文章描述了 React Native 中开发的利与弊，比如[*React Native:值得吗？*](https://proandroiddev.com/react-native-is-it-worth-it-part-i-398ec3fb9db7) *，* [*React Native:利弊*](https://www.netguru.co/blog/react-native-pros-and-cons) *，* [*为什么要考虑 React Native*](https://www.viget.com/articles/why-you-should-consider-react-native/) *，*[*React Native 的局限性每个开发者都应该了解*](https://www.simform.com/react-native-limitations-app-development/) 。

如果您认为 React Native 适合您的项目，那么这篇文章就是为您准备的！我们将讨论你的应用程序启动选项，以及每个选项的优缺点。

初始化和开发应用程序有两种方法:Expo 和 React Native CLI。直到最近，还有一种截然不同的第三种方法，使用 Create React Native App (CRNA)。CRNA 已经与 Expo 项目合并，只是作为一个独立的实体继续存在，以提供向后兼容性。

Expo 属于第一类工具，它以牺牲一些灵活性为代价，提供了一个更加健壮和开发者友好的开发工作流程。通过 Expo 启动的应用程序也可以访问 Expo SDK 提供的许多有用的功能，如[条形码扫描仪](https://docs.expo.io/versions/latest/sdk/bar-code-scanner)、 [MapView](https://docs.expo.io/versions/latest/sdk/map-view) 、 [ImagePicker](https://docs.expo.io/versions/latest/sdk/imagepicker) 等等。

通过`react-native init`命令使用 React Native CLI 初始化应用程序提供了灵活性，但降低了开发的便利性。用`react-native init`命令创建的 React 原生应用被称为是纯粹的*React 原生应用，因为没有任何原生代码对开发者隐藏。*

根据经验，如果第三方软件包的文档说明您需要运行命令`react-native link`作为设置过程的一部分，那么该软件包只能用于纯 React 本机应用程序。

那么，当您使用 Expo 构建应用程序的过程进行到一半，却发现 Expo 开发工作流不支持您的应用程序需求所必需的包时，您该怎么办呢？幸运的是，Expo 有一个方法可以将 Expo 项目转换成一个纯粹的 React 本地应用程序，就像它是用`react-native init`命令创建的一样。当一个项目被弹出时，所有的原生代码都被解压到 ios 和 android 文件夹中，`App.js`文件被拆分成`App.js`和`index.js`，暴露出挂载根 React 原生组件的代码。

但是，如果您的 Expo 应用程序依赖 Expo SDK 提供的功能，该怎么办呢？毕竟，用 Expo 开发的大部分价值来自于 SDK 提供的优秀特性，包括 AuthSession、Permissions、WebBrowser 等等。

这就是 ExpoKit 发挥作用的地方。当您选择从项目中推出时，您可以选择将 ExpoKit 作为推出项目的一部分。包含 ExpoKit 将确保您的应用程序中正在使用的所有 Expo 依赖项将继续工作，并且还让您能够继续使用 Expo SDK 的所有功能，即使在应用程序被退出后。

不幸的是，使用 Expo 进行开发的一些好处将会在这个过程之后丢失。Expo 减轻了为 iTunes Store 和 Google Play store 构建二进制文件的负担，让您不必使用 Xcode 和 Android Studio，消除了项目中 React 原生升级的麻烦，并可以管理您的推送通知管道。如果你选择退出博览会，这些功能你将不得不说再见。

为了更深入地了解弹射过程，你可以在[https://docs.expo.io/versions/latest/expokit/eject](https://docs.expo.io/versions/latest/expokit/eject)阅读关于弹射的世博会文件。

[![](img/26797da0642875dc5a034a11d5991f56.png)](https://gitconnected.com)

# React 本机开发工具

与任何开发工具一样，在灵活性和易用性之间会有一个折衷。我鼓励您从使用 Expo 作为 React 本机开发工作流开始，除非您确定需要访问本机代码。

# 世博会

根据 expo.io 网站的说法，expo 是“一个围绕 React Native 构建的免费开源工具链，帮助你使用 JavaScript 和 React 构建原生 iOS 和 Android 项目。”世博会正在成为一个独立的生态系统，由 5 个相互关联的工具组成:

1.  **XDE:** 世博发展环境。Expo 团队称 XDE 为“图形开发环境”，它有 macOS、Windows 和 Linux 版本。这是您将用于创建、构建、服务和共享 React 原生应用的主要工具。Expo 甚至有一个将你编译的应用发布到应用商店/谷歌 Play 商店的工作流程！
2.  **世博 CLI:** 世博命令行界面。如果您喜欢生活在终端中，CLI 以命令行形式提供了基于 GUI 的 XDE 的所有功能。
3.  **世博客户端:**一款适用于 Android 和 iOS 的 app。此应用程序允许您在设备上运行 Expo 应用程序中的 React 原生项目，而无需安装。这允许开发人员在真实设备上热重装，或者与任何人共享开发代码，而无需安装。
4.  **Expo Snack:** 托管于 [https://snack.expo.io](https://snack.expo.io/) ，这款网络应用允许你在浏览器中使用 React 原生应用，并实时预览你正在工作的代码。如果你曾经使用过 [CodePen](https://codepen.io/) 或 [JSFiddle](https://jsfiddle.net/) ，那么零食就是应用于 React 原生应用的相同概念。
5.  **Expo SDK:** 这是一个 SDK，它包含了一组精彩的 JavaScript APIs，这些 API 提供了基本 React 原生包中没有的原生功能，包括与设备的加速度计、摄像头、通知、地理定位等配合使用。Expo 创建的每一个新项目都附带了这个 SDK。

这些工具共同构成了世博会的工作流程。借助 XDE 世博会或 CLI，您可以创建和构建内置 Expo SDK 支持的新应用。XDE/CLI 还提供了一种简单的方法来为您的开发中应用程序提供服务，方法是自动将您的代码推送到亚马逊 S3，并为项目生成一个 url。从那里，XDE/CLI 生成一个链接到托管代码的 QR 代码。在你的 iPhone 或 Android 设备上打开 Expo 客户端应用程序，扫描二维码，然后 BOOM 就有了你的应用程序，配备了实时/热重装功能！由于该应用程序托管在亚马逊 S3 上，你甚至可以与其他开发者实时共享正在开发的应用程序。(请注意，分享正在开发的 Expo 应用程序的二维码方法已被苹果公司在 iPhone 上禁用，现在是 Android 独有的功能。不过，这款应用仍然可以通过短信分享。)

XDE/CLI 还可以简化最终应用程序的构建。

# 2.react-本机初始化

创建新 React 本机应用程序的原始引导方法是 React 本机 CLI 提供的`react-native init`命令。我将这种方法列在最后，因为它不再是官方团队在其入门指南中推荐的初始化 React 本机应用程序的方法。

在 React 原生社区中，用这种方法创建的应用被称为纯 React 原生应用，因为所有的开发和原生代码文件都向开发者公开。虽然这提供了最大的自由，但也迫使开发人员维护本机代码。如果您是一名 JavaScript 开发人员，因为打算只使用 JavaScript 编写原生应用程序而加入了 React Native 潮流，那么必须在 React Native 项目中维护原生代码可能是这种方法的最大缺点。

另一方面，在使用 react-native init 命令引导的应用程序时，您可以访问第三方插件，并直接访问代码库的本机部分。你还可以避开 Expo 目前的一些限制，特别是无法使用背景音频或背景 GPS 服务。

# 3.CRNA 或创建-反应-本机-应用程序

如前所述，自从 CRNA 项目与 Expo 合并后，这种引导方法就被弃用了。然而，在撰写本文时，官方的 React Native *入门*文档仍然引用使用它，所以我包含了一些关于它的基本信息。

[CRNA(create-react-native-app)](https://github.com/react-community/create-react-native-app)是开源 React 社区 [react-community](https://github.com/react-community) 的项目。它在概念上与由脸书构建和维护的 [CRA (create-react-app)](https://github.com/facebook/create-react-app) 项目相同，但是是为 react 本地项目设计的。用 CRNA 初始化一个新的 React 本地项目只需要一个命令:

```
create-react-native-app my-app
```

# 规划您的应用和选择您的工作流程

每个应用程序都是独一无二的，因此选择正确的引导过程在很大程度上取决于应用程序的需求和您喜欢的开发类型。在选择开发工作流程时，您可以问自己以下几个问题:

*   我需要访问代码库的本机部分吗？
*   我的应用程序中需要 Expo 不支持的任何第三方包吗？
*   我的应用程序不在前台时需要播放音频吗？
*   我的应用程序不在前台时需要位置服务吗？
*   我需要推送通知支持吗？
*   我在 Xcode 和 Android Studio 中工作舒服吗？

根据我的经验，世博会通常是最好的起点。它显著提高了开发期间的生活质量，简化了推送通知和发布到商店的流程，并通过 Expo SDK 提供了丰富的功能。如果你确定你的应用需要 Expo 应用目前缺乏的支持(例如背景音频和背景位置服务)，或者如果你确定你需要在原生层工作，我建议只从纯 React 原生应用开始开发。

我还推荐浏览托管在 [http://native.directory](http://native.directory/) 的原生目录。这个站点有一个非常大的第三方包目录，可以用于 React 本地开发。网站上列出的每个包都有一个估计的稳定性、流行度和文档链接。然而，可以说原生目录的最佳特性是能够根据它们支持的设备/开发类型过滤软件包，包括 **iOS** 、 **Android** 、 **Expo** 和 **Web** 。这将有助于您缩小应用所需特性的包选择范围，并更好地告知哪种开发工作流最适合您的应用需求。

# 进一步阅读

*   世博会常见问题:[https://docs.expo.io/versions/latest/introduction/faq.html](https://docs.expo.io/versions/latest/introduction/faq.html)
*   用 ExpoKit 开发:[https://docs.expo.io/versions/latest/guides/expokit.html](https://docs.expo.io/versions/latest/guides/expokit.html)
*   分离使用 expo 和 react-native init 命令创建的项目的区别:[https://github.com/expo/expo/issues/1412](https://github.com/expo/expo/issues/1412)
*   从 Create React Native App 弹出:[https://github . com/React-community/Create-React-Native-App/blob/master/ejecting . MD](https://github.com/react-community/create-react-native-app/blob/master/EJECTING.md)

*如果您觉得本文有帮助，请点击👏。您还可以* [*关注我*](https://medium.com/@warlyware) *获取更多关于 React Native、Vue 和 JavaScript 开发的文章。*

[![](img/d9fe9ae1a34de6a28338eae427dd6438.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react-native) [## 学习 React Native -最佳 React Native 教程(2019) | gitconnected

### 10 大 React 原生教程。课程由开发人员提交并投票，使您能够找到最佳反应…

gitconnected.com](https://gitconnected.com/learn/react-native)