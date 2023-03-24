# JavaScript 干净代码—函数参数

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-function-arguments-f920e30b9030>

![](img/1c77847d0f0f14c8c7e3436dd4403012.png)

[克里斯托弗·坎贝尔](https://unsplash.com/@chrisjoelcampbell?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

函数是 JavaScript 程序的重要组成部分。它们用于将代码分成可重用的块。因此，为了拥有清晰的 JavaScript 代码，我们必须拥有易于理解的函数。

在本文中，我们将研究好函数的更多特征，包括减少 switch 语句的使用，减少函数中的参数数量，以及以易于理解的方式获取参数。

# Switch 语句

即使语句只有几个格，也总是很长。因此，应该以一种永不重复的方式使用它们，将它们分离成一个函数，在需要时随时调用。

下面的代码就是一个糟糕的`switch`语句的例子:

```
const calculatePay = (employeeType) => {
  switch (employeeType) {
    case 'PART_TIME':
      return calculatePartTimePay();
    case 'FULL_TIME':
      return calculateFullTimePay();
    case 'COMMISSIONED':
      return calculateContracorPay();
    default:
      return 0;
  }
}
```

上面的代码很长，它做的不止一件事。最好是跳过`switch`声明，然后在需要他们的地方给他们打电话。

还有，我们每次改变类型都要改变这个函数。

另一个问题是，我们将有许多其他函数使用这种结构，这意味着代码将变得更长，有更多的函数具有与这一个相同的问题。

为了处理这个问题，我们可以为我们想要的雇员类型返回正确的对象。例如，我们可以这样写:

```
const getEmployeeObjectByType = (employeeType) => {
  switch (employeeType) {
    case PART_TIME:
      return new PartTimeEmployee();
    case FULL_TIME:
      return new FullTimeEmployee();
    case CONTRACTOR:
      return new ComissionedEmployee();
    default:
      return undefined;
  }
}
```

然后我们可以调用这个函数来获取合适的雇员类型，然后用它做我们想做的事情。

这比让多个函数在相同的 switch 语句情况下做不同的事情要好得多。

# 描述性名称

像我们代码中的任何其他标识符一样，函数中的名字应该像其他东西一样是描述性的。

它们应该用能说明其含义的名字来命名。像含义、意图和动作这样的信息应该从代码中传达出来。

由于 JavaScript 是一种动态类型的语言，如果从代码中看不出东西的类型，那么类型在某些情况下也是有帮助的。

较长的名字是可以的，因为它们需要传达我们需要知道的所有信息。如果遵循 JavaScript 的约定，它们仍然易于阅读，即变量和非构造函数使用 camel case，常量使用大写，构造函数使用大写。

![](img/c15b91d21d0c67a6e91a26c6f2cea7bf.png)

Benny Vincent 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 函数参数

函数应该有尽可能少的参数。这是因为我们必须查看所有的对象，当我们调用它们时，我们必须确保它们都被传入。

因此，如果我们不需要参数，那么我们就不应该包含它们。零比一好，一比二好。

超过 5 可能太多了，除非有一些特殊的原因。

传入参数很难，因为我们必须收集所有的变量和值并传递它们。同样，出于同样的原因，这也增加了测试的难度。

当我们测试一个有很多参数的函数时，我们必须测试所有可能的参数组合来获得完整的测试覆盖。这使得测试一个采用大量参数的函数比采用较少参数的函数困难许多倍。

此外，rest 操作符是 JavaScript 中的标准特性，如果我们需要定义一个有很多参数的函数，是时候放弃`arguments`对象了。它是一个类似数组的对象，所以它具有数组的一些属性，比如索引和`length`属性。

我们也可以用一个`for`或`while`循环来遍历它。但是，它没有任何属于普通数组的数组方法。

与其他类似数组的对象一样，它可以使用 spread 操作符进行扩展。

他们只是给很多人制造了困惑，因为很多人不熟悉`arguments`这个物体。

`arguments`对象也没有绑定到箭头函数，所以我们不能和它们一起使用。

使用 rest 操作符，我们可以从它那里得到一个参数数组，所以我们可以对数组做任何我们能做的事情。

所以与其写:

```
function add() {
  return [...arguments].reduce((a, b) => a + b, 0);
}
```

我们应该写:

```
function add(...args) {
  return args.reduce((a, b) => a + b, 0);
}
```

做一个将一些数字相加的函数。或者更好的是:

```
const add = (...args) => {
  return args.reduce((a, b) => a + b, 0);
}
```

因为我们在代码中没有使用`this`。

正如我们所看到的，后面两个使用 rest 操作符的例子比第一个要清楚得多。第一个在签名中没有参数，但是我们得到了从`arguments`对象传入的参数。

另外 2 个显示了我们的`add`函数实际上接受参数，并且我们实际上在用它们做一些事情。

# 结论

函数表面上看起来很容易定义，但是要定义易于阅读和修改的函数，我们必须小心谨慎。我们应该避免过多的`switch`语句。

如果我们确实需要它们，我们应该对它们进行抽象，以便一个`switch`语句可以在多个地方使用。它们很长，应该最小化。

此外，函数中事物的名字应该是描述性的，我们不应该在函数中使用太多的参数。如果我们这样做，使用 rest 操作符而不是`arguments`对象来获取传递给函数的参数。