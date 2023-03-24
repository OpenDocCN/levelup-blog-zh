# 使用 React Native 进行高级单元和快照测试

> 原文：<https://levelup.gitconnected.com/advanced-unit-and-snapshot-testing-with-react-native-ea746feb5b2e>

![](img/68e6faea3d0cfa0a614bfa30b98d2b37.png)

随着 React Native 被越来越多的人采用，这个社区已经得到了极大的扩展，因此它的核心也有了很多变化。目前，0.6 是下一个候选版本。我从 0.4 开始使用 React Native，正如你们中的许多人可能知道的那样，即使是小版本升级也很有可能会有突破性的变化(特别是对于推送通知的`react-native-firebase`)，所以也许在未来，我可能会写一篇文章来单独解决这些问题。本文的主要目的是帮助那些真正希望 React 原生应用程序拥有 100%代码覆盖率的人，因为这是推动我们发现这些技术的原因。

我将使用 Jest 和 Enzyme 来捕捉这些场景，因为 Jest 是由脸书为 React 创建的，Enzyme 是一个很好的快照测试工具，涵盖了各种 UI 相关的测试。还有，在这个项目中，我用的是`[redux-thunk](https://github.com/reduxjs/redux-thunk)`。关于如何利用 [*Redux*](https://redux.js.org/) 我就不赘述了，因为有足够多的文章讨论这个问题。

首先让我们处理一些配置。

这里是`package.json`文件:

Jest 的一个我觉得很酷的特性是`coverageThreshold`对象，它允许在执行代码覆盖方面的高度可重配置性。例如，我们为关键组件设置了更高的阈值，特别是那些更难调试的组件，比如我们的 *Redux* 相关代码。这为我们提供了关于应用程序行为的高度确定性。

为了本文的目的，我只列出了几个依赖项，但是可能还有其他的*需要在生产中复制。*

如果你注意到了，我还使用了一个由传递给 Jest 的`setupFilesAfterEnv`数组执行的`setupTests`文件。这是为了设置应用程序中最常被*模仿的*功能。将这个添加到`coveragePathIgnorePatterns`数组中很重要，这样我们就不会因为跟踪一个不应该被测试的文件而降低我们的覆盖率。看一下安装文件:

很好，现在设置已经完成，让我们开始创建我们的页面。

所以在这个例子中，我们有一个页面，用户可以禁用和启用**生物认证**，以及**推送通知**。尽管这里发生了一些事情。主要是:

1.  我们需要处理用户何时离开应用程序，以便在他们的本地设备设置中启用通知。
2.  我们需要用户确认他们确实打算通过某种级别的授权来启用生物认证，在这种情况下，这将是重新输入他们的密码。

这些任务涉及异步活动，例如访问设备存储和将应用程序放在后台，这改变了我们测试的前景。让我们看看我们的`SettingsPage`容器。

所以让我们试着分析一下，我们的函数在这里做了什么。

我们的`onUpdateNotificationPermissions`功能允许我们将用户发送到他们各自设备上的设置页面，这当然是`async`，因为一旦我们请求权限，我们必须首先等待响应。

我们的`init`功能将从设备的存储器中加载用户可能已经选择的先前保存的设置。我们不想向不具备这些功能的设备显示生物特征选项，因此我们做了一些检查来确定是否应该呈现`icon`和`toggleSwitch` 。

我们的`updateNotificationPermissionStatus`函数将异步更新我们的内部状态，以显示用户是否选择了启用推送通知。

我们的`handleAppStateChange`将处理应用程序进出前台的场景，并相应地更新我们的内部状态。

我们的`onBiometricAuthenticationSwitch`实际上设置了内部状态，当用户想要改变生物特征设置时，我们切换一个模态(因此分派到模态动作)来请求他们的凭证。

我们的`checkBiometricAuthenticationForUpdate`将运行每个渲染，看看用户是否改变了他们的生物特征设置。

既然已经解释了主要功能，让我们看看测试。

因此，我们导入了一些在整个测试中使用的实用程序或助手函数，这有助于我们抽象出设置测试的一些必要的细节，尤其是较新版本的`jest`、`enzyme`和`react-native`。我稍后将讨论该文件，但是让我们在这里检查一些测试。

让我们看看如何测试它`Should update notification permissions after app resume`。我们将推送通知权限设置为 **false** ，并将 **AppState** 对象从 react native 设置为 *active。*通过 Jest 的快照功能，我们可以对按钮的外观进行快照，我们希望它是关闭的——在这种情况下，`isOn`被设置为**false**——因为这是我们在模拟函数中设置的推送通知权限。然后，我们将应用程序置于后台，将权限设置为**真**，然后*等待* 应用程序返回活动状态，并期望`isOn`现在被设置为**真**。

生物认证的一个重要方面显然是它的安全性，因此我们必须确保我们正在妥善保护任何秘密。当用户想要在这个设置中验证他们自己时，我们必须首先将他们的身份存储在 [*钥匙链*](https://developer.apple.com/documentation/security/keychain_services) *，*中，然后我们可以在稍后的时间点检索它。

所以我们来看一下`Should reset keychain and async storage on biometric option`测试。当用户不再希望启用生物认证时，我们必须重置钥匙串的通用密码，并永久存储用户不再希望以这种方式签名的信息。所以最终我们会期望 biometric 被设置为 **false** ，并期望我们的*resetGenericPassword***被调用。**

**所以我希望通过阅读这些测试能够很容易理解，因为这是将许多技术细节抽象到下面的文件`testUtils.js`中的主要原因，我将对此进行解释。**

**从`render`函数开始，由于 redux，我们有多种不同的方法来呈现一个组件进行测试。我提出的一个特殊的[问题](https://github.com/airbnb/enzyme/issues/2185)与使用`reduxForms`进行渲染有关，这需要多次调用`dive`方法。对于`pureComponent`渲染方法来说，在测试中有时我们也想更深入一些。**

**还有一些其他的实用程序来帮助[监视](https://jestjs.io/docs/en/es6-class-mocks#keeping-track-of-usage-spying-on-the-mock)函数，因此有了`SpyContainer`，但这只是为了让测试更具可读性。**

**感谢您花时间阅读这篇文章，我希望它是有用的。如果您喜欢它和/或发现它有帮助，请留下👏。如果没有，请留下评论，让我知道我可以为下一个改进什么。我是一名全栈开发人员，擅长 React Native 和 Angular。单元测试快乐！**