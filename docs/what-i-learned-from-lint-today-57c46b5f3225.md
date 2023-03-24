# 今天我从林特身上学到了什么

> 原文：<https://levelup.gitconnected.com/what-i-learned-from-lint-today-57c46b5f3225>

## 一只自动备份的兔子。

![](img/e9639302cec6930bf313fc46375093cf.png)

照片由 [Iswanto Arif](https://unsplash.com/@iswanto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/hole?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

今天我被迫学了一点自动备份。我总是喜欢学习。但这一次，我希望你能给出更具体的答案。希望我的发现能更快地帮助其他人。

我从 android Studio 创建了一个新的 Android 应用程序，“无活动”-模板。刚创作完，我就在上面运行了脚本`./gradlew lint`；1 警告。

> 在 SDK 版本 23 及更高版本上，应用安装时会自动备份和恢复您的应用数据。考虑添加属性`*android:fullBackupContent*`来指定一个`*@xml*`资源，用于配置要备份的文件。更多信息:[https://developer . Android . com/training/backup/autosynccapi . html](https://developer.android.com/training/backup/autosyncapi.html)

棉绒警告实际上是极好的。非常详细和具体。更好的是，在我的实际项目中，当我使用 GCM 时，我得到了以下更准确的警告:

> 在 SDK 版本 23 及更高版本上，您的应用数据将自动备份，并在应用安装时恢复。您的 GCM regid 将不能跨恢复工作，因此您必须确保它从备份集中排除。使用属性`*android:fullBackupContent*`指定一个`*@xml*`资源，用于配置要备份的文件。更多信息:[https://developer . Android . com/training/backup/auto synccapi . html](https://developer.android.com/training/backup/autosyncapi.html)

关于自动备份的文档也是很好的读物，我建议每个开发人员都去读一读。你要知道你存储什么数据，让其他系统存储。它可能包含敏感数据。

到目前为止，一切看起来都很好，唯一的小问题是您开始时有一个警告。然而，有一个但是，这是具体到当你使用推送通知。lint 警告告诉我们，GCM regid 不能跨恢复工作。并且我们必须将其从备份集中排除。我们应该从文档中了解如何从备份中排除文件。但是哪个文件？

更令人恼火的是，Android Studio 有一个快速修复程序来解决 lint-warning。创建备份资源文件，在自动生成的 XML 文件中，有一个注释说:

```
<!-- Exclude specific shared preferences that contain GCM registration Id -->
```

如果我知道要排除哪个文件，我会很乐意的！至少我现在知道它在共享偏好中。我还从关于自动备份的文档中了解到:

> 如果您想要应用程序存储其状态，请将状态存储在共享偏好设置中，并在恢复时恢复共享偏好设置。

所以我现在的问题是解决共享首选项中排除哪些文件，包含哪些文件。因为我想从自动备份中获益，所以我更愿意只排除包含 GCM id 的文件。但是我的搜索没有给我任何具体的答案。所以我想在 Android Studio 的设备文件浏览器中查看设备，看看有哪些文件。这是我对所有人的第二个建议，去做吧。当使用第三方库时，有许多有趣的东西。

至少现在我有一个合格的猜测，所指的 id 存储在名为***com . Google . Android . GMS . appid . XML***的共享首选项文件中，并且谷歌搜索它并查看其内容似乎可以验证它就是它。但是我没有看到任何官方的说明。我在 ***no-backup*** 文件夹里还有一个文件，名为***com . Google . Android . GMS . appid-no-backup***。如果 id 不应该被备份，它不应该从一开始就存储在那里吗？

无论如何，有了这个关于我的共享偏好文件夹中驻留了来自我使用的不同依赖项的新知识，我觉得我只想包含我自己的文件。并希望有一个模式，这句话包括所有的 ***se.challenge.*。xml*** 然后我可以在我的应用程序中创建共享偏好时使用该模式。但是不幸的是，您不能在*完整备份内容*资源中使用模式匹配。唯一不同于完整文件名的是“.”指定整个文件夹，并且它还包括所有子文件夹。但是共享偏好设置不使用文件夹，所以它也帮不了我。

所以到今天为止，我唯一的解决方案是指定我在共享首选项中使用的每个文件，这不成问题。问题是要记住每次我或其他人添加新的共享偏好时也要将其添加到备份中。从好的方面来看，这是一种安全的方式，不会无意中备份数据。

## 我在指定自动备份规则方面的主要收获

*   默认情况下，Android 几乎包含了应用程序中的所有文件。但是一旦在 XML 中插入了 include-tag，就会覆盖它，只包含指定的内容。(它甚至跨越多个领域)。
*   在计算要备份的内容时，系统将聚合所有包括项，减去所有排除项。所以 exclude 总是优先。
*   您可以为五个不同的域指定；“文件”、“数据库”、“sharedpref”、“外部”和“根”。

作为最后一个指南，这是默认的规则集:

```
<?xml version="1.0" encoding="utf-8"?>
<full-backup-content>
  <include domain="file" path="." />
  <include domain="database" path="." />
  <include domain="sharedpref" path="." />
  <include domain="external" path="." />
  <include domain="root" path="." />
</full-backup-content>
```

因此，如果您只想更改共享首选项的规则，并且在我的示例中只包含一个文件，您仍然需要为其他域添加 includes。否则，他们将被排除在外。

关于如何编写 XML 的完整文档可以在这里找到:[https://developer . Android . com/guide/topics/data/auto backup # XML sync tax](https://developer.android.com/guide/topics/data/autobackup#XMLSyntax)

## 最后的想法

在 Android 中，备份设置、状态和偏好是一个强大但经常被遗忘的功能。向自动备份的转变使它在后台进行，但也带来了一些问题。确保你知道要备份什么，它能给你的用户带来一些魔力。但是如果不知道，就可以制造一些安全漏洞。

*PS。*不保留 GCM 注册 id 的原因是，如果恢复旧的，则使用旧的。使用用于另一个设备或另一个安装的旧 ID 会导致意外的行为，例如不发送推送通知。