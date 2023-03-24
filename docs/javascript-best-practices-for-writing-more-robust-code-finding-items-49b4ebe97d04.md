# 编写更健壮代码的 JavaScript 最佳实践——查找项目

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-finding-items-49b4ebe97d04>

![](img/b211b773ea1f259f4347f0e1aa86b34f.png)

照片由 [Jonatan Pie](https://unsplash.com/@r3dmax?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究更可靠的方法来过滤和检查数组中的项目。

# 使用 every 方法检查数组的所有条目中是否存在某些内容

JavaScript 数组实例的`every`方法让我们可以很容易地检查数组的所有条目中是否存在某种东西。

我们所要做的就是传入一个回调函数，其中包含我们想要在每个条目中检查的条件。

例如，如果我们想检查数组中的每个数字是否都是偶数，我们可以编写以下代码:

```
const arr = [2, 4, 6];
const allEven = arr.every(a => a % 2 === 0);
```

在上面的代码中，我们有一个全是偶数的`arr`数组。然后我们使用了有一个回调函数的`every`,该回调函数将数组条目`a`作为参数，我们返回`a % 2 === 0`来检查数组中的所有数字是否都是偶数。

因为它们都是偶数，`allEven`是`true`，因为如果数组实例中的每个条目都满足回调中给定的条件，则`every`返回`true`，否则返回`false`。

这比编写我们自己的循环更可靠，因为它更短并且经过了很好的测试。因此，虫子的机会就少了。

回调也可以接受其他参数。被循环的索引是第二个参数，如果是第三个，则是数组本身。它们都是可选的。但是，如果我们需要，那么我们可以包含它们。

例如，我们可以将示例重写如下:

```
const arr = [2, 4, 6];
const allEven = arr.every((a, index, array) => array[index] % 2 === 0);
```

如果我们需要在回调函数中引用`this`，那么`every`方法还接受一个对象，我们可以将该对象设置为回调函数中`this`的值。

例如，我们可以编写以下代码来设置`this`的值，并在回调中使用它:

```
const arr = [1, 2, 3];
const allEven = arr.every(function(a){
 return a >= this.min;
}, { min: 2 });
```

在上面的代码中，我们通过将`{ min: 2 }`作为第二个参数传入来设置回调中`this`的值。

然后我们将函数切换到传统函数，这样我们就可以在回调中引用`this`值。在回调中，我们检查了每个数组条目是否大于或等于`this.min`，即 2。

我们不必担心如何设置循环，何时结束循环以及循环中的其他事情。

# 使用 filter 方法创建包含已过滤条目的数组

数组实例的`filter`方法返回一个数组，该数组的条目在包含返回数组的回调中具有给定的条件。

例如，我们可以如下使用它:

```
const arr = [1, 2, 3];
const result = arr.filter(a => a > 1);
```

在上面的代码中，我们用回调函数`a => a > 1`在数组实例上调用了`filter`，以返回一个新数组，其中包含了`arr`中大于 1 的所有条目。

所以我们应该得到`[2, 3]`作为`result`的值。

像`every`一样，回调也可以带一个可选的`index`和`array`参数。`index`是第二个参数，`array`是第三个参数。

所以我们也可以写下面的代码:

```
const arr = [1, 2, 3];
const result = arr.filter((a, index, array) => array[index] > 1);
```

在上面的代码中，我们使用了`array`和`index`来引用条目，而不是使用第一个参数`a`来引用每个条目。

因此，我们应该得到和以前一样的结果。

这比编写我们的循环要好，因为它附带了 JavaScript 的标准库，所以它已经过很好的测试，可以消除任何错误。

此外，它用于过滤的算法对于生产应用来说保证足够快，因为它在标准库中。

最后，它也比我们自己写循环来做同样的事情要短。

![](img/261c21f354fa729751440bd275d6ed75.png)

安迪·奇尔顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

array 实例的`every`方法对于检查每个条目是否有我们要找的东西非常有用。

要用 array 实例中满足给定条件的条目创建一个新数组，我们可以使用 array 实例的`filter`方法来简化。