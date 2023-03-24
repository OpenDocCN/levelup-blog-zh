# 更多我们可能还没用过的 JavaScript 特性

> 原文：<https://levelup.gitconnected.com/more-javascript-features-we-probably-havent-used-da32d500767>

![](img/9e03320361e30efa857f9a01755aaa29.png)

照片由[陈俊生](https://unsplash.com/@danielchenjs?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 有很多经常使用的特性。然而，也有一些功能可能是我们大多数人都没有用过的，因为它们中的一些可能对大多数人来说并不那么有用。

在本文中，我们将研究其中的一些特性以及它们可能的用例。

# 数组构造函数

`Array`构造函数允许我们创建一个数组，要么通过传入一个长度作为唯一的参数，要么传入多个参数，其中包含我们想要填充到数组中的项目。

例如，我们可以如下使用`Array`构造函数:

```
const arr = new Array(5);
```

在上面的代码中，我们将 5 作为唯一的参数传递给了`Array`构造函数。

然后我们得到一个有 5 个空槽的数组。因此，`arr`以此为值。

我们还可以向`Array`构造函数传递多个值。例如，我们可以这样写:

```
const arr = new Array(1, 2, 3);
```

我们得到`arr`就是`[1, 2, 3]`。

这可能是有用的。然而，我们需要键入的不仅仅是定义一个数组文字，所以我们也可以使用数组文字。

# 函数构造函数

`Function`构造函数让我们通过为参数和函数体传递字符串来创建函数。

我们传递给`Function`构造函数的第一个到第二个参数是参数，最后一个参数是函数体。

我们可以用它创建如下函数:

```
const add = new Function("a", "b", "return a + b")
```

在上面的代码中，我们传入了`'a'`和`'b'`来使`a`和`b`成为我们函数的参数。然后我们用`return a + b`作为我们函数的主体。

那么我们可以这样称呼它:

```
const sum = add(1, 2);
```

我们得到`sum`是我们期望的 3。

变量名是函数名，所以`add`是函数名。

然而，这并不是定义函数的一种非常方便的方式。此外，由于所有参数都是字符串，JavaScript 解释器很难进行任何优化，因为函数是动态创建的。

因此，以这种方式创建函数存在性能问题。

`Function`构造函数创建只在全局范围内运行的函数。

# 使用`length`属性截断数组内容

大多数人不知道的 JavaScript 数组的一个特性是,`length`属性可以被重新分配给不同的。它既是 getter 又是 setter。

当我们把它重新赋值给一个小于数组当前长度的值时，它会截断数组。

例如，如果我们有以下数组:

```
const arr = [1, 2, 3, 4, 5];
```

那么我们可以把它的长度减少到 3:

```
arr.length = 3;
```

然后我们得到`arr`是`[1, 2, 3]`。`arr`只是从它原来的长度 5 被截短为 3。

# 向阵列中添加空插槽

我们还可以通过将数组的长度设置为比原始长度更长来添加空槽。

例如，如果我们有与上一个示例相同的数组:

```
const arr = [1, 2, 3, 4, 5];
```

我们可以通过编写以下代码来增加长度:

```
const arr = [1, 2, 3, 4, 5];
arr.length = 10;
```

那么我们得到`arr`是:

```
[1, 2, 3, 4, 5, empty × 5]
```

现在我们得到了数组中的原始项加上 5 个空槽，因为我们将`arr`的`length`属性设置为 10。

![](img/81bc68fed20c7b859c771b850ff5f385.png)

Sebastian Herrmann 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 争论

`arguments`对象是一个类似数组的 iterable 对象，它返回函数的参数。

它只适用于传统功能。例如，我们可以从`arguments`对象获得如下参数:

```
function foo() {
  console.log(arguments);
}
```

那么当我们这样称呼它的时候:

```
foo(1, 2, 3)
```

我们会得到这样的结果:

```
Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

从控制台日志输出。因为`Symbol.iterator`被设置为迭代器函数，正如我们从控制台日志输出中看到的，所以它是可迭代的。所以我们可以使用 spread 操作符将它转换成一个数组，或者对它使用`for...of`循环。

然而，如果我们需要获取没有被赋值给参数的参数，rest 操作符是一个更好的选择。

# 结论

有一些有用的 JavaScript 特性。数组构造函数很有用，通过设置数组的`length`属性来改变数组的长度也很有用。

像`Function`构造函数或`arguments`对象这样的东西就没那么有用了。