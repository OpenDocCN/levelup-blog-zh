# 如何在没有任何连接的情况下在两个 Flutter 应用程序之间传输数据

> 原文：<https://levelup.gitconnected.com/how-to-transfer-data-between-two-flutter-apps-without-any-type-of-connection-ae808e78a00a>

![](img/264bb3929019dfaf95303d38e252b5dd.png)

没有互联网。没有局域网。没有电话信号，蓝牙，什么都没有！你处于飞行模式，你处于保存电池模式，几乎用光了你最后的 1%，没有笔，没有纸，但是你仍然想与某人可靠地交换信息。你的选择之一可能是拍下对方在手机上给你看的任何东西，然后转录数据。像个流浪者！但是…你可能有另一个选择，在这个故事中，我将解释使你能够在 Flutter 中创建这样一个应用程序的构建模块，然后去构建它，并获得名声和肮脏的财富。

你会问，你怎么报答我？嗯，你真是太好了！阅读这篇文章已经足够支持了，但你还可以鼓掌、鼓掌、关注、[订阅](https://attilavago.medium.com/subscribe)甚至[成为会员](https://attilavago.medium.com/membership)——这才是欣赏的终极表现！🤗

# 我们将要使用的技术的一点背景

这将只是一个关于 Flutter 的快速演讲，因为我在过去已经写了很多关于它的文章。我还想强调的是，我将在这里展示的构建模块在几乎所有框架中都绝对可用，无论是本机、混合还是 web，它们并不是 Flutter 独有的，但这是我测试过的框架，我知道它 100%有效。你可以在下面阅读我最新的颤振文章。

[](/building-flutter-apps-on-the-16-m1-pro-952d2325bbbe) [## 在 16 英寸 M1 专业版上构建颤振应用

### “从我的英特尔 i7 15”完全转移到 M1 Pro 16，并开始开发我的 Flutter 应用程序。我同样担心…

levelup.gitconnected.com](/building-flutter-apps-on-the-16-m1-pro-952d2325bbbe) [](/flutters-skia-engine-takes-cross-platform-app-development-to-a-new-level-85cc5f92ca9b) [## Flutter 的 Skia 引擎将跨平台应用开发提升到了一个新的高度

### 好处远远超过一些人在考虑“绘制”应用程序而不是“构建”它们时可能感到的“怪异”

levelup.gitconnected.com](/flutters-skia-engine-takes-cross-platform-app-development-to-a-new-level-85cc5f92ca9b) 

## 摆动

作为谷歌的一个快速发展的框架，无论是在功能上还是在受欢迎程度上，它现在都是一个真正的替代者——我认为是一个更好的替代者——来应对 native、Ionic 和其他人。它非常快，难以置信地接近本机速度，并且实际上可以在设备上本机运行。这包括所有设备，甚至嵌入式设备。这自然意味着支持所有主要的移动和桌面操作系统，甚至 Linux！哦，还可以在浏览器中运行——尽管对我来说这是最不令人兴奋的部分。

作为一个 UX 设计系统，它使用材料设计，但它也提供了苹果特定的组件库，如果你想走这条路。也可以两个设计系统都不理会，做自己的事！鉴于它本质上是在屏幕上绘制像素，只要你在 2D 空间，你就可以做任何你喜欢的事情。它使用的语言是 Dart，与 JavaScript 和/或 TypeScript 非常相似，因此您会有宾至如归的感觉。

不过，请注意。**如果你是 Flutter 的超级新手，这篇文章对你来说可能会觉得太高深**，所以尽管你非常欢迎阅读它，你可能会发现，一旦你 [**至少学会了 Flutter 的基础知识**](https://docs.flutter.dev/get-started/install) ，你会从中获得更多。

## 二维码

另一项用于实现无连接数据传输目标的技术是 QR 码，这是一项已有 27 年历史的技术。传统上，它们用于以更半模拟的方式读取数据。二维码是一种数据存储解决方案，尽管乍听起来可能有些奇怪。是的，比软盘小得多，但足够大——3KB，或 4296 个字母数字字符——来存储有价值的数据，见鬼，甚至是整个游戏！当然，我知道你不相信我，我必须证明我所说的一切，因为假新闻，所以在这里，看这个，然后所有人都相信，继续阅读你离开的地方。

此外，二维码不仅可以快速读取，还可以即时生成！整洁！**生成的每个二维码都是独一无二的(在上下文中),如果您愿意，可以永久保存。**这些代码可以被你设备的摄像头读取，所以从技术上来说，它们可以生活在任何地方；在一张纸上，在一张照片上，在一面墙上，蚀刻在一座山的侧面(不要这样做)或者纹在你的皮肤上，如果那是你的事情！想让你的前 26 条推文流芳百世吗？去吧！

# 把所有的放在一起

所以，如果你能在飞行中读取和生成二维码，这意味着…是的，你可能猜到了，你基本上可以通过空气传输数据。你只需要一台设备生成数据，另一台设备读取数据，一旦数据被读取，就将其保存在本地。现在你有了，一秒钟前还在一台设备上的一些数据的拷贝。

请注意，这不是一个循序渐进的教程，所以如果你期望只是复制和粘贴的东西，你的运气不好。但是**我将为您提供创建概念验证**所需的所有必要构件，并强调您可以在哪些方面采取不同的做法，或者改进方法。

## 准备好你的观点

如果你还没有安装 Flutter，请安装它，并运行`flutter doctor`来确保你的设置是正确的。开始一个项目最令人沮丧的部分是开始写一些令人惊奇的代码，却发现有些东西还没有完成。彻底的士气低落。一旦设置完毕并准备就绪，您基本上只需要三个视图:

*   登录屏幕—出于概念验证的考虑，这将有一个读取和生成按钮，
*   QR 生成器屏幕—由生成按钮触发，
*   QR 阅读器屏幕—由 read 按钮触发。

后两个视图还包含一个返回着陆视图的机制。你可以使用标准的 Flutter Nav，但是你也可以只使用一些按钮和基本的 Flutter routing，就像下面这样:

```
initialRoute: Landing.*id*,
routes: {
  Landing.*id*: (context) => Landing(),
  Reader.*id*: (context) => Reader(),
  Generator.*id*: (context) => Generator(),
},
```

其中`id`基本上只是每个视图中的一个静态字符串:

`static String id = 'landing_view';`

一般来说，您的视图代码会像这样开始:

```
class Landing extends StatelessWidget {
  static String *id* = 'landing_view';
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        // your code goes here
      ),
    );
  }
}
```

## 让我们来点魔法吧！

既然你的观点已经准备好了，你显然需要以某种方式生成这些二维码。在你的`pubspec.yaml`文件中你需要添加两个包: [qr_flutter](https://pub.dev/packages/qr_flutter) 和 [barcode_scan_fix](https://pub.dev/packages/barcode_scan_fix) 。

```
qr_flutter: ^3.2.0
barcode_scan_fix: ^1.0.2
```

前者将生成二维码，后者将帮助阅读它们。很简单，柠檬汽水！如何使用它们？只是阅读每个插件的文档页面，好吗？😄

**您的** `**generator()**` **视图将需要两个附加组件:**

*   文本输入
*   提交按钮

基本上，你想要实现的 UX 是这样的:

1.  用户点击文本输入
2.  用户键入最多 4296 个字母数字字符的文本(为了避免用户失望和错误情况，不允许超过 4296 个字符)
3.  用户通过提交按钮提交他们的输入
4.  视图被生成的 QR 码接管

**你的** `**reader()**` **视图将需要更少的开发**，因为它将只需要一个阅读按钮，带有一个更简单的 UX:

1.  用户点击阅读按钮
2.  用自动打开的相机扫描其他用户的 QR 视图
3.  扫描和翻译的内容呈现在视图中

现在你知道了，你已经通过空气发送了一个重要的信息，除了老式的光，没有任何数据连接。很整洁，是吧？😁

# 不过你可以让它更有用一点…

以上是证明一个概念的好方法，也是一个非常有趣的实验，但是就其本身而言，它没有多大用处。我当然不会把它提交给应用商店。然而，你可以添加一个实际上使它值得应用商店的改进——数据库。

不要惊慌，没有什么过于花哨，只是一个普通的 sqflite 数据库。

[](https://pub.dev/packages/sqflite) [## sqflite | Flutter 包

### Flutter 的 SQLite 插件。支持 iOS、Android 和 MacOS。支持交易和批量自动版本…

公共开发](https://pub.dev/packages/sqflite) 

一旦你将它添加到你的`pubspec.yaml`文件中，基于这个包的文档，你将为自己建立一个简单的数据库，并在应用程序第一次加载时初始化它。

也许除了长消息之外，您还可以添加另外两个输入字段，分别用于发送者姓名和接收者，甚至还可以添加一个主题行。所有(现在总共有四个)字段都将发送`String` 数据，所以没什么稀奇的。如果您愿意，也可以将日期和时间作为`String`以 ISO8601 格式发送，如下所示:

```
YYYY-MM-DD HH:MM:SS.SSS2021-11-22 10:23:08.123
```

也许您希望在以后的某个时候按照日期和时间对您的邮件进行排序，在这种情况下，从一开始就拥有数据集将会很方便。基于发件人和收件人，您还可以创建选项卡式视图来分别显示已发送和已接收的邮件！**看，天空才是真正的极限**，我只是在这里说说想法，但你如何处理数据输入和输出，**你有多有创意，完全取决于你和你的想象力**。好消息是，在你的应用中有一个数据库，给它更多的价值，并把它从概念证明提升到一些潜在有用的东西。如果你最终获得了灵感并开发了一个应用程序，我很乐意听到它并测试它，所以不要害羞，分享吧！

# 有趣的事实…

我在新的 16 英寸 M1 Pro 上写了这篇文章，但我在这里分享的所有代码实际上都是在旧的 M1 13 英寸 MacBook Pro 上开发的，现在也在 16 英寸 M1 Pro 上运行，所以如果你不节省 RAM，Flutter 在苹果的新芯片上工作得很好。我写了大量关于这两款机器的文章，所以如果你感兴趣，也可以看看这些故事。

[](/twelve-months-into-using-apples-m1-chip-and-my-opinions-have-changed-1831e77d657e) [## 使用苹果公司的 M1 芯片 12 个月后，我的看法发生了变化

### 或者，也许更像是……演变成了我怀疑随着时间推移可能会发生的事情，不管我愿不愿意。使不…

levelup.gitconnected.com](/twelve-months-into-using-apples-m1-chip-and-my-opinions-have-changed-1831e77d657e) [](/building-flutter-apps-on-the-16-m1-pro-952d2325bbbe) [## 在 16 英寸 M1 专业版上构建颤振应用

### “从我的英特尔 i7 15”完全转移到 M1 Pro 16，并开始开发我的 Flutter 应用程序。我同样担心…

levelup.gitconnected.com](/building-flutter-apps-on-the-16-m1-pro-952d2325bbbe) [](/macbook-pro-2021-early-hands-on-impressions-development-and-content-creation-workflow-setup-4fc8e92f3ee2) [## MacBook Pro 2021 早期动手体验、开发和内容创作工作流程设置…

### 我为新的 MacBook Pro 2021 设置了软件开发和内容创建，有趣的是…

levelup.gitconnected.com](/macbook-pro-2021-early-hands-on-impressions-development-and-content-creation-workflow-setup-4fc8e92f3ee2) 

*你知道吗，每当你* [*订阅成为我们作家*](https://attilavago.medium.com/membership) *中的一员，就会得到一份提成？你有一大堆好文章，我们喝咖啡。对我来说听起来很公平……*

**Attila Vago**——*软件工程师、编辑、作家，偶尔也是音乐评论家。务实的实干家，乐高迷，Mac 用户，酷酷的书呆子。JS 和颤振爱好者。无障碍倡导者。*