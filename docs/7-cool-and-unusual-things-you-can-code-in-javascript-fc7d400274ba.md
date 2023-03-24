# 你可以用 JavaScript 编写 7 个很酷很不寻常的东西

> 原文：<https://levelup.gitconnected.com/7-cool-and-unusual-things-you-can-code-in-javascript-fc7d400274ba>

![](img/abd0e38bd37d42a408fb2e8a5b0fff7b.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)拍摄

JavaScript 无疑是有史以来最流行的语言之一。它很快，很容易学习，而且可以在任何浏览器上运行，不需要安装大量额外的库和应用程序。

我们大多数人将 JavaScript 与 web 技术联系在一起。我们可以在 React 和 Vue 中使用它来创建漂亮的动态前端应用程序，或者在 Express 中编写后端。

然而，这只是该语言许多可能用途中的一部分。在这篇文章中，我想和你分享我们能用 JavaScript 编写的其他神奇的东西。

# 1.无服务器功能

传统上，当我们考虑后端时，我们认为应用程序运行在服务器上，处理用户请求。但是，如果不是一个单独的应用程序，而是创建许多可以部署到云上的小功能，会怎么样呢？这些功能可以彼此独立运行，并执行一些特定的任务。这几乎就是无服务器架构。

我们可以用 NodeJS 在 AWS 上写 [Lambda 函数](https://aws.amazon.com/lambda/)，在微软 Azure 上写 [Azure 函数](https://azure.microsoft.com/en-us/services/functions/)，在谷歌云上写[云函数](https://cloud.google.com/functions)。无服务器方法的主要优势是能够创建可扩展的应用程序，而无需管理服务器。

# 2.移动应用

JavaScript 非常适合开发跨平台的本地和混合移动应用。有很多框架允许你使用一个代码库为 iOS 和 Android 设备编写应用程序。我想给你看两个最受欢迎的。

## 反应自然

React Native 是一个由脸书支持的库，允许你为 Android 和 iOS 构建原生应用。如果你知道 React，你可以很快学会 React Native，因为两个库非常相似。

在 React Native 中，用 JavaScript 编写的代码用本机代码呈现。您可以将原生组件与 React 代码相结合，构建动态、可扩展的应用程序。

## 离子的

Ionic 是另一个构建移动应用的混合框架，它允许您使用标准的 web 技术，如 JavaScript、HTML 和 CSS 来构建跨平台的应用。它支持 Angular、React 和 Vue，所以你可以在你喜欢的框架中编码。

React Native 的目标是创建原生应用，Ionic 则试图创建类似原生应用的 PWAs(渐进式 Web 应用)。React 使用本地组件来构建界面——Ionic 使用 CSS 样式的 HTML 元素。

Ionic 的主要优势是在框架方面的选择自由。强制你使用反应。

# 3.桌面应用

有一个叫做 [Electron](https://www.electronjs.org) 的框架，允许你使用 JavaScript、HTML 和 CSS 创建桌面应用。它不需要任何原生技能，除非你想做一些高级的东西。您可以轻松构建适用于 Mac、Windows 和 Linux 的应用程序。

对于渲染器过程，您可以使用任何 JavaScript 库，如 React、Angular 或 Vue。

# 4.比赛

[Phaser](https://phaser.io/) 是 JavaScript 游戏框架中最受欢迎的选择。您可以创建可以直接在浏览器中运行的 HTML5 游戏。还可以使用 Apache Cordova 或 Phonegap 等工具将游戏编译成 iOS、Android 和桌面应用程序。

# 5.物联网设备

如果你曾经想让你的家“更智能”，安装一些传感器和物联网设备，监控温度、湿度和其他属性的负载，稍后可以在你的手机上显示…现在是时候了！

[mongoseeos](https://mongoose-os.com/)是一个物联网(IoT)固件开发框架，允许您使用 JavaScript 对 ESP32、ESP8266 或 STM32 等微控制器进行编程。要做到这一点，你不需要了解 C 和 C++，你可以将一些传感器连接到一个电路板上，并用 JS 编写逻辑。

[这是关于如何构建物联网设备来测量家中温度的官方教程。说到微控制器，我可以推荐 ESP8266。你可以买三个。从](https://mongoose-os.com/docs/mongoose-os/quickstart/develop-in-js.md)[到这里](https://amzn.to/3avS2rE)，低至 12 美元。这些主板包括 WiFi 模块，您只需一个命令就可以将它们连接到您的网络。

# 6.机器学习

机器学习一直是 Python(或 R)的事情。但是事实证明，你可以使用 [TensorFlow.js](https://www.tensorflow.org/js) 库在 JavaScript 中做几乎同样的事情。该库的 JS 版本的优势在于，你可以轻松地在浏览器中直接使用 ML，并创建令人惊叹的基于人工智能的交互式 web 应用。

你可以在官方[文档](https://www.tensorflow.org/js/tutorials)中了解更多。[这里有一个吃豆人游戏的例子](https://storage.googleapis.com/tfjs-examples/webcam-transfer-learning/dist/index.html)，你可以使用浏览器中训练的图像来玩。

# 7.幻灯片

Reveal.js 是一个演示框架，允许你使用 JavaScript、HTML 和 CSS 创建令人惊叹的演示。您可以从 11 个美丽的主题中选择，或者创建自己的主题。幻灯片可以保存为网站并进行部署。敬赫罗库。由于 reveal.js 演示文稿可以直接在浏览器中运行，您不必像在普通演示软件中那样处理兼容性问题。

这也是说话者的观点，所以你永远不会说不完你想说的话。

感谢您的阅读！如果您有任何问题或反馈，请随时留下您的评论！

编码快乐！