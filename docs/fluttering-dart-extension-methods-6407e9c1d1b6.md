# 飞舞的飞镖:扩展方法

> 原文：<https://levelup.gitconnected.com/fluttering-dart-extension-methods-6407e9c1d1b6>

## [飘动的飞镖](https://levelup.gitconnected.com/fluttering-dart/home)

## 向任何类型添加功能，即使是您无法控制的类型

![](img/d6b558272c06e10b9de28143f7c68b69.png)

照片由[哈尔·盖特伍德](https://unsplash.com/@halgatewood?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/science-ficition?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

颤振项目既可以使用特定平台代码，也可以使用跨平台代码。后者是用 Dart 写的，构建 Flutter apps，需要一些 Dart 的基础知识。

可以使用 [**DartPad**](https://dartpad.dev/) 对代码示例进行试验和测试。

# 扩展方法

自 2019 年 12 月的 11ᵗʰ以来，2.7 版本的 Dart 语言获得了很好的功能支持:扩展方法。

这使我们能够向现有的库 API 添加我们不能修改或不想破坏的额外功能。

我们可以用新的方法、getter/setter 甚至操作符来扩展。扩展有自己的名字，这有助于解决 API 冲突。

扩展将在接收者的静态类型上工作(无论是推断的还是声明的)。所以它适用于使用`var`关键字或任何静态类型声明声明的变量，而不适用于`dynamic`类型的变量。

## 履行

首先，您必须确保使用扩展方法特性的 Flutter 项目使用正确的 Dart SDK 版本。该版本必须≥ 2.7，即 Dart 获得此功能支持的版本。你可以在你的`pubspec.yaml`里面检查一下:

```
environment:sdk: ">=**2.7.0** <3.0.0"
```

如果您有较旧的项目，则最低要求可能是较旧的 SDK 版本，因此，您将收到以下错误:

```
This requires the 'extension-methods' experiment to be enabled.
Try enabling this experiment by adding it to the command line when compiling and running.
```

如果您遇到了上述问题，请确保在更新了`pubspec.yaml`文件后重新启动您的 IDE。

实际实现的语法是:

```
extension <extension_name> on <type> {
  (<member_definition>)*
} 
```

## 使用

对于一些扩展，你可以把所有的扩展写在一个文件中，例如在`extensions.dart`中。如果扩展比较复杂或者你想保持一切整洁，你可以在你的项目的 **lib** 中创建 **extensions** 文件夹，并遵循命名约定。

例如，对于 DateTime，您可以将文件命名为`date_time_extension.dart`。请注意，文件的名称不必反映其中任何扩展名的名称(就像类一样)。

要使用我们实现的扩展方法，我们只需将扩展文件导入到我们想要使用它的地方。

之后，我们可以访问我们添加的方法、getter/setter 和操作符。

扩展成员将与常规成员一起出现在 IDE 的代码完成中。

## 例子

在我的应用程序中，我经常使用 **DateTime** s，有时我需要找出自用户操作发生以来经过了多少时间单位。为了在没有扩展方法的情况下为 DateTime **userIn** object 实现这一点，我将执行以下操作:

```
Duration **toNow** = DateTime.now().toUtc().difference(**userIn**.toUtc());
print('${**toNow**.inMinutes} minutes since last user login');
```

`extensions.dart`里面的扩展方法会是这样的:

```
**extension** DateTimeExtended **on** DateTime {
  Duration get **toNow** {
    return DateTime.now().toUtc().difference(**this**.toUtc());
  } 
}
```

在日期时间中，同一**用户的上述示例为:**

```
import 'package:fluttering_dart/extensions.dart';// after importing the extensionsprint('${**userIn**.**toNow**.inMinutes} minutes since last user login');
```

请注意，您不必声明一个 **DateTimeExtended** 变量。扩展方法 **toNow** getter 将可用于我们拥有的 **DateTime** 类型变量。

此外，我通过使用 **toUtc()** 方法获取**用户在**和 **DateTime.now()** 中的 UTC 值，确保时间处于“同一水平”。这样我们就可以避免任何可能遇到的时区问题。

我觉得这个功能非常好，非常有用。这是很方便的，特别是当你不能访问一个库，并且你想扩展它的开箱即用的功能时。

如果您有兴趣了解更多关于 Dart 编程语言的信息，您可以在这里找到:

[](https://levelup.gitconnected.com/fluttering-dart/home) [## 飞舞的飞镖

### 探索 Dart 的基本原理，并揭示强大的编程语言的技巧和诀窍，使颤振的生活。

levelup.gitconnected.com](https://levelup.gitconnected.com/fluttering-dart/home) 

就这些！