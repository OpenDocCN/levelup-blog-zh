# 3 种 Dart 参数及其正确使用

> 原文：<https://levelup.gitconnected.com/3-types-of-dart-parameters-and-using-them-correctly-61d4204d53a5>

## 许多现代语言现在为定义参数提供了多种选择。Dart 有三种口味。让我们来看看如何利用好它们。

首先，强大的力量意味着巨大的责任。

![](img/44a11b0f334c6a4f666bad1cd8f09828.png)

由[凯文·Ku](https://unsplash.com/es/@ikukevk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

您的实体如何相互通信由您的参数决定。从某种意义上说，这就是你所创造的世界中事物的交流方式。因此，了解你正在使用的语言的能力是至关重要的。

Dart 提供了三种不同的方法来声明方法的参数。

*   所需的位置参数
*   命名参数
*   可选位置参数

让我们首先用例子来描述它们之间的差异。

## 目录

∘ [必需位置参数](#a468)
∘ [命名参数](#69be)
∘ [可选位置参数](#0b7f)
∘ [必需和默认值](#d36a)
∘ [断言和解释参数](#d148)
∘ [总结和最终提示](#474c)
∘ [引用](#d379)

## 所需的位置参数

你已经知道这个了。每种语言都有这种形式的参数。正则语法就像定义类型和名称一样简单。

```
class SettingsIcon extends StatelessWidget {
  final VoidCallback callback;
  final String tooltip;

  const SettingsIcon(this.callback, this.tooltip);

...
```

上面的例子让我们了解了什么是必需的位置参数。`callback`和`tooltip`是构造函数的参数，应该按照声明的顺序给出。

```
SettingsIcon(() {}, "Settings");
```

*   参数的名字只在局部对函数重要，调用者只需要知道它们的位置。
*   本例中的第一个位置用于`callback`，第二个位置用于`tooltip`。
*   为了避免编译器错误，调用函数时必须提供所有参数。

## 命名参数

您可以只使用所需的位置参数，这样也很好，但是 Flutter 小部件有许多参数，因为它们配置正在构建的小部件。

当有太多参数时，阅读会变得很困难，尤其是对于调用者来说，事情会变得非常混乱。在 Java 中，我们使用 Builder 设计模式来解决这个问题；在 Dart 中，我们使用命名参数。

```
class SettingsIcon extends StatelessWidget {
  final VoidCallback callback;
  final double? iconSize;
  final Color? iconColor;
  final String? tooltip;

  const SettingsIcon(this.callback,
      {Key? key,
      this.iconSize,
      this.iconColor,
      this.tooltip})
      : super(key: key);

...
```

我们用花括号`{}`来定义命名参数。出于示例的原因，我使用了可空类型以及命名的和必需的位置参数的混合。明白我在上面例子中的意思了吧。

```
SettingsIcon(() {},
   iconColor: Colors.amber, 
   tooltip: "Settings");
```

你可以在上面看到它是如何使用的。如您所见，我必须首先给出所需的位置参数，然后给出命名参数。由于`iconSize`是可选的，我也不需要，所以跳过了。

*   命名参数可以以任何顺序调用，因为它们不是位置性的。
*   它们是可选的，除非它们被标记为`required`。
*   调用方可以使用名为。所以用名字来引用。
*   这个名字真的很重要，因为它暴露在外面。

Flutter 文本小部件的构造函数是另一个很好的例子。

```
 const Text(
    String this.data, {
    Key? key,
    this.style,
    this.strutStyle,
    this.textAlign,
    this.textDirection,
    this.locale,
    this.softWrap,
    this.overflow,
    this.textScaleFactor,
    this.maxLines,
    this.semanticsLabel,
    this.textWidthBasis,
    this.textHeightBehavior,
  }) : assert(
         data != null,
         'A non-null String must be provided to a Text widget.',
       ),
       textSpan = null,
       super(key: key);
```

文本小部件有 14 个参数，但只有一个是必需的位置参数。如您所见，`data`是必需的，并且被断言为非空。这是文本小部件工作的基础。所有其他参数都与小部件的工作方式有关，因此它们都是可选的。

## 可选位置参数

这并不常见，但有时我们希望一些位置参数是可选的。说实话，这个我用的不多。

```
class SettingsIcon extends StatelessWidget {
  final VoidCallback callback;
  final double? iconSize;
  final Color? iconColor;
  final String? tooltip;

  const SettingsIcon(this.callback,
      [Key? key, this.iconSize, this.iconColor, this.tooltip])
      : super(key: key);

... 
```

让我们看看我们的例子。我已经将所有命名的参数更改为可选的位置参数。乍一看，人们认为他们可以在调用这个方法时跳过一个参数，但故事是不同的。

```
SettingsIcon(() {}, 16, Colors.amber, "Settings");
```

例如，我不能像上面那样调用`SettingsIcon`的构造函数。因为`Key? key`是第一个可选的位置参数。

```
SettingsIcon(() {}, GlobalKey() , 16, Colors.amber);
```

但我也可以这样称呼它:给出了`key`但没有给出`tooltip`参数。我们只能从末尾跳过参数，不能从开头或中间跳过。为了让你能更好地理解这一点，让我将其与 Java 进行比较。超载？谁记得那个？

```
public class SettingsIcon {

    VoidCallback callback;
    Key key;
    double iconSize;
    Color iconColor;
    String tooltip;

    SettingsIcon(VoidCallback callback) {
        this.callback = callback;
    }

    SettingsIcon(VoidCallback callback, Key key) {
        this.callback = callback;
        this.key = key;
    }

    SettingsIcon(VoidCallback callback, Key key, double iconSize) {
        this.callback = callback;
        this.key = key;
        this.iconSize = iconSize;
    }

    SettingsIcon(VoidCallback callback, Key key, double iconSize, Color iconColor) {
        this.callback = callback;
        this.key = key;
        this.iconSize = iconSize;
        this.iconColor = iconColor;
    }

    SettingsIcon(VoidCallback callback, Key key, double iconSize, Color iconColor, String tooltip) {
        this.callback = callback;
        this.key = key;
        this.iconSize = iconSize;
        this.iconColor = iconColor;
        this.tooltip = tooltip;
    }
}
```

相信我，我在日常工作中使用 Java，我们的类有很多方法重载。在 Dart 中，使用 Dart 中的可选位置参数，只需一个构造函数就可以编写上面显示的代码。

*   我们不能混合可选的位置参数和命名参数。换句话说，必需的位置参数可以后跟命名参数或可选的位置参数。
*   你不能在可选的位置参数前放一个必需的关键字，因为既必需又可选是没有意义的。

## 必需值和默认值

如前所述，命名参数是可选的。然而，我们可以通过在它们前面加上关键字`required`来使它们成为必需的。如果没有提供值，编译器会报错。

对于命名的和可选的位置参数，我们也可以指定一个默认值。因此，即使调用者没有为这些参数提供值，也会为它们提供一个值。

```
class SettingsIcon extends StatelessWidget {
  final VoidCallback callback;
  final double? iconSize;
  final Color? iconColor;
  final String? tooltip;

  const SettingsIcon(this.callback,
      {Key? key,
      required this.iconSize,
      this.iconColor = Colors.white,
      this.tooltip})
      : super(key: key);

...
```

在前面的例子中，您可以看到现在需要`iconSize`，默认情况下`iconColor`被设置为白色。因此，对此构造函数最简单的调用如下:

```
SettingsIcon(() {}, iconSize: 16);
```

*   如果某个参数不是使对象工作的基础，而是配置所必需的，则使用必需的命名参数。例如，对于建筑商来说，这是一个很好的用例。举个例子，考虑`ListView.builder()`。因为它只是一个带有小部件的滚动视图，所以不需要位置参数。然而，如果没有任何小部件，列表视图是没有用的，所以需要一个项目构建器。
*   使用默认值，而不是使您的类型可为空。

## 断言和解释参数

这似乎是一个断言参数的好地方。这对构造函数参数很重要。因为构造函数是你的类的第一种文档形式，在构造函数的右边有断言可以揭示很多关于你的代码。请检查下面的代码。

```
class SettingsIcon extends StatelessWidget {
  final VoidCallback callback;
  final double? iconSize;
  final Color? iconColor;
  final String? tooltip;

  const SettingsIcon(this.callback,
      {Key? key,
      required this.iconSize,
      this.iconColor = Colors.white,
      this.tooltip})
      : assert(iconSize == null || iconSize >= 60,
            "Icon size can't be bigger than 60"),
        super(key: key);

...
```

这里显示了我们的构造函数的最终形式。乍一看，以下是我的解释:

*   这个对象的主要参数是`callback`。没有它就无法运作。每个人都假设第一个参数是回调。
*   `iconSize`参数不是必需的，但是没有它，对象将起作用但是没有意义。此外，如果它大于 60，这个类的功能将被中断。
*   图标的颜色是可选的，通常是白色。
*   `toolTip`是完全可选的，如果不提供，将不显示任何工具提示。

## 总结和最终提示

以下是每种类型参数的快速摘要:

**所需位置参数:**

*   调用时必须提供所有参数
*   由他们的位置引用
*   用于基本依赖关系。

**命名参数:**

*   用花括号定义，`{}`。
*   可选，除非标记为`required`。
*   按他们的名字引用
*   调用方法时，参数的顺序无关紧要。

**可选位置参数:**

*   括号内定义，`[]`。
*   不能跳过中间的参数，因为可选性从最后一个参数开始。
*   使用它们而不是重载。

**一般来说:**

*   使用 null-safety 的默认值，同时仍然使它们对对象的默认状态有意义。
*   当命名参数对于正在构建的对象在逻辑上很重要时，使用 required 关键字。
*   在构造函数的右边断言你的参数，逻辑上限制给定的参数。
*   请记住，您的参数定义了代码文档的第一种形式，并向该类的客户传达了一些信息。让它有意义！

感谢阅读。

## 参考

*   [Dart 语言指南](https://dart.dev/guides/language/language-tour#parameters)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)