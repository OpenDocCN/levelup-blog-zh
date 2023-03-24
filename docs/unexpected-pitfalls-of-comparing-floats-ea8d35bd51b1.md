# 比较浮点数的意外陷阱

> 原文：<https://levelup.gitconnected.com/unexpected-pitfalls-of-comparing-floats-ea8d35bd51b1>

有一次我检查代码，被问及在单元测试中互换使用 double 和 integers 值的不一致性。你应该坚持一种类型。这里没有使用整数的理由！”——评审员说。

![](img/4519110b63e5194ed1b8a567c7ea3b13.png)

这并不明显，但是当你使用浮点数据类型时，你可能会因为小数部分的内部表示而在比较数字时遇到特殊的错误。让我举个例子来说明。

```
let a = 0.5;
let b = 0.5;
console.log(a == b); // true
```

这个看起来不错。结果果然没错。然而，浮点的内部表示并不是代码中写的 *0.5* ，而是某处的 *0.5000000000034* 。换句话说，一个实际的数字接近于 0.5，在最后一位数附近有一些小的额外值。

使用如上例中的显式值初始化，您可能一开始没有捕捉到额外的数字。但是让我们把 0.5 改写成一系列的 0.01:

```
let a = 0.0;
for (let i = 0; i < 50; i++) {
  a += 0.01;
}
let b = 0.5;
console.log(a);
console.log(b);
console.log(a == b);
```

现在，如果您运行代码，输出将类似于:

```
0.5000000000000002
0.5
false
```

因此，该值不是预期的 *0.5* ，而是 *0.5000000000000002。所有编程语言都是如此，不仅仅是 JavaScript。*考虑下面的 Java 片段:

```
class T {
  public static void main(String[] argsc) {
    float a = 0.25f + 0.25f;
    float b = 0.5f;
    System.out.println(a == b); // true
  }
}
```

这将导致 true(至少在我的例子中)，但是如果 0.5 被表示为一个更长的小值序列:

```
class T {
  public static void main(String[] argsc) {
    float a = 0.0f;
    for (int i = 0; i < 50; i++) {
      a += 0.01;
    }
    float b = 0.5f;
    System.out.println(a == b);
  }
}
```

这将导致比较结果为*假*！因此，应该避免直接比较*浮点数*(*double*)的相等性，因为这容易出错。

最简单的解决方案是使用整数(长整型)的显式转换，并乘以 10 的幂:

```
class T {
  public static void main(String[] argsc) {
    float a = 0.0f;
    for (int i = 0; i < 50; i++) {
      a += 0.01;
    }
    float b = 0.5f;

    int timesA = (int) Math.round(a * 10);
    int timesB = (int) Math.round(b * 10);

    System.out.println(timesA == timesB);
  }
}
```

在这种情况下，结果会如预期的那样*为真*。还要注意，需要进行舍入，因为结果可能比目标值 *0.5* 大 0.5000000034 或小 0.499999934。

您不能**使用浮点包装类来比较这个值**。考虑以下内容:

```
class T {
  public static void main(String[] argsc) {
    float a = 0.0f;
    for (int i = 0; i < 50; i++) {
      a += 0.01;
    }
    float b = 0.5f;
    boolean wrapperCmp = Float.valueOf(a).equals(b);
    System.out.println(wrapperCmp);
  }
}
```

结果为*假*！

BigDecimal 类也是如此。

```
import java.math.BigDecimal;

class T {
  public static void main(String[] argsc) {
    float a = 0.0f;
    for (int i = 0; i < 50; i++) {
      a += 0.01;
    }
    float b = 0.5f;

    BigDecimal aBigDecimal = BigDecimal.valueOf(a);
    boolean wrapperCmp = aBigDecimal.equals(b);
    System.out.println(BigDecimal.valueOf(a));
    System.out.println(wrapperCmp);
  }
}
```

结果为*假:*

```
0.4999998211860657
false
```

另一种解决方案是应用某种 epsilum *e* 值:

```
if |a - b| < e then true
```

可能认为价值是相等的:

```
class T {
  public static void main(String[] argsc) {
    float a = 0.0f;
    for (int i = 0; i < 50; i++) {
      a += 0.01;
    }
    float b = 0.5f;

    float epsilum = 0.0001f;
    boolean epsilumCompare = Math.abs(a - b) < epsilum;

    System.out.println(b);
    System.out.println(a);
    System.out.println(epsilumCompare);
  }
}
```

上面的代码会导致:

```
0.5
0.49999982
true
```

在这种情况下，我们可以只保留*浮点*类型，而不使用*数学循环*和转换为*整数*。似乎我的评论家在他的评论中有一点。