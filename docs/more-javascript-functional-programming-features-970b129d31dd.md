# 更多 Javascript 函数式编程特性

> 原文：<https://levelup.gitconnected.com/more-javascript-functional-programming-features-970b129d31dd>

![](img/3b22be4521d55575a2c158a35dc9f4cf.png)

Photo by [卡晨](https://unsplash.com/@awmleer?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

JavaScript 是一种简单易学的编程语言。它还使用了许多函数式编程特性，使我们的生活更加轻松。

在本文中，我们将研究 JavaScript 的一些函数式编程特性，包括函子和纯函数。

# 函子

函子是可以被映射的东西。如果一个实体的条目可以映射到新的值，那么它就是一个函子。

在 JavaScript 中，任何我们可以调用`map`方法的东西都是函子。这是 JavaScript 中的一个数组。

例如，对于一个数组，我们可以如下调用`map`:

```
const arr = [1, 2, 3].map(a => a * 2);
```

在上面的代码中，我们传入了一个回调函数，该函数返回一个数组，该数组的每个值都是原始数组的两倍。因此，数组是一个函子。

除了数组，我们还可以通过使用 spread 操作符将类似数组的可迭代对象转换为数组来创建数组。

例如，如果我们有一个生成器函数:

```
const generator = function*() {
  yield 1;
  yield 2;
  yield 3;
}
```

然后我们可以将它转换成一个数组，并对转换后的数组调用`map`，如下所示:

```
const arr = [...generator()].map(a => a * 2);
```

在上面的代码中，我们使用了 spread 操作符将`generator`返回的迭代器转换成一个数组。

然后我们得到和第一个例子一样的结果。

类似数组的可迭代对象的其他例子包括`arguments`对象、DOM 节点列表、集合、映射和`Files`对象。

对于不可迭代的类似数组的对象。也就是说，拥有数字键和`length`属性的对象，我们可以使用`Array.from`将其转换为数组。

我们可以如下调用`Array.from`:

```
const obj = {
  0: 1,
  1: 2,
  2: 3,
  length: 3
}
const arr = Array.from(obj).map(a => a * 2);
```

在上面的代码中，我们将`obj`传递给`Array.from`以将其转换为数组，然后像在前面的示例中一样对其调用`map`。

# 纯函数

纯函数是对于给定的一组输入，总是返回相同输出的函数。

而且，它们不会产生任何副作用。它们很有用，因为它们易于理解和测试，因为它们只是处理输入并返回输出。

它们也不会产生任何副作用，所以我们不会有函数本身以外的事情发生，这些事情是由函数引起的。

纯函数的一个例子包括:

```
const add = (x, y) => x + y;
```

`add`函数只取 2 个数并返回相加的结果，所以它是一个纯函数。它不修改函数之外的任何东西，所以不会产生副作用。

非纯函数的一个例子是:

```
let foo = 1;
const impureAdd = (a, b) => {
  foo = 3;
  return a + b;
};
```

`impureAdd`函数通过将函数外的`foo`变量重新赋值为不同的值而产生副作用。

因此，`impureAdd`不是一个纯函数。

![](img/f1c65ca8a284e1cd6fd3f99ce6e5b00e.png)

照片由[黄阮](https://unsplash.com/@hoangx?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 副作用

副作用是除了返回值之外，在被调用函数之外可以观察到的任何操作。

例如，它们包括如下操作:

*   在函数外改变变量值
*   记录到控制台
*   在屏幕上画画
*   将文件写入磁盘
*   运行外部流程
*   调用其他产生副作用的函数
*   …以及任何在函数之外做事情的东西

使用函数式编程，副作用被最小化，因此函数更容易阅读和测试，因为我们不必检查它们的副作用结果。

# 对透明性有关的

引用透明性是这样一个事实，一个表达式可以被它的值或任何具有相同值的东西代替，而不会改变程序的结果。

对于给定的参数，函数应该总是返回相同的值，而不会产生任何其他影响。

同样，这意味着我们的功能没有副作用。

例如，我们上面的`add`函数具有引用透明性，因为对于传入的同一组参数，我们总是得到相同的输出。

# 结论

为了使我们的代码易于阅读和测试，我们尽可能坚持使用纯函数。

副作用是尽可能避免的，这样我们就不用去检查它们了。

函子是我们可以映射它们的值的任何东西。在 JavaScript 中，数组有`map`方法，所以 JavaScript 数组是函子。我们还可以将多种对象转换成数组，包括可迭代和不可迭代的类似数组的对象。