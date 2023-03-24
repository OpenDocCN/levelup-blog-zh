# 如何在 Android 中实现带通知的前台服务

> 原文：<https://levelup.gitconnected.com/how-to-implement-a-foreground-service-with-notifications-in-android-5e249ed3ebeb>

![](img/af7a26aa125ea2a58f8762d10834b011.png)

照片由 [Jonas Leupe](https://unsplash.com/@jonasleupe?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

前台服务是一个后台任务，它执行将要显示给用户的操作，因此用户主要通过通知来了解更新。例如，如果我们正在开发一个 Android 应用程序来跟踪[番茄工作法](http://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)，我们希望向用户显示剩余时间，或者他当前是否在工作或休息。

一旦理解了什么是前台服务，我们将首先看到我们如何实现服务，然后如何控制它的通知来通知用户正在发生什么。

# 实现前台服务

为了能够实现前台服务，我们需要做的第一件事是请求启动这种后台任务的许可。为此，我们只需打开我们的清单文件并添加以下行:

```
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
```

> 在这种情况下，我们添加了一个[安装时权限](https://developer.android.com/guide/topics/permissions/overview#install-time)，所以没有必要要求用户将它们让给我们，所以我们完成了这一部分。

现在，让我们创建包含我们的服务逻辑的类，因为我们只需要扩展[服务类](https://developer.android.com/reference/android/app/Service)。不要忘记，和往常一样，在创建服务之前，我们需要进入清单，在`<application>`标签中，告诉我们的应用程序一个新的服务将要被创建。在本例中，该类被调用 TimerService，因此:

```
<service android:name=".TimerService"
    android:exported="false" />
```

> 请注意，导出的属性具有 false 值，因为对于本例，我们不希望其他应用程序与服务进行交互。

目前，我们的 TimerService 类将看起来像这样。

有了这几行代码，我们就有了一个工作的前台服务。请注意，我们正在覆盖 *onBind* 函数，因为我们将需要它将服务连接到不同的应用程序组件，就像一个活动。

还有其他我们可以覆盖的方法，例如， *onStartCommand* 和 *onDestroy* ，我们可以用它们来注册和注销一个 [BroadcastReceiver](https://developer.android.com/guide/components/broadcasts) ，我们可以用它们来连接服务和通过通知中的按钮触发的动作。如果我们正在实现一个番茄工作法应用程序，我们可以用这个暂停或结束当前的番茄工作法。

现在服务已经设置好了，我们需要启动它并将其绑定到活动，这样我们就可以访问服务了。首先，我们需要实现一个 [ServiceConnection](https://developer.android.com/reference/android/content/ServiceConnection) ，它将在服务连接和断开时给我们两个回调。

现在，我们实际上可以推出这项服务了。上面的代码将为我们提供两个东西，一个 TimerService 实例，以及一个控制变量，用于知道服务何时绑定到活动。这一点很重要，因为如果活动被破坏，我们希望消除这种联系以节省资源。我们也可以使用控制变量来保持显示一个[闪屏](https://medium.com/@molidev8/implementing-a-splash-animation-with-the-core-splashscreen-api-on-android-ec3fe00d105a)，直到服务被加载。

> 移除活动和服务之间的连接并不意味着服务将停止并被销毁，因此在这个示例中，如果活动结束，服务将继续运行。

# 实施通知

在 Android 中，从 8.0 版本开始，我们需要创建一个“通道”来显示通知。因此，为通知准备服务的第一步是用一些配置创建通道。我们可以配置振动模式、声音、频道名称…但这里最重要的是通知的重要性级别，您可以在这里检查。

一旦创建了通知通道，我们就可以开始显示通知了。在通知中，我们可以设置一个图标(它的内容必须是黑色的，背景是透明的)，一个标题，通知的内容，甚至可以使用 Intents 和 BroadcastReceivers 与服务连接的按钮。我们将看到如何做最简单的通知，因为我不想让教程太长，但如果你对如何使用通知按钮与服务进行交互感兴趣，请留下评论，我将创建另一个帖子来讨论它。

在这种情况下，我们希望发布通知来替换旧的通知，因此，我们需要一个 ID。我们可以生成一个这样的例子。

```
private var id: Int = UUID.randomUUID().hashCode()
```

最后，为了显示通知:

最后要做的是从我们的服务中调用这些函数，例如，覆盖 *onStartCommand* 方法。

# 我们完了！

简单对吗？现在我们有了一项服务，它可以在应用程序的生命周期内持续存在，并向用户显示正在发生的事情的通知。不要忘记留下你想添加到帖子中的任何评论或想法，或者如果你需要查看更多代码，请查看项目的存储库。

[](https://github.com/molidev8/foreground-service-sample) [## GitHub-moli dev 8/foreground-service-sample:一个示例应用程序，展示了如何实现一个前台…

### 展示如何在 Android - GitHub 上实现带有通知的前台服务的示例应用程序…

github.com](https://github.com/molidev8/foreground-service-sample) 

如果你想阅读更多这样的内容，并支持我，不要忘记检查我的个人资料，或给媒体一个机会，成为会员，以获得我和其他作家的无限故事。每月只有 5 美元，如果你使用这个[链接](https://medium.com/@molidev8/membership)，我会得到一小笔佣金。

[](https://medium.com/@molidev8/membership) [## 通过我的推荐链接加入 Medium—Miguel

### 阅读米格尔的每一个故事(以及媒体上成千上万的其他作家)。你的会员费直接支持米盖尔…

medium.com](https://medium.com/@molidev8/membership)