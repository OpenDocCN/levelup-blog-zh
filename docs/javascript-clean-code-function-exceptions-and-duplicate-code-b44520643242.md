# JavaScript 干净代码—函数异常和重复代码

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-function-exceptions-and-duplicate-code-b44520643242>

![](img/3614368b23806427f6f46e61fa8f3d71.png)

由 [Avinash Kumar](https://unsplash.com/@ashishjha?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

函数是 JavaScript 程序的重要组成部分。它们用于将代码分割成主要做一件事的可重用块。

因此，为了拥有清晰的 JavaScript 代码，我们必须拥有易于理解的函数。

在本文中，我们将看到函数的更多部分，包括输出参数、命令查询分离。、抛出异常和重复代码。

# 输出参数

输出参数是由函数直接返回的参数。

这很奇怪，因为参数通常被解释为输入，而不是直接用作输出。这个没有太多的用例。通常，参数是以某种方式计算出来的，方法是组合它们或检查它们，并通过这些检查和操作返回结果。

在 JavaScript 中，如果我们想改变一个共享状态，我们可以把共享状态作为类成员，然后我们可以有方法来操作类成员。

所以我们应该这样写:

```
class FruitStand {
  constructor(numFruits) {
    this.numFruits = numFruits;
  } addFruit() {
    this.numFruits++;
  } removeFruit(numFruits) {
    this.numFruits--;
  }
}
```

而不是返回传入的参数。

# 命令查询分离

一个函数要么改变一个对象的状态，要么返回一个对象的一些信息。然而，它不应该两者兼而有之。

例如，我们不应该有如下的函数:

```
const setProperty = (obj, property, value) => {
  obj[property] = value;
  return value;
}
```

该函数就地改变`obj`对象，并返回该值。

它做了两件事，这并不好，而且名字并没有传达出，也返回了一些关于对象的信息。这就误导了这个函数的用户，当他或她没有阅读函数定义，而仅仅是按照名字去做的时候。

因此，最好将属性设置和返回值分开，如下所示:

```
const setProperty = (obj, property, value) => {
  obj[property] = value;
}const getProperty = (obj, property) => {
  return obj[property];
}
```

像我们上面的那样，让每个函数做一件事情会好得多，这样人们就不会对他们正在做的事情感到困惑。

# 抛出异常比返回错误代码要好

返回错误代码违反了我们上面提到的命令和查询分离规则。这是因为返回某物的函数在出错时返回它，在函数成功运行时返回其他东西。

这意味着函数既做一些事情，形成命令部分，又返回一些事情，形成查询部分。

它应该只做一件事。因为函数的主要目的是做一些事情而不是返回一些东西，所以它应该只做命令部分而不是返回一个错误代码。

这意味着不要像下面这样写:

```
const setProperty = (obj, property, value) => {
  obj[property] = value;
  if (!value) {
    return 'Value not found';
  }
}
```

我们应该抛出一个异常，如下所示:

```
const setProperty = (obj, property, value) => {
  if (!value) {
    throw new Error('Value not found');
  }
  obj[property] = value;
}
```

如果需要，我们可以捕捉并处理它:

```
try {
  let obj = {};
  setProperty(obj, 'foo', null)
} catch (ex) {
  console.log(ex);
}
```

我们可以用`try...catch`消除大量的错误代码检查条件语句，而不是用 if 语句来检查函数返回的每个错误代码。

![](img/1452ba576baf82932ecf9331cbf8e22d.png)

照片由[艾弗里·伍德阿德](https://unsplash.com/@averieclaire?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 提取 Try…Catch 块

`try`程序块不应包含正常的加工代码。这是因为这使得我们很难知道错误会被抛出到哪里。

相反，我们应该把我们预计会有错误抛出的代码包装在一个`try`块中。然后我们可以在它下面写一个`catch`块来捕捉这个异常。

例如，如果我们有正常代码和需要捕获异常的代码，那么我们可以编写如下代码:

```
const functionThatThrowsError = () => { //... };
const doSomething = () => { //... };
const runFunctionThatThrowsError = () => {
  try {
    functionThatThrowsError();
  } catch (ex) {
    console.log(ex);
  }
}const runEverything = () => {
  doSomething();
  runFunctionThatThrowsError();
}
```

上面的代码将异常抛出和处理代码隔离到它自己的函数中，这让读者清楚地知道特定的抛出了一个需要处理的异常。

# 不要重复自己

重复代码绝对是大忌，一个东西变了，重复代码就得多处变。也很容易遗漏重复的代码。

代码也变得更加臃肿，因为它们在不同的地方重复。

有很多方法可以消除 JavaScript 中的重复代码，比如函数和模块。我们应该尽可能多地使用它们。

如果我们有重复的对象声明，那么我们也应该使用类作为模板来创建这些对象。

重复的文字应该被赋值给一个常量并重用。

# 结论

应该消除输出参数，因为我们不需要用它们来改变 JavaScript 中的共享状态。

做某事的函数应该与返回某事的函数分开。

此外，引发异常的代码比返回错误代码的代码更受欢迎。当我们需要处理异常时，抛出异常的代码应该被分离到它自己的函数中，以表明我们想要处理异常的意图。

重复代码是不好的。更改代码需要更多的时间，因为我们必须在多个地方进行更改。我们应该采取措施，通过使用可以在不同地方访问的代码来消除它们。