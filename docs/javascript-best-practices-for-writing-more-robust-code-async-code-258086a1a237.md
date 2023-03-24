# 编写更健壮代码的 JavaScript 最佳实践——异步代码

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-async-code-258086a1a237>

![](img/782be2da41f2efdb62795706cce1a44e.png)

Lionel HESRY 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究用 Javascript 编写异步代码的最佳实践。

# 使用承诺

承诺对于用 JavaScript 编写异步代码非常有用，因为我们可以将多段异步代码链接在一起，而无需嵌套多个异步回调。

我们不能嵌套太多的异步回调，因为它很难阅读和理解。因此，很容易产生 bug，做出改变也会很慢。

有了承诺，我们可以将多个承诺链接在一起，让编写一系列异步代码变得非常容易。

例如，我们可以编写以下代码将多个承诺链接在一起，如下所示:

```
Promise.resolve(1)
  .then(val => Promise.resolve(val * 2))
  .then(val => Promise.resolve(val * 3))
```

在上面的代码中，我们调用了`Promise.resolve`，它是异步的，返回一个承诺。每个`Promise.resolve`调用都在事件循环的末尾排队，并在 JavaScript 主执行线程没有任何排队任务时运行。

我们上面做的链接比嵌套回调要干净得多。`val`只是从之前解决的承诺中获取价值。

正如我们所看到的，我们还可以从之前解决的承诺中获取价值，并利用它做一些事情。这是嵌套回调很难做到的，因为我们必须做 3 层嵌套来做同样的事情。

为了捕捉执行任何承诺时发生的错误，我们可以使用回调函数调用`catch`,如下所示:

```
Promise.resolve(1)
  .then(val => Promise.resolve(val * 2))
  .then(val => Promise.reject('fail'))
  .catch(err => console.log(err))
```

在上面的代码中，我们调用了`Promise.reject`，这将导致承诺链失败。`Promise.reject`获取失败的原因，该原因将在`catch`回调中的`err`对象中提供。

因此，我们将看到从承诺回调记录的`'fail'`。

当第一个失败的承诺运行时，承诺停止运行，因此我们在承诺链中只能有一个`catch`调用。

错误处理也是嵌套异步回调所不具备的，除非接受回调的代码显式返回一个错误作为回调中的参数。

此外，我们必须做更多的嵌套来处理嵌套异步回调中的错误，这意味着阅读代码更加困难，并产生更多的错误。

为了使承诺更短，我们可以使用`async`和`await`语法来链接承诺。

例如，我们可以编写下面的代码来使用`async`和`await`，这样我们就可以删除`then`和回调，但是做和以前一样的事情:

```
(async () => {
  const val1 = await Promise.resolve(1);
  const val2 = await Promise.resolve(val1 * 2);
  const val3 = await Promise.resolve(val2 * 3);
})()
```

这比使用`then`和回调更干净，因为现在我们的代码中没有回调，只有 3 行代码。此外，代码的可读性不会因为使用它而变差。

`val1`、`val2`和`val3`与`then`回调中的参数值相同，都是解析后的值。

为了在承诺链中的承诺失败时捕捉错误，我们可以像处理同步代码一样使用`try...catch`。

例如，我们可以编写以下代码来捕捉带有`async`和`await`的承诺:

```
(async () => {
  try {
    const val1 = await Promise.resolve(1);
    const val2 = await Promise.resolve(val1 * 2);
    await Promise.reject('fail')
  } catch (err) {
    console.log(err)
  }
})()
```

在上面的代码中，我们在 promise 代码周围包装了一个`try...catch`块，这样我们就可以捕捉 promise 链中的错误。当承诺失败时，它停止运行，因此一个 catch 块将完成所有工作。

![](img/87f83ebfb99c3b6d2c2f5d58fbcc5e47.png)

你好，我是尼克🍌上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 将不相关的承诺与 Promise 并行运行

我们应该使用`Promise.all`并行运行不相关的承诺。这样，我们就不必在运行另一个不必要的承诺之前等待一个承诺的解决，从而加速我们的代码。

`Promise.all`接受一个承诺数组，并返回一个包含已解析值数组的承诺。

例如，我们可以编写以下代码来使用`Promise.all`:

```
Promise.all([
    Promise.resolve(1),
    Promise.resolve(2),
    Promise.resolve(3),
  ])
  .then(([val1, val2, val3]) => {
    console.log(val1, val2, val3);
  })
```

在上面的代码中，我们在一个数组中有 3 个带有`Promise.resolve`的承诺。然后我们用它来调用`Promise.all`来并行解决它们。然后我们在回调中获取解析的值并记录它们。

使用`async`和`await`语法，我们可以编写:

```
(async () => {
  const [val1, val2, val3] = await Promise.all([
    Promise.resolve(1),
    Promise.resolve(2),
    Promise.resolve(3),
  ])
  console.log(val1, val2, val3);
})();
```

`Promise.all`既然一诺千金。

# 结论

当用 JavaScript 编写异步代码时，我们应该使用承诺。这样，它们可以很容易地链接起来，我们也可以用`Promise.all`并行运行它们。

移除嵌套大大增加了可读性，因此不太可能出错。此外，错误处理是标准的，因为我们可以调用`catch`来捕捉错误。

`async`和`await`语法缩短了链接承诺的时间。同样，我们可以用`try...catch`来捕捉错误。