# Flutter: Dart 不可变对象和值

> 原文：<https://levelup.gitconnected.com/flutter-dart-immutable-objects-and-values-5e321c4c654e>

![](img/0e078701c90299c9dc27ee1a0da6a262.png)

开始开发 Flutter apps 的时候，我也是人生第一次遇到 Dart 语言。我非常惊讶地注意到，Flutter 团队选择了一种开箱即用的不支持不变性的语言。我仍然认为这很奇怪，特别是如果你将它与深深植根于值类型的 Swift UI 相比较。

让我们探索我们的替代方案。

## 天真的不变性

首先，让我们看看 Dart(当前版本是 2.8)提供了哪些现成的东西。

*   `[@immutable](https://api.flutter.dev/flutter/meta/immutable-constant.html)` [标注](https://api.flutter.dev/flutter/meta/immutable-constant.html)说`Person`(及其子类)里面的每个字段都必须是`final`。否则，Dart 编译器会抛出一个警告，但**不会**出错。
*   `[final](https://dart.dev/guides/language/language-tour#final-and-const)` [关键字](https://dart.dev/guides/language/language-tour#final-and-const)表示您不能在属性初始化后为其赋值。否则，Dart 编译器将发出错误。
*   `[UnmodifiableListView](https://api.dart.dev/stable/2.8.4/dart-collection/UnmodifiableListView-class.html)`是一个`List`包装器，禁止修改(例如:添加或删除项目)。它公开了像`add()`或`addAll()`这样的方法，但是它们在运行时抛出异常。

这很好，但是它完全没有满足编译时的安全需求，并且你没有任何你期望从[数据类](https://kotlinlang.org/docs/reference/data-classes.html)中得到的方法(比如相等、散列和克隆)。

## 源生成

谷歌自己提出了一个帮助:它被称为`[built_value](https://github.com/google/built_value.dart)`。您定义一个类描述，然后源生成器在一个成对的文件中创建缺少的部分。

同样的想法被另一个 Dart 包`[freezed](https://github.com/rrousselGit/freezed)`借用，它使用更现代的语法来实现类似的结果。通过在`pubspec.yaml`文件中插入一个依赖项来安装它:

我使用的是`any`版本，因为这个包没有公开一个可能随时间变化的功能，所以保持最新版本是可以的。

然后，您可以打开一个终端窗口，并且可以执行:

`build_runner`会观察文件系统，每次保存定义时都会生成正确的代码。

## 基本用法

让我们把`Person`转换成`freezed`:

*   第一行对`build_runner`说配对文件是`person.freezed.dart`。此名称无法更改。
*   `@freezed`注释告诉我们，下面的数据类声明必须由库合成。
*   工厂初始值设定项还指定了由该值类型声明的属性。
*   请注意`freezed`已经准备好管理即将发布的 Dart 2.9 中引入的可空类型。此时，它只在运行时检查空值。在这种情况下，只有`jobTitle`属性是可空的。
*   如果您需要为非必需的属性提供默认值，您必须使用`@Default`注释。

你免费得到了什么？

*   `toString`被覆盖以提供要打印的对象的漂亮描述。
*   `==`运算符逐个属性地比较对象。

## 复印

`copyWith`方法返回具有更新属性的新实例:

如果要复制嵌套层次结构，可以使用简单的链式语法:

所以如果`p1`有一个最好的朋友，那么`p2`将包含一个带有新的最好的朋友的父亲名字和姓氏的`p1`的副本。

## 自定义方法和属性

如果需要添加自定义方法，就不能再使用简化的 mixin 语法了:

还可以添加自定义 getters，甚至用`@late`注释，相当于 Swift 的`lazy`或者 Kotlin 的`lateinit`:

``late``将很快成为官方 Dart 关键词。

## 密封类

在 Swift 和 Kotlin 世界中，强大的枚举很受欢迎，我非常想念它们。你可以这样写美女:

显然你不能用`switch`执行模式匹配，但是有像`when`或`maybeWhen`这样的方法，`freezed`会为你合成:

## 不可变集合

集合的编译时安全性如何？`freezed`没有缓解这个问题，而`built_value`有一个名为`build_collection`的孪生包，它公开了不可变列表、字典和集合。这些符合`Iterable`，所以它们与官方 Dart 系列非常兼容。

但是我想再大胆一点用`[kt_dart](https://github.com/passsy/kt.dart)` [包](https://github.com/passsy/kt.dart)，一个包含不可变集合和其他好处的`kotlin-stdlib`端口，像列表的深度比较和没有外来名字的`map` / `filter` / `reduce`。通过在`pubspec.yaml`中插入一个新的依赖项来安装它:

您可以修改`Person`数据类只是改变类型:

默认情况下，`friends`列表现在是不可变的。列表的创建比以前更容易:

例如，如果您想创建一个方法来生成一个新的不可变的人和一个新朋友，这很简单:

只是注意:由于`KtList`不符合`Iterable`，所以不能直接使用*进行*循环，而应该在之前取迭代器:

## VS 代码集成

由于样板代码可能会很乏味，很难记住，也许通过选择*首选项:在 VS 代码启动中配置用户片段*，然后选择 *Dart* 语言来设置一些用户片段会很方便。现在，您可以复制并粘贴以下代码片段:

您也可以从*浏览器*选项卡中排除生成的文件。为此，打开*设置*并搜索*文件:排除*首选项。然后，您可以将此模式添加到列表中:`**/*.freezed.dart`。

## JSON 序列化和反序列化

`freezed`通过设计与`json_serializable`包集成，但使用`kt_dart`会有一些不明显的挑战:我在后续的[中深入讨论了这个话题。](https://medium.com/@muccy/flutter-dart-immutable-objects-json-serialization-and-deserialization-dff917b1af2)