# 使用 Turtle CLI 在本地构建您的独立 Expo 应用程序🐢

> 原文：<https://levelup.gitconnected.com/build-your-standalone-expo-app-locally-with-turtle-cli-87de3a487704>

构建 iOS。ipa 和 Android。使用 Turtle CLI 在本地使用 apk expo 应用程序

![](img/e3c3e5528a1cc3b27e0a758d6b165c2a.png)

阿道夫·费利克斯

# 介绍

最近，我不得不建立一个博览会应用程序。我通过使用 [expo.io](https://expo.io/) 服务器进行了尝试，但是我的构建在队列中，我没有时间浪费。

所以我决定看看是否有可能在本地开发一个世博应用，我已经找到了 **turtle-cli** 。

**什么是海龟 CLI:**

Expo 允许我们在他们的服务器上开发我们的应用程序。他们已经构建了一个特殊的 CLI 来实现它，并且开源了它。因此任何人都可以使用 Turtle CLI 并为之做出贡献。

它允许我们:

*   构建 iOS
*   构建 Android
*   进行持续集成

在本文中，我们将使用它在 iOS 和 Android 上进行构建🎯

您将需要:

*   一款世博应用
*   节点. js
*   macOS 和 Xcode(适用于 iOS)
*   浪子(应用程序自动化，你可以阅读[我关于它的文章](/automate-your-react-native-app-with-fastlane-ea516b4a893)

# 设置

首先，我们需要安装 Turtle CLI:

```
yarn global add turtle-cli 
```

然后，您可以检查 Turtle CLI 是否安装正确:

```
turtle-cli -V
```

# 签署应用程序

首先，我们需要创建证书、标识符和配置文件。

## 机器人

为了能够构建我们的 Android 应用程序，我们需要创建一个密钥库。您可以通过运行以下命令来实现这一点:(用您的值替换这些值)

```
keytool -genkeypair -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

示例:

创建 Android 密钥库

如果一切顺利，你应该有一个新的文件”。密钥库”。

## ios

若要签署 Apple 应用程序，您需要一个 Apple 开发者帐户。有多个步骤来获得我们的证书和个人资料。

由于这不是本文的目标，我们不会看到这些部分，但您可以查看这些资源:

*   **APP ID:** [快速指南](https://customersupport.doubledutch.me/hc/en-us/articles/229488228-iOS-How-to-Create-an-App-ID)
*   **iOS 证书:** [快速指南](https://customersupport.doubledutch.me/hc/en-us/articles/360001189514-iOS-How-to-Create-a-Distribution-Certificate)
*   **配置文件:** [快速指南](https://customersupport.doubledutch.me/hc/en-us/articles/229496268-iOS-How-to-Create-a-Provisioning-Profile)

# 为构建准备应用程序

现在，我们将准备在我们自己的服务器上构建我们的应用程序。首先，我们需要“导出”应用程序。在项目的根目录下，您可以执行以下命令:

*   如果您的服务器有 HTTPS:

```
expo export --public-url <YOUR_URL>
```

*   在只有 HTTP 的服务器上:

```
expo export --dev --public-url <YOUR_URL>
```

它将创建一个名为“dist”的新文件夹。此文件夹将用于通过 HTTP/(S)提供应用程序并构建应用程序。

# 为应用服务

一旦导出，我们可以启动一个本地服务器来服务应用程序。转到“dist”文件夹，通过运行以下命令为应用程序提供服务:

```
python -m http.server 8000
```

我也写过一篇关于这部分的文章。它将帮助你创建自己的服务器并使用 HTTPS: [用 Python 3](https://medium.com/@nicolas_dmg/serve-static-files-through-http-s-with-python-3-3ba4b6c5f57e) 通过 HTTP/(S)服务静态文件。

# 为 iOS 构建

是时候建立我们的。ipa 通过使用我们的新设置🛠

## 小验证

首先，一定要准备好这些东西:

*   Xcode
*   浪子安装([快速指南](/automate-your-react-native-app-with-fastlane-ea516b4a893))
*   签名密钥

现在，我们准备好了。我们走吧🚀

## 构建。国际认证协会

首先，不要忘记将服务器旋转到“dist”文件夹中。一切准备就绪后，您可以运行以下命令:

```
EXPO_IOS_DIST_P12_PASSWORD="YOUR_PASSWORD" \
turtle build:ios \
  --team-id YOUR_TEAM_ID \
  --dist-p12-path P12_CERT_PATH \
  --provisioning-profile-path PROVISIONING_PROFILE_PATH \
  --allow-non-https-public-url \
  --public-url http://127.0.0.1:8000/ios-index.json
```

此命令将构建您的. ipa。如果您的服务器上没有 HTTPS，您可以添加“`--allow-non-https-public-url`”。

如果一切顺利，你会得到你的”。IPA“in”/User/YOUR _ USERNAME/Expo-apps

## 用你的。国际认证协会

的”。ipa”允许你**安装你的应用**和/或**发布它**。

**安装在真实设备上:**

通过使用[苹果配置器 2](https://apps.apple.com/us/app/apple-configurator-2/id1037126344?mt=12) ，您将能够在您的苹果设备上安装您的应用程序。不要忘记使用即席配置文件构建您的应用程序，否则，您的应用程序可能无法启动

**分发:**

要在 App Store Connect 上上传您的应用，您可以使用 [Transporter](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) 。它允许您将您的版本直接发送到 TestFlight。

我们的应用已经可以在苹果设备上使用了🍏

# 为 Android 构建

由于我们希望在两个平台上都有我们的应用程序，我们需要构建 Android 版本。

## 小验证

再一次，我们需要核实一些事情:

*   一定要有 [Java JDK 8](https://jdk.java.net/java-se-ri/8-MR3)
*   检查 expo 服务器是否仍在运行

我们去安卓吧🚀

## 建造 APK

与 iOS 一样，我们需要执行一个命令(耐心点，构建可能会很长):

```
EXPO_ANDROID_KEYSTORE_PASSWORD="YOUR_KEYSTORE_PWD" \
EXPO_ANDROID_KEY_PASSWORD="YOUR_KEY_PWD" \
turtle build:android \
  --type apk \
  --keystore-path KEYSTORE_PATH \
  --keystore-alias "KEY_ALIAS" \
  --allow-non-https-public-url \
  --public-url http://127.0.0.1:8000/android-index.json
```

这个命令与 iOS 的命令非常相似，应该会给你一个新的 APK 文件。

## 用你的 APK

我们的 APK 已经准备好了，我们可以用不同的方式使用它:

*   共享文件并直接安装在 Android 设备上
*   使用 adb 通过 USB 安装
*   上传到 Google Play 控制台

现在，你可以在本地构建一个 Expo 应用程序。您将节省大量时间，因为您不必在 expo.io 服务器上排队。

如果您没有禁用 OTA(空中下载更新)，您需要让您的服务器启动并可从 web(为 dist 文件夹提供服务的 web)访问。

**你可以在这里** **找到我的其他文章并关注我** [**。感谢阅读，我希望你今天学到了一些新东西🚀**](https://medium.com/@nicolas_dmg)

[](/automate-your-react-native-app-with-fastlane-ea516b4a893) [## 使用浪子自动化您的 React 原生应用程序

### 简化截图、测试版部署、应用商店部署和 React 原生应用的登录🚀

levelup.gitconnected.com](/automate-your-react-native-app-with-fastlane-ea516b4a893) [](https://medium.com/@nicolas_dmg/serve-static-files-through-http-s-with-python-3-3ba4b6c5f57e) [## 使用 Python 3 通过 HTTP/(S)提供静态文件

### 使用 Python 3 通过 HTTP 和 HTTPS 提供文件的简单指南🐍

medium.com](https://medium.com/@nicolas_dmg/serve-static-files-through-http-s-with-python-3-3ba4b6c5f57e)