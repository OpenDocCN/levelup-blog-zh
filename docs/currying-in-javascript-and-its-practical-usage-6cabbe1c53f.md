# Javascript 中的 Currying 及其实际应用

> 原文：<https://levelup.gitconnected.com/currying-in-javascript-and-its-practical-usage-6cabbe1c53f>

![](img/512f58079b42dca0c384b0055cea6ced.png)

奉承有时很难理解或混淆实际用法。在本文中，我将解释它是什么以及一些用例。

# 什么是 currying？

Currying 是将接受多个参数的函数转换成一系列函数的过程，每个函数一次接受一个参数。是[部分功能应用](https://en.wikipedia.org/wiki/Partial_application)的特殊类型。在纯函数式编程语言(如 ML 或 Haskell)中，函数在默认情况下是以 curried 形式定义的。让我们看看它在 Javascript 中是如何工作。

```
 // A normal JS function
const sum = (a, b) => a + b
sum(1, 2) // 3// Curried version of the sum function above
const curriedSum = a => b => a + b
curriedSum(1)(2) // 3
```

上面的`curriedSum`函数返回一个函数而不是最终结果。返回的函数在其闭包范围内包含`a`，接受列表中的另一个参数(`b`，然后计算最终结果。听起来不错？现在，如果有更多的参数会怎么样呢？

```
// Normal sum function with 3 parameters
const sum = (a, b, c) => a + b + c
sum(1, 2, 3)  // 6const curriedSum = a => b => c => a + b + c
curriedSum(1)(2)(3)  // 6
```

函数需要多少参数并不重要。如果一个函数有`n`个参数，它的简化版本将是一串`n`函数。只有最后一个函数会返回期望的结果。其他函数返回链中的下一个函数。注意，每个函数应该只有一个参数，称为`Unary function`。我们将在另一节更深入地讨论一元函数。

# 为什么是咖喱？

Currying 是部分功能应用的一种类型。我们可以使用它返回的函数来创建一个现有函数的简化版本。当我们有很多地方以完全相同的方式使用一个函数时，这是很有帮助的。我们的实现将会更短，可读性更强。

```
// Normal usage
const PERCENT_120 = 1.2
const multiply = (a, b) => a * bconst newSalary = multiply(salary, PERCENT_120)// Currying usage
const multiply = a => b => a * b
const increase20Percent = multiply(1.2)const newSalary = increase20Percent(salary)
```

听起来很酷。但这不是主要的好处。正如我在上一节提到的，currying 需要严格使用`unary function`。它为标准接口打下了良好的基础。当我们有一组实现公共接口的函数时，我们可以很容易地在它们上面引入灵活的用法。这非常类似于`Promise`如何定义其标准并变得超级强大。

以下是约定函数的标准:

*   它有一个参数
*   它返回一个`unary function`，它也有一个参数

在这篇文章的范围内，我将讨论两种主要的用法:自由风格和功能组合。

# 无点样式

单点自由风格，也称为[隐性编程](https://en.wikipedia.org/wiki/Tacit_programming)，是一种调用者不将参数显式传递给函数的编码风格。请看下面的两个例子。第二个解释了自由风格，其中`console.log`符合`catch`要求的标准。

```
// Normal implementation
try {
  ...
} catch (err => console.log(err))// Point free style
try {
  ...
} catch (console.log)
```

对无点风格的唯一要求是输入函数的原型与参数的规格相匹配。这种风格可以缩短代码，使其可读性更好。

```
// Normal implementation
fetchUser(id).then(user => {
  return verifyAdminUser(user)
}).then(isAdmin => {
  return render(isAdmin)
})// Point free style
fetchUser(id).then(verifyAdminUser).then(render)
```

好了，现在让我们看看如何在下面的例子中用 curried 函数实现这种风格。这个示例中的`compose`函数非常流行，出现在许多库中，一个流行的函数是 [lodash](https://github.com/lodash/lodash/wiki/FP-Guide) 。我把它的实现放在这里，以防您想自己运行代码。

```
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));const sum = a => b => a + b
const multiply = a => b => a * bconst addTransactionFee = sum(2)
const addTax = multiply(1.1)
const addMonthlyPromotion = multiply(0.8)// Result of compose function below equals to
// addTransactionFee(addTax(addMonthlyPromotion(100)))
const paymentAmount = compose(addTransactionFee, addTax, addMonthlyPromotion)(100)  // 100 * 0.8 * 1.1 + 2 = 90
```

# 功能组成

[功能组合](https://en.wikipedia.org/wiki/Function_composition_(computer_science))是一种将简单功能组合起来构建更复杂功能的机制。在组合链中，每个函数的结果都是下一个函数的参数。最后一个函数的结果将是最终结果。在我之前的示例中，我将 3 个函数`addTransactionFee`、`addTax`和`addMonthlyPromotion`组合成一个。

在 Javascript 中，函数只返回一个值。因为这个值将是下一个调用的参数，所以一个函数必须是一元的才是可组合的。一个课程化的函数完全适合写作。如果你想构造一个有不止一个参数的函数，最好的方法是 curry。

让我们回到之前的例子。现在我想在每一步之间添加一个日志函数来调试我的代码。

```
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));const sum = a => b => a + b
const multiply = a => b => a * bconst addTransactionFee = sum(2)
const addTax = multiply(1.1)
const addMonthlyPromotion = multiply(0.8)
const log = a => {
  console.log(a)
  return a
}const paymentAmount = compose(
  log,
  addTransactionFee, 
  log,
  addTax,
  log,
  addMonthlyPromotion
)(100)
// Output:
// 80
// 88
// 90
```

在上面的示例中，我添加了一个新函数`log`。这个函数只是在返回输入参数之前添加了一个日志。注意，我不能直接编写`console.log`，因为这个函数不返回任何东西。现在一切似乎都好了。但是，如果我想为每个步骤记录一个自定义标签，会发生什么呢？log 函数将接收另一个参数:

```
const log = (label, value) => {
  console.log(label, value)
  return value
}
```

这个函数不再是可组合的，因为它有两个参数。该是奉承的时候了。以下是完整的代码:

```
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));const sum = a => b => a + b
const multiply = a => b => a * bconst addTransactionFee = sum(2)
const addTax = multiply(1.1)
const addMonthlyPromotion = multiply(0.8)
const log = label => value => {
  console.log(label, value)
  return value
}const paymentAmount = compose(
  log('addTransactionFee'),
  addTransactionFee, 
  log('addTax'),
  addTax,
  log('addMonthlyPromotion'),
  addMonthlyPromotion
)(100)
// Output:
// addMonthlyPromotion 80
// addTax 88
// addTransactionFee 90
```

# 结论

在这篇文章中，我介绍了 currying 的定义和它的一些实际用法。在我看来，最有用的是函数构成。感觉好吗？如果你想更深入地了解，我推荐埃里克·艾略特的书`Composing Software`。他从数学和函数式编程的角度对 currying 做了很好的解释。

稍后，我可能会带着另一篇关于奉承技巧的文章回来。下次见。

*原发布于*[*https://huynvk . dev*](https://huynvk.dev/blog/currying-in-javascript-and-its-practical-usage)*。*