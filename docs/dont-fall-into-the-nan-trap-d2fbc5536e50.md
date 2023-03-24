# 不要落入南的陷阱

> 原文：<https://levelup.gitconnected.com/dont-fall-into-the-nan-trap-d2fbc5536e50>

## 那天我的单元测试验证了一千个错误的计算

![](img/d677e97b328ce06d14828fc1230eb769.png)

照片由 [Miron Cristina](https://unsplash.com/@crismiron?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我很尴尬。我以为我做的一切都是对的。我写了一些代码来执行一些复杂的浮点计算。像一个优秀的开发人员应该做的那样，我编写了单元测试，将结果与期望值进行比较。一切都很顺利。

至少我是这么认为的。几天后，我开始将据称有效的 JavaScript 代码移植到 Python。Python 版本在尝试访问不存在的属性时崩溃。

我被难倒了，因为我在从 JavaScript 到 Python 的翻译中找不到任何错误。两种算法做的是完全相同的事情。当 Python 中存在如此明显的问题时，我精心制作的单元测试怎么能说 JavaScript 一切正常呢？

凭直觉，我使用调试器来调试 JavaScript 代码，并观察它是如何工作的。这时候我的下巴掉了下来。

## JavaScript 中的 NaN 陷阱

我将用更简单的代码来说明发生了什么。这是为一个名为`Polar`的函数设计的单元测试。第 5 行的`if`语句应该验证将向量(3，4)转换为极坐标会产生非常接近 5 的距离。

通过从计算的距离中减去期望的距离，我们得到一个叫做`diff`的误差值，它的绝对值永远不应该超过万亿分之一(`1.0e-12`)。当我运行测试时，它通过了:

```
$ **node oops.js** 
PASS
```

这有什么大不了的？一切似乎都很好，对吗？如果我告诉你这整个测试是假的，因为财产名称`distance`是错误的。看看`Polar`功能实际上是做什么的:

这个函数返回的对象甚至不包含一个`distance`属性！单元测试应该检查`radius`属性。`Polar`函数计算并存储在`radius`中的是什么荒谬的值并不重要，因为单元测试从不看那个值。

那么为什么这个拙劣的测试认为答案是正确的呢？让我们进入 Node.js 命令行，看看发生了什么。

```
$ **node**
> **const polar = {};**
undefined
> **const diff = polar.distance - 5.0;**
undefined
> **diff**
NaN
```

最后一行是关键。在 JavaScript 中，从`undefined`中减去一个数字会得到`NaN`，这个值意味着“不是一个数字”。

`NaN`有些怪癖:

```
> **diff > 3.0**
false
> **diff < 3.0**
false
> **diff === 3.0**
false
```

正如你在上面看到的，如果你问`NaN`是大于、小于还是等于某个数值，答案总是否定的(`false`)。

因此，当我询问`diff`的值是否过大时，单元测试没有进入`if`语句，单元测试“通过”

## 不仅仅是 JavaScript 的问题

我知道你们有些人在想什么。*哈哈，这就是你使用没有静态类型分析的语言的结果。你说得有道理。这个特别的问题，轻率地访问一个不存在的属性，不会发生在具有编译时类型检查的语言中。在 C 或 Java 中，编译器会注意到这个未定义的符号，并且会失败并显示一条错误消息。*

但也不要太自鸣得意。`NaN`能用严格类型的语言咬你一口。考虑下面的 C 代码:

下面是我编译并运行这个 C 程序时发生的情况。

```
$ **gcc -lm -o bogus bogus.c**
$ **./bogus**
diff = nan
PASS
```

哦不，我们的带有静态类型检查的编译语言并没有给我们太多帮助，不是吗？我们有另一个单元测试，它在应该失败的时候通过了。

## 南的成因

在上面的 C 程序中，`NaN`是试图求一个负数的平方根的结果。以下是计算产生`NaN`的一些其他方式:

*   将 0 除以 0。
*   0 的 0 次方。比如在 C: `pow(0, 0)`中。
*   将范围[-1，+1]之外的值传递给反三角函数，如`acos`。这被称为*域错误*。
*   任何包含`NaN`的计算都会产生`NaN`。比如`x`是`NaN`，那么`sqrt(x*x + 1)`也会是`NaN`。

## 避开陷阱

既然我们已经意识到了`NaN`的问题，那么我们如何防范与之相关的 bug 呢？

首先，人们可能想用一个简单的比较来检查一个数值是否是`NaN`。例如，回到 JavaScript，我们可以问一个变量是否等于`NaN`。大概是这样的:

```
> **let x = 0/0;**
undefined
> **x**
NaN
> **x === NaN**
false
```

哎呀。那没用。这是因为无论何时你问`NaN`是否等于任何东西，甚至是另一个`NaN`，答案都是否定的。一些程序员通过将一个变量与*本身*进行比较，利用这种行为来检测`NaN`:

```
> **x === x**
false
> **x !== x**
true
> **if (x !== x) console.log('Not a number!')**
Not a number!
```

这看起来很奇怪，但很有效。问一个变量是否不等于它本身，如果答案是肯定的，那它就不是一个数。

但是有更好的方法。如果你关心这个可怜的懒汉，他以后必须维护代码，你可以写一些不那么神秘和怪异的东西。大多数支持浮点计算的编程语言都提供了检查`NaN`值的函数。例如，在 JavaScript 中，您可以像这样使用函数`isNaN`:

```
> **isNaN(Math.sqrt(-1))**
true
```

这里有一张关于如何在一些流行的编程语言中检查某个值`x`是否为`NaN`的备忘单:

*   JavaScript: `isNaN(x)`
*   C: `isnan(x)`(必须`#include <math.h>`)
*   C#: `double.IsNaN(x)`
*   Python: `math.isnan(x)`

顺便说一句，在 Python 中很难意外地创建一个`NaN`值，因为它往往会引发异常:

```
>>> **0.0 / 0.0**
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: float division by zero
>>> **import math**
>>> **math.sqrt(-3.0)**
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: math domain error
```

然而，Python 在其标准数学包中包含了一个显式的`NaN`值:

```
>>> **math.nan == math.nan**
False
>>> **math.sqrt(math.nan + 7)**
nan
```

## 我如何改变我的单元测试

被虚假的单元测试愚弄的经历真的让我思考。在我的[天文引擎](https://github.com/cosinekitty/astronomy)开源项目中，我有许多单元测试将误差与阈值进行比较。我在所有四种受支持的编程语言中都这样做:C、C#、Python 和 JavaScript。每一次阈值检查都让我面临出现`NaN`问题的风险。

我不想再次被烧伤。我从 JavaScript 版本中的一个简单函数`v`开始。如果它的参数是`NaN`，或者甚至不是一个数字，它抛出一个异常。该异常会立即导致单元测试失败。否则，它将返回值。

在我进行风险阈值检查的几乎每一个案例中，我都使用`Math.abs`来获取计算值和期望值之间的差值的绝对值。我围绕`Math.abs`写了一个包装函数，如下所示:

我改变了我所有的阈值测试，用`abs`代替`Math.abs`。我为其他数学函数编写了类似的包装器，以验证参数和/或返回值永远不会是`NaN`。

## 正负不定式

另外值得一提的是，浮点计算会产生两种不同的无穷大值:`+Infinity`和`-Infinity`。这些特殊的无限值表现得比`NaN`更直观。例如，在 Node.js 解释器中:

```
> **let z = -1 / 0;**
undefined
> **z**
-Infinity
> **z < 0**
true
> **z > 0**
false
> **Math.abs(z)**
Infinity
> **Math.abs(z - 5) > 1.0e-12**
true
```

从上一个表达式中可以看出，无限的结果肯定会导致我的单元测试失败。因此，我没有必要像处理`NaN`一样，将`Infinity`作为特例处理。但是，您至少应该考虑一下，如果浮点代码中的某个值变成无穷大，会发生什么。

## 最后的想法

除了将`NaN`作为一个特定的问题来处理之外，这个经历告诉我，我需要更加怀疑我的单元测试。使用调试器并在第一次逐步通过单元测试并观察发生了什么是一个好主意。

要有耐心和好奇心。看变量的值。确保一切都有意义。不要等一个 bug 出现；第一次运行新代码时使用调试器。

这看起来需要额外的时间，但是不管你的代码是否正确，避免虚假的单元测试是值得的！

## 资源

1.  [南在维基百科上的文章](https://en.wikipedia.org/wiki/NaN)
2.  维基百科上的 IEEE 754 文章:关于浮点数在大多数现代计算机上表示方式的更多细节。
3.  [每个计算机科学家都应该知道的浮点知识](https://www.itu.dk/~sestoft/bachelor/IEEE754_article.pdf):这是一篇优秀的文章，涵盖了高级浮点计算的细微差别。