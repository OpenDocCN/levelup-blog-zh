# 通过两个简单的步骤在 Android 中实现本地备份

> 原文：<https://levelup.gitconnected.com/implement-local-backups-in-android-with-2-simple-steps-77d79d65ff4c>

## 开发备份功能工具的指南

![](img/9aaf67f0b79f4b8f40aa42c2e48b8194.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

W 当用户下载一个应用程序时，除了应用程序的主要用途之外，他们还会假设一些基本功能。这些特性包括隐私性、可用性、可靠性…

作为开发人员，我们有时会过分关注某个特定的功能，而忽略了用户认为理所当然的重要的基本需求。

在这篇文章中，我将集中讨论可靠性，我将谈论备份用户数据。

# 可用的替代品

谷歌提供了几个选项来在安卓系统中实现这一功能，几乎不费吹灰之力就能激活。

如果您不想使用这些工具，您可以开发自己的本地备份。出现这种情况的一些原因是:

*   不知道谷歌复制了什么。
*   [空间限制](https://developer.android.com/guide/topics/data/autobackup)。
*   尽可能多地控制代码。
*   开发一个可重用的库。

总的来说，本地备份可能看起来不是一个有用的功能，但一些用户会喜欢它，例如，如果你让他们选择将其存储在外部 SD 卡中。例如，如果他们想要将数据传输到新设备。

或者，您可能希望这项功能稍后存储在任何云服务中，就像我在这个故事中展示的那样。

[](/how-to-implement-backup-functionality-that-values-user-privacy-in-android-5ec85165bcfe) [## 如何在 Android 中实现重视用户隐私的备份功能

### 如果您想让您的用户成为其数据的 100%所有者，您可以将备份直接存储在他们的 Dropbox 帐户中。

levelup.gitconnected.com](/how-to-implement-backup-functionality-that-values-user-privacy-in-android-5ec85165bcfe) 

所以，事不宜迟，让我们看看如何通过两个简单的步骤做到这一点。如果你愿意，你可以自己用代码检查项目，一切都在 *BackupUserData.kt* 类中。

[](https://github.com/molidev8/Recipe-Vault) [## GitHub - molidev8/Recipe-Vault:一个存储、共享和备份食谱的 Android 应用程序

### Recipe Vault 是一个 Android 应用程序，允许用户存储、共享和备份他最喜欢的食谱。它遵循干净…

github.com](https://github.com/molidev8/Recipe-Vault) 

# 1.管理数据库

我假设您使用 SQL 数据库保存用户数据，但是如果您使用其他东西，比如共享首选项(我个人不推荐)，使用这种方法应该很简单。

我们将复制包含数据库的文件。为了逆转这一过程，我们在恢复时将其复制回同一目录。

> 请小心关闭数据库，以防止在写操作发生时启动备份。

在下面的代码片段中，您可以看到如何实现这一点。这段代码关闭数据库，将文件压缩成 zip 文件，然后将文件上传到 Dropbox(对于仅本地备份，忽略最后一步)。

# 2.将所有内容保存到. zip 文件

为了节省空间并便于将备份上传到任何云服务，最好将所有内容压缩到一个 zip 文件中。

在代码中，我读写两个目录，处理图像文件和整个文件夹。

# 简单对吗？

不需要依赖 Google 提供的工具及其局限性，开发自己解决方案的代码也很紧凑。

我提供了我能想到的简单解决方案，但它并没有就此停止，因为您还可以使用 WorkManager 来自动化备份过程，正如这里所解释的那样。

[](/implement-backup-automation-with-workmanager-in-android-107c3d0cb166) [## 使用 Android 中的 WorkManager 实现备份自动化

### 多亏了 WorkManager，Android 中的后台任务自动化变得前所未有的简单。

levelup.gitconnected.com](/implement-backup-automation-with-workmanager-in-android-107c3d0cb166) 

如果你想阅读更多这样的内容，并支持我，不要忘记检查我的个人资料，或给媒体一个机会，成为会员，以获得我和其他作家的无限故事。一个月只要 5 美元，如果你使用这个链接，我会得到一小笔佣金。

[](https://medium.com/@molidev8/membership) [## 通过我的推荐链接加入 Medium—Miguel

### 阅读米格尔的每一个故事(以及媒体上成千上万的其他作家)。你的会员费直接支持米盖尔…

medium.com](https://medium.com/@molidev8/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)