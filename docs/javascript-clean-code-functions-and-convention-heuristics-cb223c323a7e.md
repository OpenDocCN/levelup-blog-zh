# JavaScript 干净代码—函数和约定试探法

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-functions-and-convention-heuristics-cb223c323a7e>

![](img/747a061a783a5ae7865963a86480764d.png)

照片由 [Ruslan Zh](https://unsplash.com/@chupzzz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

糟糕的代码有许多独特的字符。在这篇文章中，我们将看看每一个和他们是什么。我们看更一般的代码味道。

# 使用解释变量

变量应该有自己的名字。例如，不具有解释性的东西是像`x`或`y`这样的东西。我们不知道它们是什么意思。

另一方面，`numOranges`或`numApples`是解释性的，因为它们告诉我们要在这些变量中存储什么。我们知道我们将它分别设置为橘子和苹果的数量。

# 函数名应该说明它们是做什么的

函数名需要告诉我们它们在做什么，这样我们就不用猜了。

例如，我们不知道`date.add(2)`是做什么的？它可以增加秒、分、小时、天、月，或者任何我们还没有想到的东西。

我们应该将其重命名为更清晰的名称，如`addDays`或`addMonths`，这样我们就知道我们添加了什么。

如果我们必须查看代码或文档才能知道它在高层次上做了什么，那么也许它应该被重命名。

# 理解算法

我们应该理解我们写的代码。否则，我们可能偶尔会侥幸成功，但如果我们不知道它到底在做什么，那么我们最终会遇到问题。

当我们遇到问题时，我们不知道如何解决它们，因为我们一开始就不明白我们写的是什么。

此外，通过猜测来编写代码会产生混乱的代码，因为我们为了让它们工作而对它们进行了处理，但是我们担心当我们清理代码时，它会再次失败。

因此，我们应该在编写代码之前和期间进行思考和理解。

# 更喜欢多态而不是条件

条件句又长又乱。嵌套的更糟糕。如果我们只是用它们来调用不同的对象，我们应该尽可能少地使用它们。

# 遵循标准惯例

每个人都应该遵循基于行业规范的编码标准。在 JavaScript 中，有命名变量、常量和函数的约定。

此外，文件之间的间距和最大行长度是标准化的。

我们可以通过使用 Linters 和代码格式化程序来自动处理这些问题。

像垂直格式、函数和变量的放置等其他事情必须手动处理。

# 用命名常数替换幻数

当一个数没有被赋值给一个常数时，很难知道它意味着什么。

所以，如果我们用一个数作为常数，就要把它赋值给一，这样我们就知道它们的意思了。

例如，如果我们有一个每天小时数的常数，我们应该写:

```
const HOURS_PER_DAY = 24;
```

而不仅仅是`24`。

其他问题包括需要精度的浮点数。为了保持精度不变，我们应该将它们赋为常数。

像`PI`和`E`这样的东西应该被赋值给常量，这样它们总是有相同的精度。

除了数字，它们还适用于重复使用的任何其他常数值。例如，如果我们总是使用字符串`'Joe'`编写测试，那么我们可以将它赋给一个常量，并在任何地方引用它。

通过这种方式，我们避免了输入错误，减少了产生 bug 的机会。

# 精确

我们应该对代码中的所有内容保持精确。例如，我们不应该在变量名中使用`array`这个词，除非它是一个数组。

如果我们期望某些东西返回`null`或`undefined`，那么我们应该检查它们。

此外，我们应该期望任何东西的第一个匹配都是正确的匹配。我们实际上应该检查我们正在寻找的条件。

![](img/512adbfdeccddbf0ddd01b96c9c85880.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 传统之上的结构

我们应该加强结构而不是传统。我们可以通过测试和评审来塑造结构。

# 封装条件

当我们有一个包含多个条件的条件时，考虑将它们封装在一个函数中。

例如，不要写:

```
if (employee.type === 'FULL_TIME' && employee.hasHealthBenefits) {}
```

我们可以将布尔表达式放入如下函数中:

```
const hasFullTimeHealthBenefits = (employee) => {
  return employee.type === 'FULL_TIME' && employee.hasHealthBenefits;
}if (hasFullTimeHealthBenefits(employee)) {}
```

# 避免消极条件句

否定对我们的大脑来说很难，所以我们应该尽可能使用肯定的布尔表达式。例如:

```
if (isEmployed) {}
```

优于:

```
if (!isNotEmployed) {}
```

# 函数应该做一件事

函数应该只做一件事。如果一个函数做多件事，那么我们应该把它分成更小的函数。

例如，如果我们有以下代码:

```
const calculateTotalPay = (employees) => {
  let totalPay = 0;
  for (let employee of employees) {
    totalPay += employee.regularPay;
    totalPay += employee.overtimePay;
    totalPay += employee.bonus;
  }
  return totalPay;
}
```

我们可以将`totalPay`计算移到它自己的函数中，如下所示:

```
const calculateEmployeePay = (employee) => {
  return employee.regularPay +
    employee.overtimePay +
    employee.bonus;
}const calculateTotalPay = (employees) => {
  let totalPay = 0;
  for (let employee of employees) {
    totalPay += calculateEmployeePay(employee);
  }
  return totalPay;
}
```

现在我们有一个函数来获取总工资和员工工资，而不是一个大函数来获取员工工资和所有员工的总工资。

# 结论

编写代码时，我们应该遵循标准惯例。名字应该是清楚的，他们也应该遵循同样的情况。

双重否定也很难理解，要避免。

如果常量被重复使用，我们应该给它们赋值。

最后，函数应该只做一件事来简化它们。