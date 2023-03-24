# JavaScript 中的 Currying

> 原文：<https://levelup.gitconnected.com/currying-in-javascript-c0563b9b6c2c>

不，我不是在说食物😜

![](img/b657560959ff1dd8e600fc4179a88e74.png)

[rawpixel.com 喜剧电影——www . free pik . es](https://www.freepik.es/fotos/comida)

首先，**curry**不仅仅是一个 JavaScript 概念。**阿谀奉承**是每个人在使用函数式编程时必须知道的概念！

在理解 curry 之前，你必须对什么是纯函数有一个清晰的概念。

## 纯函数

纯函数可以简单地定义为一个函数，给定一个特定的输入，它将总是返回相同的输出。举个例子，

```
// ES5 syntax
const multiplyByTwo = function(numberToMultiply) {
  return numberToMultiply * 2;
};// ES6 [arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) expanded syntax
const multiplyByTwo = (numberToMultiply) => {
  return numberToMultiply * 2
};// ES6 reduced syntax (used when the function has just one line)
const multiplyByTwo = (numberToMultiply) => numberToMultiply * 2;console.log(multiplyByTwo(2)) // 4
```

如您所见，您可以任意多次调用 multiplyByTwo()函数，并且您将始终获得相同的结果，无论您是在今天、明天还是在一年内调用该函数，它都会产生相同的结果。

这仅仅是关于纯函数的一个基本解释。他们背后还有更多。更多信息请查看我之前关于纯函数的文章。

## 回到奉承

简单来说，currying 就是调用作为另一个函数的结果而产生的函数的动作。基本上是这样的，

```
const num = 1;console.log(add(1)); // (num2) => num1 + num2console.log(add(1)(1)); // 2
```

你可能想知道*加*是做什么的？，或者为什么 *add(1)(1)* 有两对括号而不是只有两个自变量？。这只是因为 *add* 是一个返回函数的函数。所以我们调用 add 返回的函数。那叫**阿谀奉承**！

Currying 非常强大，因为它允许您用一个基本行为组合不同的函数，而无需重复代码。让我们看看我们的*添加*函数的定义和一个‘普通’添加函数的定义。

```
const normalAdd = (num1, num2) => num1 + num2;const add = (num1) => (num2) => nun1+ num2;
```

所以现在你可能会认为 *normalAdd* 方法更清晰、更直接，但是我们的 curried *add* 版本隐藏着一种力量！

使用我们定制的 *add* 函数，我们可以如下构建一个递增或递减函数，

```
const incrementByOne = add(1);const decrementByOne = add(-1);console.log(incrementByOne(3)); // 4console.log(decrementByOne(3)); // 2
```

如您所见，我们能够使用之前创建的函数 *add 创建 2 个新的特定函数。*

多亏了 currying，我们可以用一个特定的实现来组合新的功能。当然，上面提到的例子非常简单，但是通过 currying，你可以构建更复杂的函数。例如，React 对 [*高阶组件*](https://reactjs.org/docs/higher-order-components.html#___gatsby) 一直使用这种组合模式。

> 高阶组件(HOC)是 React 中重用组件逻辑的一种高级技术。本质上，hoc 不是 React API 的一部分。它们是从 React 的**组成**本质中显现出来的一种模式。

换句话说，React 中的 HOC 是一个返回另一个函数的函数(就像我们的 *add* 函数一样！)将一些属性添加到被传递到 HOC 中的函数。

## 结论

Currying 允许我们将简单的基本函数组合成更具体的函数。在您的应用程序中使用 currying 不仅可以创建可重用的代码，还可以开始习惯函数式编程的一些概念。

一本了解函数式编程的牛逼书:[https://www . Amazon . com/-/es/gp/product/1661212565/ref = DBS _ a _ def _ rwt _ bibl _ vppi _ i0](https://www.amazon.com/-/es/gp/product/1661212565/ref=dbs_a_def_rwt_bibl_vppi_i0)