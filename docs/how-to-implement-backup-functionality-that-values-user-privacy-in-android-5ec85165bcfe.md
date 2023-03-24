# 如何在 Android 中实现重视用户隐私的备份功能

> 原文：<https://levelup.gitconnected.com/how-to-implement-backup-functionality-that-values-user-privacy-in-android-5ec85165bcfe>

## 如果您想让您的用户成为其数据的 100%所有者，您可以将备份直接存储在他们的 Dropbox 帐户中。

![](img/b2023a36b17ec4c88e507a516760debb.png)

杰森·登特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你是一个非常关心用户隐私的开发人员，你可能会问自己，我可以做些什么来让我的应用程序收集的数据更加透明？

我发现自己几次陷入这种困境，特别是在实施备份功能时。我开始调查在 Android 世界中开发这项功能的一些途径，有一天我发现 Dropbox 提供了一个访问用户个人帐户的 API。

当然，这种访问并没有让开发者完全控制存储在 Dropbox 账户中的个人数据。它只允许开发人员写入用户存储中的一个文件夹，这个文件夹包含允许读写的文件。用户也可以访问应用程序在 Dropbox 帐户中写入的数据，所以最好通知他们为什么该文件夹会在那里，以及如果他们想保留他们的数据，为什么不应该删除它。

该解决方案使备份功能对用户完全透明，而且对开发人员来说也是免费的，因此您不需要为您还需要维护和管理的外部云存储付费。

我看到的唯一缺点是，如果用户没有可用的存储空间，他将不得不支付 Dropbox 或任何你想使用的解决方案，以获得更多空间，他可能不喜欢这样，但老实说，应用程序备份通常不会是一个巨大的文件。因此，如果您需要为每个用户存储大量数据，这可能不是您想要采用的方法。

现在，我们将了解如何将 Dropbox 连接到我们的应用程序，但如果您也想自动化备份过程，您可以使用 WorkManager。我在这里写了一个关于如何做的教程，如果你想看看的话。

[](/implement-backup-automation-with-workmanager-in-android-107c3d0cb166) [## 使用 Android 中的 WorkManager 实现备份自动化

### 多亏了 WorkManager，Android 中的后台任务自动化变得前所未有的简单。

levelup.gitconnected.com](/implement-backup-automation-with-workmanager-in-android-107c3d0cb166) 

# 将 Dropbox 与我们的应用程序连接

这个过程非常简单，我们需要在 Dropbox 中创建一个开发者帐户，然后将 SDK 添加到我们的 Android 项目中。为此，Dropbox 在他们的库的 [README 中有一个指南，所以我将把重点放在 Android 代码方面的这篇文章上。](https://github.com/dropbox/dropbox-sdk-java)

和往常一样，在 Android 中使用外部库的第一步是将它添加到 Gradle 模块并同步项目。为此，我们只需要下面一行:

```
dependencies {
    // ...
    implementation 'com.dropbox.core:dropbox-core-sdk:5.3.0'
}
```

为了访问我们将要存储备份数据的 Dropbox 文件夹，我们需要用户登录他的帐户。为此，Dropbox SDK 将启动浏览器，以便用户可以给我们权限，一旦该过程完成，系统将从浏览器返回到我们的应用程序。

我们将看到的第一件事是如何开始登录过程。这就像调用 Dropbox SDK 提供给我们的一个方法一样简单。在下一段代码中，*客户端*对象将在稍后被初始化，我们马上就会看到，代码的其余部分非常简单。

*startoauth 2 authentication*获取 *config* 对象、我们应该已经在 Dropbox 开发人员控制台中获得的 API 密钥以及我们可以读写数据所需的权限。

现在我们需要使用之前获得的凭证，这样我们就可以开始从云中上传和下载数据。

在下面的代码片段中，我检查用户是否已经登录到 Dropbox，如果我没有凭据，Dropbox 客户端将不会启动。应该在 Activiy/Fragment 的 *onResume* 回调中启动 *initDropboxClient* 方法，以便当用户使用浏览器登录后应用程序返回时，Dropbox 客户端使用现在存储在 *Auth* 对象中的凭据进行初始化。

现在我们已经设置好了一切，我们将看看如何上传和下载数据。为此，Dropbox 提供了两种方法来请求我们想要与之交互的文件。

> 在下一个示例中，我还将强制覆盖上一次备份，因此请记住指定您想要遵循的策略。

还有其他可用的功能，如检查我们上传的文件的大小或文件的最后更新日期。如果你感兴趣，你可以检查一下 *DropboxManager* 类，在那里你可以看到我在文章中解释的全部代码。

不要忘记，如果你使用收缩和模糊，你需要用 ProGuard 规则保护 Dropbox SDK 的一些代码，正如你在这里看到的。如果你不知道什么是收缩和模糊，以及它如何使你的应用受益，你可以看看我写的这个关于这个话题的故事。

[](/simple-tips-to-optimize-android-apps-334355a318d5) [## 优化 Android 应用的简单技巧

### 用几行代码优化你的 Android 应用的大小和性能

levelup.gitconnected.com](/simple-tips-to-optimize-android-apps-334355a318d5) 

# 结论

如您所见，实现简单、免费，并给予用户对其数据的完全所有权。这是我最喜欢的在 Android 中开发备份的解决方案之一，我希望它能帮助你。

如果你想阅读更多这样的内容，并支持我，不要忘记检查我的个人资料，或给媒体一个机会，成为会员，以获得我和其他作家的无限故事。一个月只要 5 美元，如果你使用这个链接，我会得到一小笔佣金。

[](https://medium.com/@molidev8/membership) [## 通过我的推荐链接加入 Medium—Miguel

### 阅读米格尔的每一个故事(以及媒体上成千上万的其他作家)。你的会员费直接支持米盖尔…

medium.com](https://medium.com/@molidev8/membership)