# Flutter Web: Firebase 认证和 Google 登录

> 原文：<https://levelup.gitconnected.com/using-firebase-in-flutter-web-4b99952180aa>

![](img/0032e31f9c0ef48827071d97eb984316.png)

**更新时间:2020 年 5 月**

本教程已于 2020 年 5 月在**更新，因为旧教程为 Dart 使用了一个单独的 Firebase 包。**

这个更新的教程使用了标准的 Firebase 认证和 Google 登录包。使得真正的跨平台实现成为可能，它将在移动和网络上工作。

但是，请注意，本教程只显示了如何使用 Flutter web 的软件包。你可以在互联网上找到大量致力于移动实现的教程。

如果您感兴趣，可以在此处找到旧文章:

 [## 在 Flutter Web 中使用 Firebase |与 Flutter 同乐

### 在 Flutter Web 中使用 Firebase 就像在技术空间中使用任何东西一样——注意这篇文章的日期(2019 年 9 月)…

blog.funwith.app](https://blog.funwith.app/posts/using-firebase-in-flutter-web/) 

但是，这里建议的解决方案不再推荐。

# 新方式

下面是一个视频教程，向你展示了在一个 Flutter web 项目中添加 Firebase 认证和 Google 登录需要做的事情。如果你是 Firebase 的新手，我建议你看视频教程。

## 正在初始化

对于 Flutter web，您可以使用通常用于移动设备的 Firebase 认证包。

[](https://pub.dev/packages/firebase_auth) [## firebase_auth | Flutter 包

### 一个使用 Firebase 认证 API 的 Flutter 插件。对于其他 Firebase 产品的 Flutter 插件，请参见…

公共开发](https://pub.dev/packages/firebase_auth) 

对于谷歌登录。

[](https://pub.dev/packages/google_sign_in) [## google_sign_in | Flutter 包

### 一个用于谷歌登录的抖动插件。注意:这个插件仍在开发中，一些 API 可能不可用…

公共开发](https://pub.dev/packages/google_sign_in) 

软件包的发布页面提供了所有你需要设置和开始的信息。但是我们将在本文中讨论重要的细节。

首先，您需要在您的`pubsec.yaml`文件中添加相关的依赖项。

```
dependencies:
  firebase_auth:
  google_sign_in:
```

接下来，您需要将 Firebase JavaScript 库包含在您的`index.html`文件中(在 Flutter web 目录中)。对于一个 Flutter 项目，默认的 HTML 文件位于`web/index.html.`

下面是一个例子`index.html`。请注意标有**步骤 1、步骤 2 和步骤 3** 的区域，因为这些是您需要添加自己的配置的地方。下面提供了关于这些步骤的更多信息。

**步骤 1:** 添加 Firebase SDK 和您正在使用的其他 Firebase 产品。对于本教程，所有需要的是`firebase-auth.js`脚本。建议你把这个加在`<body>`标签的底部，但是在你使用 Firebase 之前(在`main.dart.js`脚本之前)。

您的版本可能与示例中的版本不同。你可以在 Firebase 网站上找到最新版本:

[](https://firebase.google.com/docs/web/setup#available-libraries) [## 将 Firebase 添加到 JavaScript 项目中

### 遵循本指南，在您的 web 应用程序中使用 Firebase JavaScript SDK，或者作为最终用户访问的客户端，例如…

firebase.google.com](https://firebase.google.com/docs/web/setup#available-libraries) 

*与身份验证类似，您需要导入将要使用的其他 Firebase 库，例如，Analytics、Firestore 等。*

**第二步:**接下来，您需要用项目的配置初始化 Firebase。Firebase 项目的配置可以在项目设置中的 Firebase 上找到。如果你找不到它，我建议你看这篇文章的视频教程。

第三步:最后，你需要添加你的谷歌登录客户端 Id。这在 Firebase 和 Google Cloud 中都有。如果你找不到这个，我建议你看视频教程。

**重要提示:**记得在 Firebase 项目中启用身份验证，例如*匿名登录*和*谷歌登录*。您还可以启用和使用大量其他登录机制(例如，*脸书*、*推特*、*电话号码*等)。).

# Firebase 身份验证和 Google 登录

初始化之后，现在只需要使用 Firebase 认证和 Google 登录包。下面是一个利用认证包的 Firebase 认证服务的例子。

看看演示应用程序的视频教程，看看我如何使用[提供者](https://pub.dev/packages/provider)包来注入认证服务，并使用它来处理应用程序的状态。

或者，看一看示例应用程序的代码:

[](https://github.com/funwithflutter/tutorials/tree/master/001-Flutter-Web-Firebase-Auth-Google) [## funwithfutter/教程

### 这是一个演示应用程序，展示了如何将 Firebase 身份验证和 Google 登录整合到一个 Flutter Web 中…

github.com](https://github.com/funwithflutter/tutorials/tree/master/001-Flutter-Web-Firebase-Auth-Google) 

# 结论

我希望你觉得这很有用。这是一个比前一篇文章好得多的实现。使用这个实现，我们不需要依赖移动和网络的独立包。

如果你想探索一个完全由 Flutter 创建的网站，以及阅读更多的 Flutter 文章，去看看 [Fun with Flutter 网站](https://funwith.app)。该网站也使用 Firebase 身份验证的这种实现。所以去试试吧。