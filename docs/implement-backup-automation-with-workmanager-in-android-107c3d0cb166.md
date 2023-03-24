# 使用 Android 中的 WorkManager 实现备份自动化

> 原文：<https://levelup.gitconnected.com/implement-backup-automation-with-workmanager-in-android-107c3d0cb166>

## 多亏了 WorkManager，Android 中的后台任务自动化变得前所未有的简单。

![](img/c6e2d5ba4a4275381374c6666cf65496.png)

[胡思远](https://unsplash.com/@siyuan_hu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

没有人喜欢丢失数据并不得不重新开始。它可能最终会让你失去这个用户，因为这会产生挫败感。当用户更换手机或登录到另一个平台(你的应用可用)时，用数据恢复应用的状态总是很好的。

在 Android 中，有不同的方法来进行备份，但今天我将分享我如何通过使用 WorkManager Jetpack 库来实现可编程的备份功能。

# 工作管理器简介

[WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) 是一个实现可编程任务的简单工具，可以在后台运行。有了这个库，你可以运行你想要定期执行的任务，或者只运行一次，但是你可以做更多类型的工作。

在这篇文章中，我将集中讨论定期运行的任务，因为我们通常希望在每次备份时都不需要用户输入就可以执行备份。

# 配置工作管理器

和往常一样，我们需要做的第一件事是将库添加到项目中。

```
implementation "androidx.work:work-runtime-ktx:2.7.1"
```

在 Gradle sync 之后，我们可以开始设置一个 worker，它是将要运行任务的实体。当创建一个将要执行代码的工人时，我们可以指定一系列约束。例如，如果我们要进行备份并将其上传到云服务，我们不希望在设备未连接到互联网时运行备份，因为该任务会失败。您还可以使用其他约束来考虑电池消耗等问题。您可以在这里检查所有可用的约束[。](https://developer.android.com/topic/libraries/architecture/workmanager/how-to/define-work#work-constraints)

为了实现周期性任务，我们需要下面代码中的*PeriodicWorkRequestBuilder*。在这个构建器中，我们将使用不同的单位来指定任务的约束和周期。这里我设置了一个网络连接约束和一个由用户指定的时间间隔(以天为单位)。

每次用户设置他的备份首选项时，这个 worker 将被启动，以便在时机到来时触发它。要启动这个工人，我们只需要这样做。

您可能已经注意到*PeriodicWorkRequestBuilder*采用了一个*备份器*数据类型*。*这是指定要运行的代码的地方。

为了构建，worker 必须从这些类中的[扩展。对于这个特定的任务，一个*协同工作器*是最合适的，因为我们正在运行一个可能需要一些时间来执行的代码，并且我们不希望有短时间的突发。](https://developer.android.com/topic/libraries/architecture/workmanager/how-to/define-work#expedited)

在下面的代码中，我将备份功能封装在另一个类中，因为我使用 Dropbox 来存储备份。在接下来的故事中，我将解释如何做这一部分，因为我想把这篇文章放在 WorkManager 的中心，所以如果你感兴趣，不要忘记检查回购或给我一个关注，以便我发布文章时通知你。

代码非常简单，只需从 *CoroutineWorker* 扩展并在 *doWork* 函数中启动您的代码，该函数将被强制覆盖。

# 搞定了。

如果你想看到完整的代码，你可以查看下面的库，特别是 *BackupUserData* 和 *BackupWorker* 类。

[](https://github.com/molidev8/Recipe-Vault) [## GitHub - molidev8/Recipe-Vault:一个存储、共享和备份食谱的 Android 应用程序

### Recipe Vault 是一个 Android 应用程序，允许用户存储、共享和备份他最喜欢的食谱。它遵循干净…

github.com](https://github.com/molidev8/Recipe-Vault) 

如果你想阅读更多这样的内容，并支持我，不要忘记检查我的个人资料，或给媒体一个机会，成为会员，以获得我和其他作家的无限故事。这只是 5 美元一个月，如果你使用这个链接，我会得到一小笔佣金。

[](https://medium.com/@molidev8/membership) [## 通过我的推荐链接加入 Medium—Miguel

### 阅读米格尔的每一个故事(以及媒体上成千上万的其他作家)。你的会员费直接支持米盖尔…

medium.com](https://medium.com/@molidev8/membership)