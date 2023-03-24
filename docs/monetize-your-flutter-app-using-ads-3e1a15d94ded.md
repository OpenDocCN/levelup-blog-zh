# 利用广告将你的 Flutter 应用货币化

> 原文：<https://levelup.gitconnected.com/monetize-your-flutter-app-using-ads-3e1a15d94ded>

![](img/1b0a087ac77f4e2a92e84739158abcf8.png)

佩皮·斯托扬诺夫斯基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

你是否迷失在互联网中，寻找将广告整合到你的手机应用程序中的最佳方式？你真幸运！你已经为你的问题找到了最好最简单的解释。本文将解释如何将 Google 的 Admob 与 Flutter 集成。

## **第一步:你需要一个 Admob 账户**

1.  转到 [Admob](https://admob.google.com/home/) 。
2.  使用您的 Google 帐户注册。
3.  前往应用程序>添加应用程序
4.  选择您的应用程序是否已发布。
5.  如果已发布，您可以在商店中搜索您的应用程序，也可以创建新的应用程序，然后单击“添加”。
6.  将为您的应用生成一个应用 Id。

> 样本应用 ID:**ca-App-pub-3940256099942544 ~ 3347511713**

7.单击“添加广告单元”创建广告单元。

8.选择广告格式。

9.为您的广告单元命名，在高级设置中选择您的首选项，然后单击“创建广告单元”。

10.将为您的广告单元生成一个广告单元 ID。

> 样本广告单元 ID:**ca-app-pub-3940256099942544/6300978111**

## 步骤 2:添加依赖项

在项目的 pubspec.yaml 文件中添加 firebase_admob 包。

## **步骤 3:更改 AndroidManifest.xml 文件**

将以下元数据标记和您的应用 ID 添加到您的应用的 AndroidManifest.xml 文件中。

## 步骤 4:集成 Firebase

1.  转到[火焰基座控制台](https://console.firebase.google.com/)。
2.  点击“添加项目”。
3.  为您的项目命名，选择您的首选项，然后单击“创建项目”。
4.  在 Firebase 项目中，选择平台(ios、android、web)。
5.  Firebase 将在整合过程中为您提供平稳的指导。

集成 Firebase 将让你访问各种数据，如**会话、用户统计数据、收入、崩溃报告**以及更多与你的应用相关的数据。

## 步骤 5:配置广告

将以下导入添加到您的 dart 文件中。

```
import 'package:firebase_admob/firebase_admob.dart';
```

根据广告格式，使用以下代码定义广告的属性:横幅广告、插页广告。**不要忘记在有状态小部件**的 **状态中添加代码。**

## 步骤 6:初始化并加载 ad

如下所示更改您的 **initState()** 方法，以便初始化和加载 ad。

答对了。我们都准备好了，可以走了。真正的广告需要一些时间才能在你的应用中显示出来。如果测试广告有效，你可以高枕无忧了。😎

> **阅读关于在你的应用内放置广告的** [**Admob 政策**](https://support.google.com/admob/answer/6128543?hl=en) **。**

点击这里访问我的 Playstore 开发者页面。在另一篇精彩的文章中再见。在那之前，编码快乐！💻