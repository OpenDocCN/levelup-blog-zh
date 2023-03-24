# 探索科特林的双重平等

> 原文：<https://levelup.gitconnected.com/double-equality-in-kotlin-f99392cba0e4>

你可能知道我们可能不应该使用`==`来检查浮点等式，因为浮点表示并不精确。那么检查等式的最佳方法是什么呢？答案是，视情况而定。

在我们继续之前，让我们先检查一下这是否确实是一个问题！让我们运行这段代码:

```
val a = .1 + .1
val b = .2
println("===Let's see the values")
println("$a and $b")
println("${a - b} and ${0.0}")
```

```
===output===
===Let's see the values
0.2 and 0.2
0.0 and 0.0
```

哦哇！他们是平等的，没有问题，对不对？怎么样:

```
val a = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val b = .8

println("===Let's see the values")
println("$a and $b")
println("${a - b} and ${0.0}")
```

```
===output===
===Let's see the values
0.7999999999999999 and 0.8
-1.1102230246251565E-16 and 0.0
```

啊。好了，这很简单，让我们谷歌一下如何做等式检查！

# 有些人比其他人更平等

如果您在 Stackoverflow 上搜索，您可能会找到一堆使用`==`的解决方案/替代方案:

*   使用`Double.compareTo`
*   使用`Double.compare`
*   使用`Double.doubleToLongBits`

