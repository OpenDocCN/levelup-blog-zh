# 代码推送:正确的方式

> 原文：<https://levelup.gitconnected.com/codepush-the-right-way-7cf24bccde4f>

> 本文不是关于安装 CodePush 或如何使用它。您可以随时在以下位置查阅该文档:[https://learn . Microsoft . com/en-us/app center/distribution/code push/rn-overview](https://learn.microsoft.com/en-us/appcenter/distribution/codepush/rn-overview)

当谈到为你今天的下一个项目选择移动交叉平台时，你可能会缩小范围，要么选择 React Native，要么选择 Flutter。我通常脚踏两个阵营，并试图不坚持只有一个，但我必须说，我有更多的同情反应本土。其中一个主要原因是，我相信很多其他人也一样:**代码推送！**

![](img/01424f1089dc7770eb115f5a53102a3e.png)

照片由[罗布·汉普森](https://unsplash.com/@robman?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

对于那些不知道的人来说， **CodePush** 允许你发送热门更新(或者空中更新，如果你喜欢的话)，这些更新不一定需要经过 App Store 或谷歌 Play 商店的审查。如果您正在处理一个需要在短时间内进行大量更改的项目，例如电子商务应用程序，这一点尤其有用。你只需在你的应用业务中修正或引入一个新功能，然后发送更新，就这样。你的用户会立即收到它，就像一个网站一样。

根据文档，安装完库之后，您只需包装您的入口组件，通常是 App.js，就可以开始了。即使这足以让它工作，你可能想改变你的用户获取更新的方式，以使你的应用程序更加用户友好。

但是为什么我们需要改变它呢？实际上你不必。如果你进行基本安装，人们将在下次重启时获得更新，如果是紧急更新，你可以在应用中心的仪表板上将其标记为“立即”,用户可以立即获得更新。

然而，这种即时更新会导致你的用户误解你的应用程序的行为，因为它一得到更新就会突然重启。通过我过去的项目，用户向我反馈这种行为是“错误”,但我仍然意识到这对 UX 不好，所以我决定改变它。

首先，我觉得用户，尤其是手机 app 用户，是不欢迎意想不到的东西的。在每一次互动或应用程序功能中，他们都期待一些对他们来说似乎有意义的熟悉的东西。因此，首先，我们将添加一个模式，提醒用户有可用的更新，这样他们就会知道发生了什么。CodePush 具有 updateDialog 选项，您可以在包装组件时指定这些选项，如下所示:

当有更新时，这个设置会弹出一个本地警告，这样你的用户就会知道有一个更新在等着他们。标题、主体和按钮都是英文的，但是 **updateDialog** 参数也有一个对象，你可以在那里自定义它们。这里有一个完整的德语例子:

根据更新类型的不同，该对话框会略有不同。对于标准更新，您可以关闭对话框并选择以后安装，而强制更新让您别无选择，只能安装。

尽管我们通过显示一个弹出窗口来通知用户更新，但在下载时我们也需要显示某种进度指示器。这确保了我们是否真的得到了更新。CodePush 没有内置的进程，所以我们需要从头开始。

CodePush 提供了几个关于更新阶段的事件以及更新期间传输的字节，如下所示:

这里我们有**codepushstatusdichange**和**codepushstatusdichange**事件，它们基本上允许我们实现我们想要的。我知道它是一个类组件和类函数，你的 App.js 可能是一个功能组件。您不能混合使用这两种方法，但是您总是可以使用 **codePush.sync** 方法来代替，如下所述[https://learn . Microsoft . com/en-us/app center/distribution/code push/rn-API-ref # codepushsync](https://learn.microsoft.com/en-us/appcenter/distribution/codepush/rn-api-ref#codepushsync)。请注意，一些用户体验到以这种方式提供的状态可能无法按预期工作。

现在为了清楚起见，让我们坚持使用类函数，只为 CodePush 创建一个单独的组件。我还想在应用程序的底部弹出一个微小的进度条，当下载时，它会读取“下载，重新启动…”并重新启动应用程序的更新。为此，我们必须手动管理流程。让我们来看看:

代码太多了。我们来分解一下；

首先我们定义两个变量， **stat** 和 **downloadProgress。**变量 **stat** 基本上负责我们组件中的每一个布局变化，包括打开/关闭我们的模态。另一方面, **downloadProgress** 是我们的进度条所必需的。如果你愿意，你可以显示一个百分比，但在我看来，如果你有一个可视化的表示，这是没有必要的。

然后我们有了一个基本的布局。我们有一个模态和一个包装器视图，我应用了 react-native-reanimated 中的布局动画，为里面即将到来的文本制作收缩效果。

我们使用 2 个 CodePush 事件，下载和安装，其余的只是为了记录。我们还想手动重启应用程序，所以在代码推送选项中，我将安装模式更改为下一次重启应用程序。你可能已经注意到了一件重要的事情，我们需要用 codePush 而不是 App.js 来包装这个组件，因此如果你以前做过，你必须打开你的应用程序。

还有一件事；CodePush 也适用于开发环境。这可能不是你想要的。如果你想避免在模拟器上工作时得到更新，你可以如下编辑底部:

```
export default codePush({
checkFrequency: __DEV__ ? codePush.CheckFrequency.MANUAL : codePush.CheckFrequency.ON_APP_RESUME,
installMode: codePush.InstallMode.ON_NEXT_RESTART,
mandatoryInstallMode: codePush.InstallMode.ON_NEXT_RESTART,
updateDialog: true,})(CodePushManager);
```

最后，我们需要将组件放在 App.js 中，最好放在每个提供者组件之外，如下所示:

就是这样！我们有一个很好的代码推送更新的预加载程序。您可以尝试不同的设置，更改不同的更新和安装模式，以最适合您的项目。

CodePush 提供了更新应用程序内容的最快方式，并能立即修复错误。还有一些其他相关的主题，比如版本控制。这是一件重要的事情，我打算在我接下来的文章中提到它。直到那时；

编码快乐！