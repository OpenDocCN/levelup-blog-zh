# JavaScript 中的高阶函数是什么？

> 原文：<https://levelup.gitconnected.com/what-are-higher-order-functions-in-javascript-e22924b39483>

![](img/2b379e05a0fe7ea3a6222d20a387ea42.png)

照片由[Micheile Henderson @ Micheile 010//视觉故事【nl】](https://unsplash.com/@micheile?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 JavaScript 中，高阶函数被用在很多地方。因此，知道它们是什么很重要。

在本文中，我们将了解什么是高阶函数，以及如何在 JavaScript 中使用它们。

# 高阶函数的定义

高阶函数是以其他函数作为自变量或返回函数作为结果的函数。作为参数接受的函数也叫做回调函数。

为什么函数可以接受其他函数作为参数或者返回函数？这是因为 JavaScript 函数像 JavaScript 中的任何其他实体一样是对象。作为对象的函数称为一级函数。

一级函数和其他变量一样对待。在 JavaScript 中就是这样，所以 JavaScript 函数是一级函数。

在 JavaScript 中，我们可以将函数赋给变量:

```
let foo = () => 1;
```

在上面的代码中，我们给`foo`变量分配了一个简单的匿名函数。

然后我们可以通过运行来调用它:

```
foo();
```

我们还可以向它添加属性，如下所示:

```
foo.bar = 1;
```

上面的代码是不好的实践，因为它令人困惑，因为函数通常是通过添加属性来调用而不是操作的。

但是，我们由此知道 JavaScript 函数是对象。它还有一个原型，包含像`apply`、`bind`和`call`这样的方法，可以修改用`function`关键字声明的常规函数的行为。

我们想对数字、字符串和对象等其他变量做的任何事情，都可以用函数来做。

# 作为函数参数的函数

因为 JavaScript 函数只是常规对象，所以我们可以在调用函数时将它们作为参数传入。这被用在很多地方，比如数组函数和承诺回调。

下面是一个简单的例子:

```
const addOrMultiply = (addFn, multiplyFn, a, b) => {
  if (Math.random() > 0.5) {
    return addFn(a, b);
  }
  return multiplyFn(a, b);
}const add = (a, b) => a + b;
const multiply = (a, b) => a * b;
const result = addOrMultiply(add, multiply, 1, 2);
```

在上面的代码中，我们定义了`addOrMultiply`函数，它接受一个将两个数相加的函数、一个将两个数相乘的函数和两个数作为参数。

在函数中，如果`Math.random()`返回一个大于 0.5 的数字，那么我们调用`addFn(a, b)`。不然我们叫`multiplyFn(a, b)`。

然后我们定义了`add`和`multiply`函数分别对 2 个数进行加法和乘法运算。

最后，我们通过传入`add`和`multiply`函数以及数字 1 和 2 来调用`addOrMultiply`函数。

这样，我们传入的函数就可以用`a`和`b`来调用。这并不意味着我们传入的函数必须与`addOrMultiply`函数内部的函数调用签名相匹配。JavaScript 不关心我们传入的函数的签名。然而，我们必须传递能得到预期结果的函数。

根据我们提到的高阶函数的定义，`addOrMultiply`符合它的定义。

![](img/f7702cfd2deafbfce1d0ef7d78564476.png)

照片由 [Greg Jeanneau](https://unsplash.com/@gregjeanneau?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 高阶函数的例子

## Array.prototype.forEach

我们可以使用`forEach`方法遍历数组的每个元素。它接受一个回调函数作为参数。回调将当前正在处理的数组条目、正在处理的数组的索引以及数组本身作为参数。

索引和数组参数是可选的。然而，我们知道 JavaScript 并不关心我们用于回调的函数的函数签名，所以我们必须小心。

我们可以编写以下代码:

```
const arr = [1, 2, 3];
arr.forEach((a, i) => console.log(`arr[${i}] is ${a}`))
```

我们应该看到:

```
arr[0] is 1
arr[1] is 2
arr[2] is 3
```

从`console.log`记录。

## Array.prototype. **地图**

`map`数组方法采用与`forEach`相同的回调函数。它用于将被调用的数组的数组条目映射到通过回调函数操作每个条目而返回的值。它返回一个新的数组。

例如，我们可以使用如下:

```
const arr = [1, 2, 3];
arr.map((a, i) => a**2)
```

那么我们应该得到:

```
[1, 4, 9]
```

## 设置超时

`setTimeout`函数将我们传递给它的回调函数中的代码的执行延迟了一段指定的时间。

例如，我们可以如下使用它:

```
setTimeout(() => {
  console.log('hi');
}, 1000)
```

上面的代码将在调用`setTimeout`函数 1 秒后记录`'hi'`。

`setTimeout`将回调中的代码推迟到下一次事件循环迭代，这样我们就可以在不影响整个程序的情况下延迟回调中代码的执行。

这种延迟代码是异步的。我们延迟代码执行，而不挂起主执行线程来等待它的执行完成。

这种模式在很多异步代码中都很常见，包括承诺回调、事件监听器和其他异步代码。

由于我们不能一行一行地执行异步代码，回调更加重要，因为这是在不确定的时间内运行一个函数的唯一方法。

## 事件处理程序

DOM 元素和 Node.js 中的事件处理程序也使用回调，因为有些代码只有在事件被触发时才会被调用。

例如，我们可以将一个简单的点击监听器附加到`document`对象，如下所示:

```
document.addEventListener('click', () => {
  console.log('clicked');
})
```

然后，每当我们点击页面，我们就会得到`'clicked'`记录。

正如我们所见，高阶函数在 JavaScript 中非常有用。它允许我们创建回调函数来传递给其他函数。它被同步代码和异步代码广泛使用。

同步回调的例子包括我们传递给数组方法的回调。

异步回调包括事件处理程序、`setTimeout`、承诺回调等等。它广泛用于异步代码，因为代码不是逐行运行的，所以我们需要回调来运行需要在不确定的时间内运行的代码。