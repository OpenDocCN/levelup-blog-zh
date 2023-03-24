# Flutter local_value 包:存储本地数据的最简单方式

> 原文：<https://levelup.gitconnected.com/flutter-local-value-package-simplest-way-to-store-local-data-8fbc5819d79e>

## 将值写入本地存储的最简单方法。

![](img/68b0c9040ba95d645e4f4a13f888c4e3.png)

[TL；大卫:酒吧到了。戴夫！](https://pub.dev/packages/local_value)

大约一年前，[我写了这篇文章，](/the-4-ways-to-store-data-locally-in-your-flutter-app-that-youre-going-to-need-abdafa991ae3)讲述了将值写入本地存储的不同方法。即使一年后，这也是我最活跃的文章之一。我对那篇文章的看法过多。

这意味着 Flutter 开发人员试图找到存储他们的文件的最佳方式，但是在从事许多项目之后，很明显大多数开发人员并不打算将任何类型的文件写入本地存储——本质上，他们正在序列化对象以备后用。诸如当前用户、得分数据、缓存的 API 调用…这些对象每次都以完全相同的方式存储:

1.  写一个`toJson`和`fromJson`的方法。
2.  确定您将在哪个本地存储器中存储对象(共享首选项？申请文件目录？应用程序支持目录？暂时的？)
3.  编写一个在序列化后将值写入本地存储的方法
4.  编写一个读取该值并将其反序列化为对象的方法
5.  可以选择编写一个删除方法

如果你完成了第 6 步，恭喜你！现在对下一个对象再做一次:)。

另一个主要问题是`path_provider`不能在 web 上工作——所以如果你把你的方法移植到 web 上，你将不得不写一个`kIsWeb`检查。如果你不走这条路，用一个通用的方法，你最终会遇到一个类型安全问题:你必须保证你写的和你读的完全一样，否则你的 json 转换会抛出。

所有这些都是为了简单地写入本地存储！高度重复的工作应该被抽象成一个包。

有时候，您需要将高度结构化的数据写入磁盘。任何大规模的东西，或者任何需要高度结构化或高度关联的东西。这不是为了那个— [我推荐看一下](https://pub.dev/packages/moor_flutter) `[moor](https://pub.dev/packages/moor_flutter)` [，](https://pub.dev/packages/moor_flutter)，它在作为 Flutter 的本地数据库方面做得非常好。

相反，local_value 用于面向对象的数据。我个人已经用过自己的包了(狗粮！)在两个应用程序中。

*   将访问令牌写入磁盘:我希望这些令牌能够随着时间的推移而持久化，但是我并不担心它们会泄露，就像我担心刷新令牌一样。
*   编写通知树:flutter 中的通知需要大量的管理工作。您必须跟踪哪个通知将过期、哪个通知进来以及它们去哪里、跟踪它们元数据等等。我编写了一个 JSON 树，允许我将通知数据嵌套得尽可能深。
*   编写当前用户:当前用户有一些数据，比如他们的名字和 ID。这必须保存在后端，但是每次获取它都是一个很大的痛苦和浪费。相反，我使用`local_singleton`将其写入磁盘。
*   列一个清单:我有一个应用程序，作为基于颜色的化学分析的联邦学习模型(实际上我对化学一无所知，我在学校的化学系工作！).用户可以从云中下载模型。我不希望用户每次都必须重新提取数据，所以我使用`LocalValue`将数据保存到磁盘！

我不打算深入讨论如何使用，但这里有一个来自`readme`的片段:

```
//locally save a singleton:
final localCounterValue = LocalSingleton<int>(id: 'counter_val');await localCounterValue.write(5);print(await localCounterValue.read()); //5//or save multiple related objects:
final localUserScores = LocalValue<int>(basePath: ['user_scores']);await localUserScores.write('user_1', 15);
await localUserScores.write('user_2', 24);print(await localUserScores.read('user_1')); //prints 15
print(await localUserScores.read('user_2')); //prints 24
```

我认为非常简单的 API 具有很大的灵活性。

不过，不要相信我的话:自己去试试吧！

如果你有建议/顾虑，请让我知道，我随时乐意学习。如果你遇到了一个 bug 或者想要建议一个特性，请到 github issues 告诉我！

我绝对没有从中获得任何经济利益，也不打算这样做。我只是想写这个来帮助人们。