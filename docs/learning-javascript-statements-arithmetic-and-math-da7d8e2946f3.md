# 学习 JavaScript:语句、算术和数学

> 原文：<https://levelup.gitconnected.com/learning-javascript-statements-arithmetic-and-math-da7d8e2946f3>

![](img/110fda19f23bfe5cb1cb71a965c57c6b.png)

约翰·莫塞斯·鲍恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在这篇文章中，我将讨论如何在 JavaScript 中执行算术和更高级的数学运算。不过，首先，我需要讨论 JavaScript 中语句是如何形成和使用的。

# 声明

JavaScript 程序由语句组成。语句可以是任何东西，从单个函数调用或命令，甚至只是一个变量名。JavaScript 评估语句，然后执行它们。

例如，当您创建一个变量时，您编写一个语句:

```
let number = 100;
```

JavaScript 将此识别为一个语句，并按照其语法规则对其求值。在这种情况下，规则是将赋值运算符右边符号上的表达式赋给左边的变量。

如上所述，语句可以只是一个表达式，如下例所示:

```
js> 1;
1
```

您可以对变量做同样的事情:

```
js> let name = "Brendan";
js> name
"Brendan"
```

但是，随着您对 JavaScript 的深入了解，语句可能会比这些示例复杂得多。到目前为止，您已经看到了两种类型语句的例子——变量声明和赋值语句以及打印语句。

# JavaScript 算法

JavaScript 中使用算术运算符执行算术运算。有五种算术运算符:

*   `+`(添加)
*   `-`(减法)
*   `*`(乘法)
*   `/`(分部)
*   `%`(模/余数)

这些运算符是二元运算符，这意味着运算符的两边都必须有值。`+`运算符和`-`运算符也可以用作一元运算符，在这些运算符中，它们可以用来区分一个数的符号(正或负)。

JavaScript 算术运算符也有一个运算顺序，或者说优先级，在语句中使用时遵循这个顺序。运算的顺序是:1)取模；2)乘除法；3)加减法。

您可以使用括号来修改运算的顺序。当算术表达式放在括号内时，该表达式在任何其他运算之前进行计算。

例如，以下面的表达式为例:

```
let n = 100 + 3 * 22;
```

`n`是得到值 2266，103 * 26，还是变量得到值 166？没有括号的情况下，`n`的值是 166，因为乘法运算符优先于加法运算符，所以乘法发生在加法之前。

然而，如果你像这样写表达式:

```
let n = (100 + 3) * 22;
```

`n`的值是 2266，因为括号强制加法发生在乘法之前。

# 模运算符

许多人在了解计算机编程环境中的模运算符之前并不熟悉它们。这个运算符——`%`——返回两个操作数的余数。以下是使用 shell 的一些示例:

```
js> 100 % 2
0
js> 33 % 2
1
js> 1 % 2
1
```

第一个数字除以第二个数字，余数就是返回值。在第一个示例中，由于 2 除以 100，余数为 0，但在第二个示例中，由于 2 不除以 33，余数为 1。

最后一个例子很有趣，因为 2 根本不能除以 1，余数只是被除的数。

模运算符的一个应用是确定一个数是偶数还是奇数。如果一个数以 2 为模的结果是 0，那么这个数就是偶数。如果一个数以 2 为模的结果是 1，那么这个数是奇数。下面是一些再次使用 shell 的示例:

```
js> 24 % 2
0
js> 123 % 2
1
js> 1922 % 2
0
js> 7233 % 2
1
```

在另一篇文章中，我将向您展示如何使用这一事实编写一个函数来确定一个数字是偶数还是奇数。

模运算符的另一个用途是从数字中提取数字。您可以使用这种能力来创建在末尾包含一个校验位的标识号，这样标识号的前三个数字必须加起来就是第四个数字。

首先，让我们探索如何使用数字 123 去除数字。第一步是去掉 1 位的 3。这个表达式的作用是:

```
123 % 10; // returns 3
```

然后将 123 除以 10，并将结果转换为整数:

```
parseInt(123 / 10); // returns 12
```

`parseInt`是一个将浮点数转换成整数的内置函数。

然后取 12 模 10:

```
12 % 10; // returns 2
```

现在除以 12/10，并将其转换为整数，得到 1:

```
parseInt(12 / 10); // returns 1
```

现在让我们用它来验证一个识别号。有效的标识号是像 4217 这样的数字，其中 4+2+1 = 7。无效的数字应该是 4216。下面是确定识别号是有效还是无效的程序:

```
let idNumber = 4217;
let tempNumber = idNumber;
let digit4 = tempNumber % 10;
tempNumber = parseInt(tempNumber / 10);
let digit3 = tempNumber % 10;
tempNumber = parseInt(tempNumber / 10);
let digit2 = tempNumber % 10;
tempNumber = parseInt(tempNumber / 10);
let digit1 = tempNumber % 10;
let sumThree = digit1 + digit2 + digit3
print("Check digit is: " + digit4);
print("Sum of first three digits: " + sumThree);
```

这个程序的输出是:

```
Check digit is: 7
First three digits added: 7
```

4217 是一个有效的识别号，因为前三个数字加在一起等于第四个数字。

如果我们将`idNumber`的值更改为 4216，则输出为:

```
Check digit is: 6
First three digits added: 7
```

4216 不是有效的识别号，因为前三位数字加起来不等于第四位数字。

当然，模操作符还有其他用途，但是我认为您会从使用模来检查有效标识号的真实示例中受益。

# 使用数学函数

大多数编程语言都提供了一个包含大量数学函数的库，您可以在程序中使用这些函数。JavaScript 为此提供了`Math`库。[这里的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math)是一个链接，指向蜘蛛猴版本的`Math`库中所有函数的列表。

首先，这里有一个在 JavaScript 中使用`Math`库内置函数的快速入门。必须使用`Math`库名，后跟点运算符，然后是函数名来调用这些函数。下面是语法模板:

*Math.function-name(参数)；*

该函数将接受参数，执行计算，并返回结果。例如，下面是 pow 函数将一个数提升到幂的工作方式:

```
let number = Math.pow(2,3);
print(number); // displays 8
```

下面是另一个例子，这次是计算一个数的平方根:

```
let number = Math.sqrt(144);
print(number); // displays 12
```

数学库有全套的三角函数:

```
print(Math.cos(12.22)); // displays 0.9406110315852104
print(Math.tan(1.4); // displays 5.797883715482887
```

有查找最小值和最大值的函数:

```
print(Math.max(2.13, 2.129)); // displays 2.13
print(Math.min(2.13, 2.129)); // displays 2.129
```

你可以找到浮点数的上限和下限:

```
print(Math.ceil(88.76)); // displays 89
print(Math.floor(88.76)); // displays 88
```

数学库中有更多的函数，但是这个概述向您展示了使用这个库可以执行的各种计算。

# 用 JavaScript 做数学

众所周知，JavaScript 不是一种执行大量繁重数学计算的编程语言，但是这种语言确实有能力处理您将会遇到的大多数数学类型。这个概述描述了 JavaScript 算术运算符以及一些编程中常用的数学函数。

感谢您的阅读，请给我发电子邮件提出意见和建议。