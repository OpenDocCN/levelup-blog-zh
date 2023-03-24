# Javascript 中的怪癖

> 原文：<https://levelup.gitconnected.com/comparison-in-javascript-25e7569f7417>

## 语言中的一些怪癖，以及如何理解它们

![](img/bbbab6fc99628470add15052bb51d73c.png)

在过去的一周里，我一直在学习 Javascript，并且遇到了许多在开始时似乎没有意义的事情，但是一旦你理解了 Javascript 在幕后是如何工作的，以后就明白了。我在这里列出了其中的一些，以及我自己对正在发生的事情的解释，以帮助你更好地理解。我们将讨论使用`==`和`===`的宽松与严格比较。

啊！所以，这就是 Javascript 的工作方式…

# 比较数值

```
let a = '2';
let b = 1;console.log(a > b); // this prints true
```

从上面的例子中，我们可以看到我们正在比较两个不同数据类型的变量，一个字符串“2”和一个数字 1。但是，JS 仍然能够计算出 2 大于 1，并将结果返回为 *true* 。这是因为在比较不同类型的值时，JS 会将值转换为数字，然后进行比较。在上面的例子中，字符串“2”首先被转换为数字 2，然后与数字 1 进行比较。这导致语句返回*真值*。

# 比较布尔值

```
console.log(true == 1); // this prints trueconsole.log(false == 0); // this prints true
```

这里，*真*等于 1，*假*等于 0，在它们各自的数字转换中。一个很好的经验法则是记住所有真值都转换为数字 1，所有假值都转换为数字 0。

现在让我们来看看上面两个例子的一个有趣的结果。考虑下面的代码

```
let a = 0;
let b = "0";console.log(Boolean(a) == Boolean(b)); // this prints false
console.log(a == b); // but this prints true
```

布尔(a) =布尔(0)，这等同于*假*，因为 0 是一个假值。Boolean(b) = Boolean("0 ")，这等同于 *true* ，因为任何非空字符串都是真值。

因此，(Boolean(a) == Boolean(b)返回*假*。

然而，a == b 返回 *true* ，因为 b 的“0”值被转换为数字 0，然后与 a 的 0 值进行比较

# 严格等式问题

比较几个值时使用`==`有一个问题。

```
console.log(false == 0); // this prints true
console.log(false == ''); // this prints true
```

例如，`==`运算符无法区分*假*和 0，因为它们都是假值，并且在数字转换中等于 0。同样适用于*假*和空串。

上面的难题可以通过使用三重等于(`===`)运算符来解决。三重等于和二重等于运算符之间的区别在于，前者在比较之前不进行任何隐式类型转换。换句话说，

```
console.log(false == 0); // this prints true
console.log(false === 0); // this prints false
```

在上面的例子中，第二条语句将 *false* 直接与 0 进行比较。因此，语句的结果打印为*假*。

当使用`===`操作符时，不同数据类型的值之间的任何比较默认返回 false。这同样适用于`!==`。

# 比较 null 和 undefined

在 Javascript 中， *null* 和 *undefined* 有一种怪异的关系。我猜这是由于 Javascript 在早期的构建方式。然而，让我指出一些可能让初学者感到困惑的差异。考虑下面的代码

```
console.log(null === undefined); // this prints false
console.log(null == undefined); // this prints true
```

*null* 和 *undefined* 在 Javascript 中是不同的数据类型，因此第一条语句输出 false。但是，我们可以看到第二条语句打印的是 *true* 。根据我们在本文前面讨论的，当使用`==`操作符比较两个值时，Javascript 首先尝试将值转换成它们的数字表示。*空*变为 0*未定义*变为 *NaN* 。虽然 0 不等于 *NaN* ，但是我们发现`null == undefined`仍然返回为 true。这是一个特殊的规则(或者也许是错误？)允许在 *null* 和 *undefined* 之间存在这样的关系。

然而，这只适用于`==`操作员。当比较 null 和 undefined 时，所有其他运算符都返回 false。

```
console.log(null > undefined); // this prints false
console.log(null < undefined); // this prints false
console.log(null >= undefined); // this prints false
console.log(null <= undefined); // this prints false
```

# 10 > 9 可由< “9”

When comparing numbers in Javascript, the logic differs on whether we are comparing their *数字*或*字符串*表示。以*号*为例，逻辑和现实生活中差不多。

```
10 > 9; // this returns true;
"10" > "9"; // this returns false;
```

然而，当使用*字符串*版本时，我们注意到了一些不同。“10”不大于“9”。这样做的原因是，当 Javascript 比较字符串时，它会将它们转换为 ASCII 表示，并比较它们的值。您可以使用 *charCodeAt()* 函数查看“10”和“9”的 ASCII 码。

```
"10".charCodeAt(0); // this returns 49
"9".charCodeAt(0); // this returns 57
```

由于 ASCII 码“10”是 49，小于 ASCII 码“9”的 57，因此它被认为是较小的值。

附言:我会在这篇文章中更新我遇到的其他怪癖。到那时，快乐的编码！