# JavaScript 中的睡眠功能在哪里？

> 原文：<https://levelup.gitconnected.com/wheres-the-sleep-function-in-javascript-69dc15d54ac8>

![](img/f62b8ed430eb66622881f8ce7b44af5e.png)

照片由 [Alexandra Gorn](https://unsplash.com/@alexagorn?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

如果我们试图在 JavaScript 中搜索`sleep`函数，我们不会找到它。但是，我们可以很容易地用现有的功能制作一个。

# setTimeout 函数

有一个`setTimeout`函数让我们在指定的毫秒数后运行代码。我们可以用它来创建我们自己的`sleep`函数。

像大多数对时间敏感的 JavaScript 代码一样，它将是异步的，因为这不会阻塞运行我们程序的主线程。

我们可以通过编写以下代码来创建一个在指定时间后运行代码的函数:

```
const runLater = (delay) => {
  setTimeout(() => {
    console.log('bar');
  }, delay)
}
```

当我们以毫秒为单位传递延迟时，`runLater`函数将显示字符串`'bar'`。

如果我们跑:

```
console.log('foo')
runLater(100);
console.log('baz')
```

我们会看到我们得到:

```
foo
baz
bar
```

这是因为第一行和最后一行同步代码首先运行。`runLater(100)`排队等待在第一行和最后一行运行之间的事件循环的下一次迭代中运行。

然后在事件循环的下一次迭代中，`setTimeout`回调中的代码运行，这将记录`'baz'`。

这意味着如果我们想在回调中连续运行代码，我们必须嵌套回调。过多的回调会造成回调地狱。

# 承诺

因此，顺序运行异步 JavaScript 代码的最佳方式是使用承诺。我们可以通过使用`Promise`构造函数用`setTimeout`函数创建一个承诺。

我们可以如下编写`sleep`函数:

```
const sleep = (delay) => {
  return new Promise(resolve => {
    setTimeout(resolve, delay)
  });
}
```

`sleep`函数返回一个在`setTimeout`回调后解决的承诺，也就是在`delay`毫秒后`resolve`函数被调用。

承诺和`setTimeout`回调一样是异步的。它的好处是，我们可以在实现它们时按顺序运行它们。

Fulfilled 意味着调用了上面回调中的`resolve`方法。当出现错误时，也可以使用`reject`功能拒绝承诺。

使用承诺让我们链接异步代码并顺序运行它们。

我们可以用`async`函数干净利落地做到这一点。为了定义一个`async`函数，我们使用如下的`async`和`await`关键字:

```
(async ()=>{
  console.log('foo');
  await sleep(2000);
  console.log('bar');
})();
```

如果我们运行上面的代码，我们应该看到`'foo'`被记录，然后 2 秒后`'bar'`被记录。

这将让我们在不挂起应用程序的情况下完成延时代码。

参数中的数字 2000 是以毫秒为单位的，所以它将在运行下一行之前等待 2 秒钟。

`await`告诉浏览器暂停执行下一行，直到`await`行被解析。我们可以把`await`放在任何承诺的前面，表示要等承诺解决了再继续。

我们可以不断重复这个:

```
(async () => {
  console.log('foo');
  await sleep(2000);
  console.log('bar');
  await sleep(2000);
  console.log('a');
  await sleep(2000);
  console.log('b');
})();
```

但是它变得重复。幸运的是，我们可以使用循环来消除这种重复。

![](img/eead374ff2cf09feeffd5eeed8062a82.png)

照片由[布鲁斯·马斯](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# For-Await-Of 循环

我们有一个`for-await-of`循环，它遍历可迭代对象，比如数组和类数组对象，不管它们是同步的还是异步的。

为此，我们可以写:

```
(async () => {
  const arr = ['foo', 'bar', 'a', 'b'];
  for await (let a of arr) {
    console.log(a);
    await sleep(2000);
  }
})();
```

上面的代码要干净得多，我们想循环多少次都没关系。

不管我们是否想使用循环，在我们的`async`函数中有 promise 代码是很重要的。

我们也可以从一个`async`函数返回一个承诺，所以我们可以写:

```
(async () => {
  const arr = ['foo', 'bar', 'a', 'b'];
  for await (let a of arr) {
    console.log(a);
    await sleep(2000);
  }
  return sleep(2000);
})();
```

为了回报`sleep(2000)`的承诺。

此外，我们可以给它命名，并在其他`async`功能中使用，如下所示:

```
const foo = async () => {
  const arr = ['foo', 'bar', 'a', 'b'];
  for await (let a of arr) {
    console.log(a);
    await sleep(2000);
  }
  return sleep(2000);
};(async () => {
  await foo();
  console.log('c');
})();
```

这说明`async`功能是承诺。

# 结论

在 JavaScript 中，不像其他语言那样有`sleep`函数。然而，我们可以很容易地做出自己的选择。

有了承诺，我们可以轻松解决时间问题。它使得延时代码的执行变得清晰易读。我们可以通过使用`setTimeout`函数来编写延时的 JavaScript 代码。

`async`和`await`语法使得读写代码变得轻而易举。

最后，我们可以在其他`async`函数中调用`async`函数，因为这些类型的函数只返回承诺。