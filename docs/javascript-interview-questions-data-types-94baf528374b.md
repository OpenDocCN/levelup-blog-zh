# JavaScript 面试问题—数据类型

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-data-types-94baf528374b>

![](img/d1e53b97040850e5ae4afa4109ddc56e.png)

照片由[优 X 创投](https://unsplash.com/@youxventures?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

在这篇文章中，我们将看看一些数据类型的问题，包括一些在面试中经常被问到的技巧性问题。

# JavaScript 中有哪些 falsy 值？

在 JavaScript 中，falsy 值如下

*   `''`
*   0
*   `null`
*   `undefined`
*   `false`
*   `NaN`

当它们被转换成布尔值时，就变成了`false`。

# 如何检查一个值是否为 falsy？

使用`!!`操作符或`Boolean`功能检查它们是否错误。

如果一个值是 falsy，那么两者都将返回`false`。

例如:

```
!!0
```

会返回`false`。

```
Boolean(0)
```

也会返回`false`。

# 什么是真理价值观？

真值是上面列出的假值之外的任何值。

# 什么是包装对象？

包装对象是从返回原始值的构造函数中创建的对象，但如果是数字或字符串，则具有类型`'object'`。符号和 BigInt 都有自己的类型。

在 JavaScript 中，有`Number`、`String`构造函数、`Symbol`和`BigInt`工厂函数。

原始值被临时转换为包装对象以调用方法。

例如，我们可以写:

```
'foo'.toUpperCase()
```

然后我们得到`'FOO'`。它的工作原理是将`'foo'`文字转换成一个对象，然后对其调用`toUpperCase()`方法。

我们还可以为 numbers 和 BigInt 创建包装对象，如下所示:

```
new Number(1)
BigInt(1)
```

直接使用`Number`或`String`构造函数并不太有用，所以我们应该避免使用。它们给了我们对象的类型，但与它们的原始对应物具有相同的内容。

如果我们用如下的`new`操作符创建了`Number`和`String`包装对象:

```
const numObj = new Number(1);
const strObj = new String('foo');
```

我们可以使用`valueOf`方法返回这些包装对象的原始值版本，如下所示:

```
numObj.valueOf();
strObj.valueOf();
```

# 隐性强制和显性强制有什么区别？

隐式强制意味着 JavaScript 解释器将一段数据从一种类型转换为另一种类型，而无需显式调用方法。

例如，以下表达式被隐式转换为字符串:

```
console.log(1 + '2');
```

然后我们得到:

```
'12'
```

因为 1 被隐式转换为`'1'`而返回。

另一个例子是:

```
true + false
```

我们返回 1，因为`true`被转换为 1，而`false`被转换为 0，它们被加在一起。

下面的例子:

```
3 * '2'
```

返回 6，因为`'2'`被转换为`2`，然后它们相乘。

JavaScript 强制规则的完整列表在[这里](https://delapouite.com/ramblings/javascript-coercion-rules.html)。

显式转换是通过调用方法或使用运算符转换类型来完成的。

我们可以看到代码中的强制。

例如:

```
+'1' + +'2'
```

得到 3，因为我们有`'1'`和`'2'`都被一元`+`操作符转换成数字。

我们也可以用函数做同样的事情。例如，我们可以使用`parseInt`函数来代替一元`+`运算符。

我们可以将上面的表达式改写为:

```
parseInt('1') + parseInt('2')
```

然后我们得到同样的结果。

![](img/e3af970af109e62c53870fa8e5cdcda3.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 0 || 1 返回什么？

因为`0`是 falsy，它将触发第二个操作数运行，这给我们 1。因此，它应该返回 1。

# 0 && 1 返回什么？

因为`0`是假的，它将停止运行，因为我们已经短路评估。因此，我们返回 0。

# false == '0 '返回什么？

`false`为 falsy，`'0'`转换为 0，为 falsy，所以它们在`==`的比较标准上是相同的。因此，我们应该返回`true`。

# 4 < 5 < 6 返回什么？

在 JavaScript 中，比较是从左到右进行的。所以首先会对`4 < 5`进行评估，这就给了我们`true`。然后`true < 6`求值，转换成`1 < 6`也是`true`。

因此，我们得到了返回的`true`。

# 6 > 5 > 4 返回什么？

在 JavaScript 中，比较是从左到右进行的。所以会先评估`6 > 5`。这给了我们`true`。然后`true > 4`求值，转换成`1 > 4`，也就是`false`。

因此，表达式返回`false`。

# typeof undefined == typeof null 返回什么？

`typeof undefined`返回`'undefined'`，`typeof null`返回`'object'`。

由于`'undefined'`确实等于`'object'`，所以表达式应该是`false`。

# typeof typeof 10 返回什么？

JavaScript 将`typeof typeof 10`评估为`typeof (typeof 10)`。因此，这会返回`typeof 'number'`，而`typeof 'number'`会返回`'string'`，因为`'number'`是一个字符串。

# 结论

类型强制经常让人们在 JavaScript 面试中。因此，我们应该了解一些基本的 JavaScript 类型强制规则，并准备一些棘手的问题。