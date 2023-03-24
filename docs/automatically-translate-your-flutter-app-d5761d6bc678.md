# 自动翻译你的 Flutter 应用

> 原文：<https://levelup.gitconnected.com/automatically-translate-your-flutter-app-d5761d6bc678>

## 颤动的包裹聚光灯

![](img/fa2c781fdfc1f1a1f6b4aeefc7ad3abe.png)

# 介绍

如果你想覆盖全球受众，将你的应用国际化是关键。Flutter 通过结合 ARB 文件的`flutter_localizations` & `intl`包使这变得容易。然而，你仍然需要翻译你的信息。这就是为什么我很兴奋地向大家介绍`[auto_translator](https://pub.dev/packages/auto_translator)`！不仅仅是因为在国际化你的 Flutter 应用程序时，它是你工作流程的一个很好的补充，还因为它是我正式发布的第一个包！😃

在设计了这个程序供我个人使用后，我觉得它值得与 Flutter 社区分享。我应该注意到，在我编写代码和决定发布之间，有几个类似地执行这个任务的包被放到了 [pub.dev](https://pub.dev) 中。事实上，为了发布，我不得不重命名我的包，因为原来的名字已经被使用了。😅但是，我觉得我的包给开发人员带来了不同的价值，所以我继续发布。我们来看看`auto_translator`能做什么。

# 特征

创建该软件包时考虑了 4 个主要目标:

1.  易用性。
2.  处理复杂的 ARB 字符串、条件和变量。
3.  使用最少配额的高质量上下文翻译。
4.  与`flutter_localizations`和`intl`封装无缝集成，这是 Flutter 国际化的首选方法。

这些目标在实践中看起来如何？

## 易用性和无缝集成

这两个目标在某种程度上是齐头并进的。`auto_translator`使用与`flutter_localizations` & `intl`相同的文件进行配置。在`l10n.yaml`中，一个额外的`translator`部分允许您定义您的目标语言，并且在模板 ARB 文件的元数据部分，您可以根据需要告诉`auto_translator`到`ignore`或`force`对特定字符串的翻译。

运行之前的另一个步骤是设置谷歌云翻译，如果你还没有访问该服务的权限。操作说明可在[这里](https://cloud.google.com/translate/docs/setup)找到。然后将 API 密匙保存到项目根目录下的一个名为`translator_key`的文件中。

要使用`auto_translator`，只需在`pubspec.yaml`中的`dev_dependencies`下添加`auto_translator`。然后，一旦配置完毕，运行`flutter pub run auto_translator`。

## 最小云翻译配额使用量

`auto_translator`使用一种新颖的方法来处理谷歌云翻译查询，以减少这些查询中的字符数，同时保持基于上下文的翻译质量。这当然是为了节省配额的使用，最大限度地降低成本。如果你不是在翻译数量惊人的字符串，那么遵守免费使用限制应该没有问题。

为了确保没有不必要的配额使用，`auto_translator`永远不会为已经翻译成特定目标语言的短语请求翻译(除非使用`force`选项)。另外，你可以告诉`auto_translator`到`ignore`任何不需要翻译的字符串。

## 复杂 ARB 字符串

使用 Google Cloud Translate 翻译字符串常量文件相对简单，但是当您开始使用 ARB 格式的高级功能时，就变得困难多了。ARB 字符串能够使用占位符变量，其他 ARB 字符串作为变量，以及条件表达式，所以它们可以变得相当复杂。让我们看一个例子:

```
"userSelection": "{choice, select, 0{$name chose $otherString} 1{$name chose $someOtherString} other{$name chose $yetAnotherString}}",
"@userSelection": {
  "placeholders": {
    "choice": {
      "type": "int"
    },
    "name": {
      "type": "String",
  }
}
```

这里，我们接受一个整数和一个字符串输入，并基于该整数计算一个条件表达式，以输出一个包含字符串输入和 ARB 文件中其他地方定义的字符串的字符串值。

随着字符串变得如此复杂，要对给定的短语进行正确的上下文翻译就变得非常困难。幸运的是，`auto_translator`专为解决这一问题而设计，同时最大限度地减少云翻译配额的使用。

# 考虑因素和未来工作

如前所述，这是一个个人使用的项目。我决定使用谷歌云翻译，因为我以前用过它，我觉得它在翻译相当准确方面做得很好。我已经考虑过扩展这个包来集成其他翻译服务，所以我正在收集应该包含的服务的建议。

如果你有兴趣看看一个用`auto_translator`翻译的成熟应用，看看 [Lights:一个记忆游戏](https://onelink.to/lights-game)。这是我的一个有趣的兼职项目，扩展了经典的记忆游戏*西蒙说*。

一如既往的感谢您的阅读！请关注我，了解未来关于 Flutter、软件开发、IT 管理以及更多内容的文章！