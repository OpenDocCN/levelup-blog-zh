# 函数式 JavaScript —优势

> 原文：<https://levelup.gitconnected.com/functional-javascript-benefits-bdc61cc0925>

![](img/d97eeffe877736c7a5cb4fe4cc2cbb35.png)

由[马克斯·伯麦](https://unsplash.com/@m_a_x_b?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将了解如何使用 JavaScript 中的函数式编程特性。

# 函数式编程的好处

函数式编程有各种好处。

这就是它被 JavaScript 等编程语言采用的原因。

# 纯函数

函数式编程的一个好处是我们在代码中定义纯函数。

如果我们向一个纯函数传递相同的输入，它会返回相同的输出。

例如，我们可以写:

```
const square = (value) => value ** 2;
```

我们得到这个值，然后返回它的平方。

不管外面发生什么，这一点都不会改变。

好处是纯函数很容易测试。

我们可以在给它一些输入后检查输出。

由于它不依赖于外部的任何东西，我们可以很容易地检查它。

我们可以通过编写如下代码来检查返回值:

```
square(2) === 4
```

# 合理代码

阅读代码很容易，因为函数的代码都在函数内部。

例如，如果我们有:

```
const square = (value) => value ** 2;
```

我们所做的就是对传入的数字求平方。

外面什么都没有，所以我们可以很容易地看着它。

# 并行代码

由于纯函数不依赖于函数外部的任何值，所以我们不必担心函数的值与外部的值同步。

如果我们有全球价值观，那么我们可能必须这样做:

```
let global = "something"
let foo = (input) => {
  global = "somethingElse"
}let bar = () => {
  if (global === "something") {
    //...
  }
}
```

在做一些事情之前，我们必须检查变量`global`的值。

对于纯函数，我们不必这样做，因为没有外部依赖。

# 可食用的

对于给定的输入，纯函数总是返回相同的输出。

所以我们可以很容易地缓存函数输出。

我们只是用输入作为键，输出作为值。

我们可以从缓存中查找值。

通过缓存，我们可以提高代码的速度。

例如，我们可以用一个对象保存一个缓存:

```
const cache = {
  1: 2,
  3: 4,
  //...
}
```

然后，我们可以通过编写以下代码来检查缓存的值:

```
const value = cache.hasOwnProperty(input) ?
  cache[input] :
  cache[input] = longRunningFunction(input)
```

在运行`longRunningFunction`之前，我们检查缓存的值。

# 管道和可组合

我们可以很容易地合成纯函数。

我们只是将一个纯函数的返回值传递给另一个纯函数。

例如，我们可以写:

```
const foo = (a) => {
  return a * 2;
}const bar = (b) => {
  return b * 3;
}
```

我们可以通过编写以下内容来组合这些函数:

```
foo(bar(100));
```

它看起来像一个数学函数，它是一个数学函数。

![](img/288e8de7a5948ea58ed6ba6c6efae499.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

函数式编程有各种好处。

我们可以很容易地并行运行代码，因为我们不必同步代码。

此外，我们可以很容易地组合函数。

它们也更容易阅读和测试。