这些都不是*完全*正确的，对于前 2 个，你可以从阅读[文档](https://docs.oracle.com/javase/8/docs/api/java/lang/Double.html)中看到:`compareTo`和`compare`都返回“如果 d1 在数值上等于 d2，则为 0”，实际上使其与`==`相同。

让我们再次看看代码(为了完整起见，添加了`.equals()`):

```
val a = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val b = .8

println("===Let's see the values")
println("$a and $b")
println("${a - b} and ${0.0}")

println("===Let's compare")
println("==: ${a == b}")
println("equals: ${a.equals(b)}")
println("Double.compareTo: ${a.compareTo(b) == 0}")
println("Double.compare: ${java.lang.Double.compare(a, b) == 0}")
println("Double.doubleToLongBits : ${java.lang.Double.doubleToLongBits(a) == java.lang.Double.doubleToLongBits(b)}")
```

```
===output===
===Let's see the values
0.7999999999999999 and 0.8
-1.1102230246251565E-16 and 0.0
===Let's compare
==: false
equals: false
Double.compareTo: false
Double.compare: false
Double.doubleToLongBits : false
```

![](img/a475a33ff69a0b90b56bc0689892f370.png)

## 边注

浮点等式是棘手的，不是因为不精确表示本身，而是因为对不精确表示“相加”所做的算术。相反，如果我们从两个相同的浮点值开始，不进行算术运算，或者如果完成的算术运算是相同的，那么使用`==`进行相等比较是可行的。

例如，如果我们从`0.8`开始:

```
val a = .8
val b = .8
<...same comparison code as above>
```

```
===output===
===Let's see the values
0.8 and 0.8
0.0 and 0.0
===Let's compare
==: true
equals: true
Double.compareTo: true
Double.compare: true
Double.doubleToLongBits : true
```

或者如果我们有相同的算术:

```
val a = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val b = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
<...same comparison code as above>
```

```
===output===
===Let's see the values
0.7999999999999999 and 0.7999999999999999
0.0 and 0.0
===Let's compare
==: true
equals: true
Double.compareTo: true
Double.compare: true
Double.doubleToLongBits : true
```

# 如何检查等式？

推荐的方法是使用一个阈值，它是一个足够小的值，如果`a`和`b`只相差这个量，我们可以安全而实际地说它们相等。`threshold`又称`epsilon`或`delta`。通常的公式是:

```
abs(a - b) < delta  // pseudocode
```

我们稍后将讨论如何选择`delta`。假设我们选择了`0.000001`，在 Kotlin 中，我们可以在`Double`上编写一个扩展函数并使用它:

```
fun Double.equalsDelta(other: Double) = abs(this - other) < 0.000001

val a = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val b = .8

println("===Let's see the values")
println("$a and $b")
println("${a - b} and ${0.0}")
println("===Let's compare with delta")
println("a.equalsDelta(b): ${a.equalsDelta(b)}")
```

```
===output===
===Let's see the values
0.7999999999999999 and 0.8
-1.1102230246251565E-16 and 0.0
===Let's compare with delta
a.equalsDelta(b): true
```

IBM 的另一个可能的公式是这样的:

```
abs(a/b - 1) < delta
```

这比`(a — b)`方法更稳健，因为取两个数字的比值“抵消”了它们的比例对 delta 的影响。(也就是说，如果 a 和 b 真的很小，那么与`(a — b)`相比，delta 最终可能会非常大。在科特林:

```
fun Double.equalsDelta(other: Double) = abs(this/other - 1) < 0.000001
```

## 达美怎么选？

如果我们知道操作数的小数位数，我们可以设置一个足够小的**固定增量**。

如果我们不知道比例，那么一种方法是使用**相对增量**。相对增量是根据操作数缩放的固定增量。这实际上非常类似于`abs(a/b — 1)`方法，它考虑了操作数的规模。一个简单的实现可以是:

```
val delta = max(abs(a), abs(b)) * fixedDelta  // pseudocode

fun Double.equalsDelta(other: Double) = abs(this - other) < 0.000001 * max(abs(this), abs(other))
```

另一种方法是从`ulp` ( [**最小精度单位**](https://en.wikipedia.org/wiki/Unit_in_the_last_place) )计算增量。简而言之，`ulp`是一个给定数字和它相邻的更大数字之间的差距。Kotlin(通过 Java)有一个方法让[返回一个数字的 ulp](https://docs.oracle.com/javase/8/docs/api/java/lang/Math.html#ulp-double-):`Math.ulp(double)`。我们可以根据操作数的`ulp`设置增量。例如，基于此[回答](https://stackoverflow.com/a/29900125/4212710) ( `steps`指我们感到舒适的`ulp`“步骤”的数量):

```
val delta = max(Math.ulp(a), Math.ulp(b)) * steps // pseudocode

fun Double.equalsDelta(other: Double) = abs(this - other) < max(Math.ulp(this), Math.ulp(other)) * 2
```

# 其他对比呢？

像`<`和`>`这样的比较一般不是问题，因为通常在这些情况下我们并不在乎数字完全相同，只在乎一个比另一个少/多。

```
val x = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val y = .7999
println("$x and $y")
println("${x > y}")
```

```
===output===
0.7999999999999999 and 0.7999
true
```

然而`≤`和`≥`有点棘手。

```
val x = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val y = .8
println("$x and $y")
println("${x >= y}")
```

```
===output===
0.7999999999999999 and 0.8
false
```

这可能是也可能不是一个问题。经验法则是，如果`y`作为 x 的“极限”,那么`≥`的含义类似于`>`,所以这不是一个真正的问题。然而，如果我们经常遇到`x`和`y`确实相等的情况(并且我们需要知道它们相等)，那么推荐使用`(a > b) || (a == b)` 而不是`a ≥ b`，使用上述方法之一来实现`==`。

```
fun Double.ge(other: Double) = this > other || this.equalsDeltaFixed(other)

val x = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val y = .8
println("$x and $y")
println(">=: ${x >= y}")
println("ge(): ${x.ge(y)}")
```

```
===output===
0.7999999999999999 and 0.8
>=: false
ge(): true
```

## 边注

我们可以使用 Kotlin 的[中缀符号](https://kotlinlang.org/docs/reference/functions.html#infix-notation)来创建流畅的方法:

```
infix fun Double.eq(other: Double) = abs(this - other) < 0.000001
infix fun Double.ge(other: Double) = this > other || this.eq(other)

val x = .1 + .1 + .1 + .1 + .1 + .1 + .1 + .1
val y = .8
println("$x and $y")
println("==: ${x == y}")
println("eq: ${x eq y}")
println(">=: ${x >= y}")
println("ge: ${x ge y}")
```

```
===output===
0.7999999999999999 and 0.8
==: false
eq: true
>=: false
ge: true
```

# 有其他选择吗？

如果我们不太依赖于`Double`，当涉及到精确的数量时，使用`BigDecimal`通常是更好的方法。如果可能，在进行算术运算之前，使用`BigDecimal`和/或从`Double`转换。(*当然，* `BigDecimal` *也有自己的告诫*)

```
val _1 = BigDecimal.valueOf(.1)
val a = _1.add(_1).add(_1).add(_1).add(_1).add(_1).add(_1).add(_1)
val b = BigDecimal.valueOf(.8)
println("$_1 and $a and $b")
println("compareTo: ${a.compareTo(b) == 0}")
```

```
===output===
0.1 and 0.8 and 0.8
compareTo: true
```

# 参考

*   [https://randomascii . WordPress . com/2012/02/25/comparing-floating-point-numbers-2012-edition/](https://randomascii.wordpress.com/2012/02/25/comparing-floating-point-numbers-2012-edition/)
*   【jessesquires.com/blog/floating-point-swift-ulp-and-epsilon】

## 你可能喜欢的其他故事

*   [用 Kotlin 写流畅的代码](/write-fluent-code-in-kotlin-133647f2a869)
*   [软件评估是一种双边关系](/opinion-software-estimates-are-a-two-sided-relationship-6247108ace41)
*   [使用 Java 可空性注释来简化到 Kotlin 的转换](https://medium.com/dont-code-me-on-that/use-java-nullability-annotations-to-facilitate-conversion-to-kotlin-7196f0cee9e9)