# 飞舞的飞镖:流动

> 原文：<https://levelup.gitconnected.com/fluttering-dart-the-flow-7be2080763ad>

## [飘动的飞镖](https://levelup.gitconnected.com/fluttering-dart/home)

## 所有关于控制流的语句

![](img/0beb971ea5a5858e5d4176778b89f2a3.png)

由[布雷克·理查德·凡尔登](https://unsplash.com/@blakeverdoorn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/flow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[Flutter](https://flutter.dev) 项目可以使用特定平台和跨平台代码。后者是用 [Dart](https://dart.dev) 写的，而且，构建 Flutter apps，需要一些 Dart 的基础知识。

在本系列的前几部分中，我们介绍了 Dart[](https://medium.com/@constanting/fluttering-dart-9a3e74b0d9c5)****[**函数**](https://medium.com/@constanting/fluttering-dart-b37110f4d1bf) 和[**运算符**](https://medium.com/@constanting/fluttering-dart-ee493f4b0440) 。****

****在这一部分，我们将发现如何编写和使用控制流语句。****

****可以使用[](https://dartpad.dev)**试一试一些代码示例。******

## ******控制流语句******

******如果您来自另一种命令式编程语言，您将熟悉 Dart 中的控制流语句。******

******有条件语句: **if** — **else** 和 **switch** — **case** ，迭代语句: **for** 、 **for** — **in** 、 **forEach** 、 **while** 、**do**—**while**loop。还有一些“帮助者”语句:**中断**、**继续**、**断言**。不要忘记，不久前当我们讨论[函数](https://medium.com/@constanting/fluttering-dart-b37110f4d1bf)时，我们也讨论过 **try** — **catch** 和 **throw** 语句。******

## ****条件语句****

****这些用于基于满足/匹配某些布尔条件来决定动作(更具体地说是代码的执行)。****

****Dart 支持带有可选 **else** 元素的 **if** 语句:****

```
****if**(myCat.meowing) {
  feed(myCat);
} **else if**(!myCat.sleepy) {
  pet(myCat);
} **else** {
  // don't disturb the cat
  takePicturesOf(myCat);
}**
```

****如果你还记得[条件操作符](https://medium.com/@constanting/fluttering-dart-ee493f4b0440)，它几乎是一个 **if** — **else** 语句(`condition ? expr1 : expr2`)的速记符号。****

****上面例子中的一个衬垫是:****

```
**myCat.meowing **?** feed(myCat) **: (** !myCat.sleepy **?** pet(myCat) **:** takePicturesOf(myCat) **)**;**
```

****我们不应该用这种方式编码，因为这样可读性更差。****

****条件**必须**只使用布尔值( **bool** 赋值返回 **bool** 的类型化对象或表达式)。****

******Switch** 语句可以比较整数、字符串或编译时常量，使用`==`。被比较的对象必须都是同一个类，没有[覆盖`==`操作符的](https://medium.com/@constanting/fluttering-dart-ee493f4b0440)。[列举类型](https://medium.com/@constanting/fluttering-dart-9a3e74b0d9c5)与**开关**一起使用时，非常适合:****

```
**enum CatMoods = {meowing, sleepy, playful, curious, angry};**switch**(myCat.mood) {
  **case** meowing:
    feed(myCat);
    break;
  **catAngerManagement**:  
  **case** playful:
  **case** curious:
    pet(myCat);
    break;
  **case** angry:
    **continue catAngerManagement
  case** sleepy:
    continue;
  **default**:
    takePictures(myCat);
}**
```

****在 **switch** 语句中使用 **enum** s 迫使你处理所有可能的情况。****

****非空的 **case** 子句通常以 **break** 语句结束。也可以以**继续**、**抛出**或**返回**结束。不结束这样的子句会产生错误。****

****正如我们在上面的例子中看到的，Dart 也支持空 case 子句(`case playful:`)。还有一个有趣的**继续**到自定义标签(`catAngerManagement`)。****

****在 **case** 语句中声明的任何局部变量只在该子句的范围内可见。****

****请注意， **switch** 语句并不像我们预期的那样灵活，那是因为它们**是为有限的环境**设计的，比如在解释器或扫描仪中。****

## ****循环语句****

****循环允许我们多次运行一段代码。这样做，我们重用代码。****

****使用 **for** 循环可以运行指定的次数:****

```
**import 'dart:math';void weirdMeow() { var meow = StringBuffer('M'); var random = Random.secure();

  List<String> meows = <String>['m','e','o', 'w'];

  meows.forEach((String m) {
    int l = 1 + random.nextInt(5);
    for (int i = 0; i < l; i++) {
      meow.write(m);
    }
  });
  print('$meow!');
  // prints something like:
  // Mmmmmeeeeeoooooww!}**
```

****上面，我们已经定义了**古怪喵**函数，每次都会产生不同的**喵**。为此，我们使用一个随机生成器和两种风格的**用于**循环:**用于**和**用于每个**。****

****后者可用于一个**可迭代**对象。当我们不需要知道迭代计数器时，使用 **forEach()** 非常有用。****

******Iterable** 对象还支持第三种风格的**用于**循环，**用于**—中的**。上述 **forEach** 示例变成:******

```
// ...
for(String m in meows) {
  int l = 1 + random.nextInt(5);
  for(int i = 0; i < l; i++) {
    meow.write(m);
  }
}
// ...
```

**使用 **while** 循环也可以进行多次迭代。这有两种方式:**

*   **简单的 **while** 循环——在循环之前评估条件，并运行零到任意次数以满足条件**
*   **以及一个**执行** — **同时**循环——该循环评估循环后的条件，至少运行一次**

```
// simple while
while(isTooDark()) {
  bringUpTheLevelOfLightBy(10); 
}// do-while
do {
   bringUpTheLevelOfLightBy(10);
} while(isTooDark());
```

**简单的 **while** 只会在太暗的情况下提升亮度，相比之下 **do** — **while** 无论如何都会将亮度提升至少 10。**

****中断**和**继续**用于停止循环，或者分别跳到下一次循环迭代。**

```
<Cat>List cats = <Cat>[simba, micuta, klein, saki];for(Cat currentCat in cats) {
  if(!currentCat.isHungry) {
    **continue**;
  }
  feed(currentCat);
}
```

**在前面的例子中，我们将遍历我们的猫，只喂养饥饿的猫。其他的，我们跳过。**

**在开发过程中，我们可以使用以下语法的**断言**语句:**

```
assert(*condition*, *optionalMessage*);
```

**在我们的[飞镖](https://medium.com/tag/fluttering-dart/archive)旅程中，我们已经多次使用它来中断正常的执行，如果布尔条件为假的话。**

**如果表达式的值为真，断言成功，执行继续。如果为 false，断言将失败并出现异常。**

**Flutter 在调试模式下启用断言，而诸如 [**dart2js**](https://dart.dev/tools/dart2js) (用于构建 web 平台)等工具通过命令行标志`--enable-asserts`支持断言。**

**在生产代码中，断言被忽略，并且不计算`assert`的参数**

**在[**flying Dart**](https://medium.com/tag/fluttering-dart/archive)系列的下一部分，我们将深入研究 Dart 的[类、对象和其他面向对象编程](https://medium.com/@constanting/fluttering-dart-oop-8b92cd89a7f0)方面。**

**[](https://medium.com/@constanting/fluttering-dart-oop-8b92cd89a7f0) [## 飞舞的飞镖:噢

### 类、对象、接口等等

medium.com](https://medium.com/@constanting/fluttering-dart-oop-8b92cd89a7f0) 

就这些！**