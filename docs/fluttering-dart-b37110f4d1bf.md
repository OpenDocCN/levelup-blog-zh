# 飞舞的飞镖:功能

> 原文：<https://levelup.gitconnected.com/fluttering-dart-b37110f4d1bf>

## [飘动的飞镖](https://levelup.gitconnected.com/fluttering-dart/home)

## 如何在 Dart 中编写、使用和滥用函数

![](img/552f768c8669a0423ce6470521a34ed2.png)

[timJ](https://unsplash.com/@the_roaming_platypus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/function?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

[Flutter](https://flutter.dev) 项目可以使用特定平台和跨平台代码。后者是用 [Dart](https://dart.dev) 写的，而且，构建 Flutter apps，需要一些 Dart 的基础知识。

在本系列的第一部分中，我们介绍了 Dart [**内置数据类型**](https://medium.com/@constanting/fluttering-dart-9a3e74b0d9c5) 。

在第二部分，我们将发现函数。

可以使用[](https://dartpad.dev)**对一些代码示例进行试验和测试。**

## **功能**

**一个**函数**(在一个对象的上下文中也可能被称为**方法**)是一个算法的子集，它在逻辑上是分离的并且是可重用的。它可以不返回任何东西( **void** )或者返回一个[内置数据类型](https://medium.com/@constanting/fluttering-dart-9a3e74b0d9c5)或者一个自定义数据类型。它也可以没有参数或有任意数量的参数。**

**在 Dart 这种真正面向对象的编程语言中，函数也是对象，它们的类型是，你猜对了， **Function** 。**

**这意味着:我们可以将函数赋给变量，或者将它们作为参数传递给其他函数。**

**名为 **toggle** 的简单函数示例如下所示:**

```
bool toggle(bool value) {
  // returns the opposite 
  return !value;
}
```

**我们可以省略类型，函数也一样工作(尽管建议使用类型注释):**

```
toggle(value) {
  // also returns the opposite
  return !value;
}
```

**对于只包含一个表达式的函数，我们可以使用一个简写语法(也称为**箭头语法**):**

```
toggle(value) => !value;
```

**函数可以有两种类型的参数:**必需的**和**可选的**。必需的参数在第一行，后面是可选的参数。**

## **可选参数**

**这些可以是****命名的**或**位置的**:****

*   ******命名为**参数****

****调用函数时，我们可以使用`*paramName*: *value*`指定命名参数。例如:****

```
**toggleSound(to: true, notifyUser:false);**
```

****定义函数时，使用`{*param1*, *param2*, ...}`指定命名参数:****

```
**void toggleSound({bool to, bool notifyUser}) {
  // code that (optionally) toggles the sound
  // and (optionally) notifies the user
}**
```

******默认情况下，命名参数是可选的。为了使它们具有强制性，我们必须用`@required`来注释它们(这在 DartPad 中不起作用):******

```
**import 'package:meta/meta.dart';void toggleSound({**@required** bool to, bool notifyUser}) {
  // code that toggles the sound
  // and (optionally) notifies the user
}**
```

*   ******位置**参数****

****如果我们在`[]`中包装一组函数参数，我们将它们标记为可选的位置参数。上面的例子变成了:****

```
**void toggleSound(bool to, **[**bool notifyUser**]**) {
  // code that toggles the sound
  // and (optionally) notifies the user
}**
```

****我们可以将编译时常量设置为命名参数或位置参数的默认值。如果没有提供值，那么参数将为空。****

****假设我们想将上面例子中的 **notifyUser** 的默认值设置为 **false** 。这将看起来像:****

```
**void toggleSound({bool to, bool notifyUser**=false**}) {
  // code that (optionally) toggles the sound
  // and (by default) doesn't notify the user
}**
```

****我们应该注意，只有可选参数可以有默认值。****

## ****主()****

****我们构建的每个应用程序都必须有一个入口点，这个角色由强制的顶级`main()`函数来担当。该函数返回`void`，并有一个可选的`List<String>`参数作为参数，如下所示:****

```
**// a web app
void **main**() {
  // web app code goes here
}**
```

****或者****

```
**// a command line app
void **main**(List<String> arguments) {
  print(arguments);
}**
```

## ****一等品****

****如开头所述，我们可以将函数作为参数传递给其他函数，也可以将它们赋给变量:****

```
**Function **_toggleSound** = (SoundSource source, [bool to=false, bool notifyUser=false]) {
  // code that toggles the sound of a SoundSource object
  // by default mutes it without notification
}List<SoundSource> soundSources = <SoundSource>[source1, source2, source3];// we're passing the _toggleSound Function variable as
// parameter to the forEach method of the soundSources List object
soundSources.forEach(_toggleSound);**
```

## ****匿名函数****

****尽管我们将要创建的大多数函数都是并且应该被命名的，但是我们可以选择创建无名函数。这些函数被称为**匿名**、**λ**或**闭包**函数。****

****在前面的代码示例中，我们已经定义了这样的函数，并将其赋给了函数变量 **_toggleSound** 。****

## ****词法范围和闭包****

****Dart 是一种词汇作用域语言，这意味着变量的作用域是静态确定的，只是由代码的布局决定。我们可以“沿着花括号向外”来查看变量是否在范围内。****

****在下面的**游戏**示例中，**敌人**字符串变量对于所有嵌套函数都是可用的，或者在作用域内，除非我们决定通过声明一个同名的变量来覆盖该值(新变量将只在该作用域级别上可用)。我们在第一级函数**中做了这个。作用域结束后，该变量将不再存在，并且上层声明的变量将再次可用。******

```
**void main() {
  String enemy = '';
  void **game**() {
    void **init**() {
      enemy = 'boss';
    }
    void **firstLevel**() {
      String enemy = 'miniboss';
      print(enemy);
    }
    void **endGame**() {
      print(enemy);
    }
    init();
    firstLevel();
    endGame();
  }
  game();
}**
```

******闭包**是可以访问其作用域内变量的函数对象，即使是在它们的初始作用域之外使用。****

****函数可以封闭周围作用域中定义的变量。在下面的例子中，**customize regement**捕获变量 **greeting** 。无论返回的函数走到哪里，它都会记住**问候**。****

```
**Function **customiseGreeting**(String greeting) {
  return (String name) => '$greeting, $name,';
}void main() {
  var goodMorning = customiseGreeting('Good morning');
  var goodEvening = customiseGreeting('Good evening'); print(goodMorning('Monica'));
  print(goodEvening('Monica'));
}**
```

## ****Typedefs****

****在 Dart 中，函数是对象(Function 类型的实例)。实际类型实际上是其签名的结果:参数类型+返回类型。****

****当函数用作参数或返回值时，重要的是实际的函数类型。Dart 中的 **typedef** 允许我们创建一个函数类型的别名。类型别名可以像其他类型一样使用。****

```
****typedef** Processor<T> = T Function(T value);
**typedef** Printer<T> = void Function(T value);Function customiseGreeting(String greeting) {
  return (String name) => '$greeting, $name,';
}void **greet**<T>(List<T> list, Processor<T> processor, Printer<T> printer) {
  list.forEach((item) {
    printer(processor(item));
  });
}void main() {
  Function goodMorning = customiseGreeting('Good morning'); 
  greet(['Monica', 'cats', 'Collin'], goodMorning, print);
}**
```

****在上面的例子中，我们有 **greet** 函数，它获取一个名字列表并问候列表中的每个人。它使用处理器和打印机打印。处理器是我们在前面的例子中使用的函数**customize regering**。打印机是实际的**打印**功能。****

## ****例外****

****我们经常在其他函数内部调用函数，有时，这些函数会失败。****

****Dart 有一个类似于 Java 的异常机制，不同之处在于 Dart 中的所有异常都是未检查的(函数不会声明它们可能抛出的异常)。这意味着我们可以跳过捕捉异常。这种自由会损害应用程序的整体性能和可靠性，健壮的应用程序应该能够正确处理故障。****

****为了**报告失败**我们应该使用`throw`关键字。虽然所有非空对象都可以被**抛出** n，但是建议我们只抛出**错误**和**异常**类型的对象。****

****一个**错误**对象代表了我们代码中的一个错误。错误是不应该被捕获的，通常它们会导致程序终止。****

****一个**异常**对象代表代码中一个不成功的事件转折。与错误相比，它们应该被捕捉和处理。它们通常携带有用的信息。我们应该创建自定义数据类型，从异常扩展到封装有用的数据。****

****抛出异常后，我们可以**捕捉**并阻止它传播。主要目标是处理它(我们不应该只是为了好玩而捕捉异常)。****

****使用`try`、`on`和`catch`关键字捕获异常。`catch`在我们想要访问异常对象和堆栈跟踪时使用，而`on`在我们不关心这些时使用。****

****要重新抛出一个异常，我们应该使用`rethrow`关键字。当我们想要部分地处理一个异常(不是一个好的实践)或者需要它在堆栈中更高的位置时，这可以派上用场。****

****最后，我们有`finally`关键字，它允许我们在最后运行代码，不管我们在运行过程中遇到了什么异常。****

****将所有这些应用到前面的例子中，并确保我们引起了一个异常，我们会得到:****

```
**void main() {
  Function goodMorning;
  **try** {
    goodMorning('test');
  } **on** NoSuchMethodError **catch**(e) {
    print('our attempt to greet failed: ${e.runtimeType}'); 
    goodMorning = customiseGreeting('Good morning'); 
  } **finally** {
    greet(['Monica', 'cats', 'Collin'], goodMorning, print);
  }
}**
```

****Dart 函数灵活且非常强大。****

****你可以看看这个使用顶级函数的好例子:****

****[](https://medium.com/@constanting/your-explosive-energy-aaab2e9d94c4) [## 你的爆发力

### 发现它使用颤振或飞镖

medium.com](https://medium.com/@constanting/your-explosive-energy-aaab2e9d94c4) 

我们刚刚讨论了函数的基础知识。要掌握所有相关的东西，需要一些时间和练习。

在 [**颤动飞镖**](https://medium.com/tag/fluttering-dart/archive) 系列的下一部分中，我们将深入研究[操作符](https://medium.com/@constanting/fluttering-dart-ee493f4b0440)，这是构建健壮颤动应用程序所需的另一个飞镖基础。

[](https://medium.com/@constanting/fluttering-dart-ee493f4b0440) [## 飞舞的飞镖:操作符

### Dart 的操作员…一个接一个

medium.com](https://medium.com/@constanting/fluttering-dart-ee493f4b0440) 

就这些！****