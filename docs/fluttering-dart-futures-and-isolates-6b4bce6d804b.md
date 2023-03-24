# 飞舞的飞镖:未来与孤立

> 原文：<https://levelup.gitconnected.com/fluttering-dart-futures-and-isolates-6b4bce6d804b>

## [飘动的飞镖](https://levelup.gitconnected.com/fluttering-dart/home)

## 如何克服单线程的缺点

![](img/e90b42cc5e56ba053a0eb32070c345a7.png)

照片由 [Createria](https://unsplash.com/@createria?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/wind-turbine?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Dart 是单线程的。这意味着我们所有的应用程序代码都在同一个线程中运行。不幸的是，如果单线程运行非常耗时的操作，比如 HTTP 请求，我们的代码可能会阻塞单线程的执行。

## 期货

未来通过允许我们执行异步操作来解决这个问题。为了表示和处理这些操作的结果，我们得到了关键字`async`和`await`。

一个`Future<T>`对象在未来的某个时间提供一个 T 类型的值。

让我们继续使用我们在前面部分中使用的 Cat 类，并看看它们领域中的一个例子:

```
import 'dart:math';class Cat {
  DateTime birthday; Cat.baby() {
    birthday = DateTime.now();
  } **Future**<**String**> **meow**() **asyn**c {
    var random = Random.secure();
    **await** Future.delayed(Duration(seconds: random.nextInt(10)));
    return 'Meow!';
  }
}void main() async {
  Cat babyCat = Cat.baby();
  print('Baby cat says:');
  String babyCatMeow = await babyCat.meow();
  print('$babyCatMeow');
}
```

在上面的例子中，我们创建了一只小猫。如果你不知道，猫想做什么就做什么，想什么时候做就什么时候做。喵喵叫也不例外，我们的小猫会以 0-10 秒的间隔随机喵喵叫。

**猫**类的**喵**方法是**异步**同步的。在**等待** s 随机秒数(0–10)后，它在**未来**返回一个**字符串**。 **Future.delayed** 方法在给定的持续时间内停止单线程执行。注意，我们还必须使我们的**主异步**同步，并且**等待**的**喵**结果。

这种等待结果的异步方法机制适用于任何时候，因为计算/逻辑的原因，我们需要获得一些只能/应该在未来才可用的数据。

## 孤立者

我们身边越来越多的多核 CPU 设备上的单线程似乎有点低效。

开发人员利用设备所有处理能力的传统方式是使用并发运行的共享内存线程。这种方法既复杂又容易出错。

Dart 使用隔离来利用所有可用的处理能力。隔离允许真正的并行代码执行，从而提高性能和整体应用程序响应能力。

所有的 Dart 程序都从至少一个 Isolate 实例开始，这个实例就是 **main** **Isolate** ，我们所有的应用程序代码都在这里运行。

要让我们的代码并行执行，我们必须创建新的隔离实例。这些将与主隔离并行运行。隔离是完全独立的，有自己的内存堆，确保任何其他隔离都无法访问任何隔离的状态。

为了在隔离之间建立通信(发送/接收数据),我们需要交换消息。这些可以是原始值，如`null`、`num`、`bool`、`double`或`String`，也可以是简单对象，如`List<Cat>`。在隔离之间传递更复杂的对象，比如`Future`或`http.Response`，可能会导致错误。

**Isolate.spawn** 期望传递一个静态或顶级函数。

```
import 'dart:math';
import 'dart:isolate';class Cat {
  DateTime birthday; Cat.baby() {
    birthday = DateTime.now();
  } **static void meow(int s) async {**
    await Future.delayed(Duration(seconds: s));
    print('Meow!');
  }
}void main() async {
  var timeInterval = 10;
  var random = Random.secure();
  for(var i=0; i<100; i++) {
    **await Isolate.spawn(Cat.meow, random.nextInt(timeInterval));**
  }
  await Future.delayed(Duration(seconds: timeInterval+1));
}
```

我们修改了 Cat 类，将**喵**方法转化为静态(类)方法。我们还将其更改为接受 **s** ，这是一个表示猫必须喵喵叫的秒数的参数。这种方法将用于观察隔离物的工作情况。

在主隔离中，我们声明一个 10 秒的时间间隔和一个随机生成器。然后，我们在接下来的 10 秒钟内产生 100 个 Cat.meow 方法，每个方法都在一个专用的隔离区中运行。

我们还确保**主隔离**比它产生的其他隔离运行的时间长一点，否则我们只会看到产生的输出有 0 秒的延迟。

当编译成 JavaScript 时，孤立文件被转换成网络工作者(T21)。

请注意，这在 DartPad 中不起作用。您可以在您最喜欢的编辑器中的 Dart/Flutter 项目设置中测试它(我使用的是 [Visual Studio 代码](https://code.visualstudio.com)，尽管还有其他很棒的选项)。

[Flutter](https://flutter.dev) 项目既可以使用特定平台代码，也可以使用跨平台代码。后者是用 [Dart](https://dart.dev) 写的，而且，构建 Flutter apps，需要一些 Dart 的基础知识。

在本系列的前几部分中，我们介绍了 Dart [**内置数据类型**](/@constanting/fluttering-dart-9a3e74b0d9c5) **，** [**函数**](/@constanting/fluttering-dart-b37110f4d1bf) **，** [**运算符**](/@constanting/fluttering-dart-ee493f4b0440) **，** [**控制流语句**](/@constanting/fluttering-dart-the-flow-7be2080763ad) 和 [**面向对象编程**](https://medium.com/@constanting/fluttering-dart-oop-8b92cd89a7f0) **(类、对象等等)**

在这一部分中，我们发现了如何克服 Dart 的单线程缺点。

可以使用[](https://dartpad.dev)**试一试上面的一些代码示例。**

**在[**flying Dart**](https://medium.com/tag/fluttering-dart/archive)系列的下一部分，我们将深入探讨[库和包](https://medium.com/@constanting/fluttering-dart-libraries-and-packages-972edf864ff9)，它们对于构建、使用、重用和共享我们的代码至关重要。**

**[](https://medium.com/@constanting/fluttering-dart-libraries-and-packages-972edf864ff9) [## 飞舞的飞镖:库和包

### 库和包对于在 Flutter 和 Dart 中构建、使用、重用和共享组件是至关重要的…

medium.com](https://medium.com/@constanting/fluttering-dart-libraries-and-packages-972edf864ff9) 

就这些！**