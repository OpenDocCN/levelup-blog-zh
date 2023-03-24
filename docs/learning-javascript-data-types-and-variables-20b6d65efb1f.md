# 学习 JavaScript:数据类型和变量

> 原文：<https://levelup.gitconnected.com/learning-javascript-data-types-and-variables-20b6d65efb1f>

![](img/71b261c8f8408ba7fe226132ef83bf10.png)

[Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

套用一本旧的计算机科学教科书的标题，“算法+数据=程序。”学习诸如 JavaScript 之类的编程语言的第一步是学习该语言可以处理什么类型的数据。第二步是学习如何在变量中存储数据。在本文中，我将讨论 JavaScript 程序中可以处理的不同类型的数据，以及如何创建和使用变量来存储和操作这些数据。

# JavaScript 数据类型

与编译编程语言不同，JavaScript 不要求您声明变量的数据类型。然而，在内部，JavaScript 跟踪存储在变量中的数据类型。

JavaScript 程序中可以使用的三种基本数据类型(也称为原语)是:

`number`:数值，如 100 或 3.14159
`string`:文本数据，可以包括数字和符号
`boolean`:真值或假值

还有其他原始类型(`BigInt`、`Symbol`和`undefined`)，但它们对于您的 JavaScript 初级教育并不重要。JavaScript 中还有三种其他的数据类型——`object`、`null`和`function`——但是我也将把对这些类型的讨论留到以后再说。

# 文字数据

当在程序中遇到数据时，该数据被称为文字数据。例如，如果我写:

```
print(100);
```

值`100`被称为数字文字。如果我写:

```
print("Hello, world!");
```

短语`“Hello, world!”`是一个字符串文字。在整个程序中可以找到文字，我需要描述什么是文字数据，以区别于变量，变量只是内存中可以保存数据的存储位置的名称。

# 确定文本的数据类型

你可以通过调用一个特殊的函数来查看文字的类型:`typeof`。这个函数返回传递给它的数据类型。下面是一些在一些文字数据上调用`typeof`函数的例子:

```
typeof(100); // returns "number"
typeof("hello"); // returns "string"
typeof(false); // returns "boolean"
```

随着您对 JavaScript 编程越来越有经验，您会发现`typeof`函数对于避免程序中的各种逻辑错误非常有用。

# 声明和使用变量

变量是在 JavaScript 中使用三种语法之一创建的。我可以通过展示创建变量的语法模板来描述这些。语法模板是 JavaScript 语句或结构的一般形式。

在我们研究如何创建变量之前，我们需要讨论创建变量的规则。变量需要以字母字符开头，尽管它们可以以符号开头。然而，标准的编程惯例是变量名只能以字母字符开头。

在第一个字符之后，变量的后续字符可以是其他字母字符、数字或符号。好的变量命名的关键是使你的变量名有意义，除非变量有非常临时和不重要的用途。

我将在本文的其余部分和以后的文章中演示良好的变量命名。

声明新变量的三个语法模板是:

*设变量-名称=表达式；
var 变量-名称=表达式；
常量变量名=表达式；*

您可能想知道这三种声明变量的方式有什么区别。其中两个与变量作用域的概念有关，我想在另一篇文章中讨论这个概念。另一种形式(以`const`开头)在下面讨论。

与此同时，我将只讨论如何使用`let`关键字创建变量，你必须相信我这是声明变量的最佳方式，直到我可以轻松地讨论变量范围以及为什么`let`比`var`更适合正确地确定变量范围。

下面是一些使用`let`声明变量并赋值的例子:

```
let name = "Sarah Cross";
let grade = 88;
let ratio = 0.25;
let flag = false;
```

现在让我们通过检查这些变量的类型来研究 JavaScript 做了什么:

```
print(typeof(name)); // displays string
print(typeof(grade)); // displays number
print(typeof(ratio)); // displays number
print(typeof(flag)); // displays boolean
```

JavaScript 变量的一个有趣特性是，它们不局限于保存一种数据类型的值。最初存储数字的变量可以被重新分配来存储字符串或其他类型的数据。当你使用变量时，你需要小心，不要让这种情况发生，因为它会让你或其他人更难阅读你的程序，理解它在做什么。

另一种声明变量的方法是使用`const`关键字。该关键字代表*常量*，表示变量中存储的值不能改变。如果你试图改变它，你会得到一个错误。

下面是一个声明一个`const`变量并试图改变其值的例子(这个例子直接取自我的 JavaScript shell，所以我可以显示错误消息:

```
js> const pi = 3.14159;
js> pi = 3.1;
typein:2:1 TypeError: invalid assignment to const `pi'
Stack:
@typein:2:1
js>
```

显示的错误消息表明您不能给声明为`const`的变量赋值。这违背了常数的定义。

`const`变量应该用于在程序中有特殊意义的值。为了突出这个变量的重要性，您应该将变量名全部用大写字母表示，就像这样:

```
const PI = 3.14159;
```

通过大写一个`const`变量的名字，你可以确保任何阅读代码的人都能认识到这个变量的特殊性质。

# 使用变量

变量用于它们的值。我们要么想在变量中存储一个值，要么想从变量中获取一个值。我已经向您展示了如何在变量中存储数据。现在让我们看看如何检索和更改存储在变量中的值。

只需使用变量名就可以检索变量值。在 shell 中，您只需输入变量名，按 Enter 键，系统就会返回存储在变量中的值。这里有一个例子:

```
js> let name = "Dennis Ritchie";
js> name
"Dennis Ritchie"
js>
```

您可以通过重新分配新值来更改变量值:

```
js> name = "Ken Thompson";
"Ken Thompson"
js> name
"Ken Thompson"
js>
```

您可以对数值变量做同样的事情:

```
js> let salary = 20000;
js> salary
20000
js> salary = 25000;
25000
js> salary
25000
js>
```

我将在另一篇文章中介绍算术，但是您可以用算术来更改变量值，如下例所示:

```
js> let salary = 20000;
js> salary
20000
js> salary = salary * 1.1;
22000
js> salary = salary - 5000;
17000
js>
```

现在我已经向您展示了变量值是如何更新的，我可以用一个练习来结束这篇文章，这个练习将允许您练习变量跟踪。阅读下面的 JavaScript 程序，从程序的开头到最后一行跟踪每个变量:

```
let num1 = 2;
let num2 = 3;
let num3 = num1 + num2;
num1 = num3 – num2;
num2 = num3 – num1;
num3 = num3 + 5;
num2 = num1;
num1 = num3;
```

下面是变量 trace:

数量 1: 2，2，10
数量 2: 3，3，2
数量 3: 5，10

列表末尾的值是变量在程序末尾存储的值。

# 接下来

这就结束了我对 JavaScript 变量的介绍。我的下一篇文章将介绍如何用 JavaScript 做算术，以及一些数学函数，以及如何执行简单的字符串操作。我还将回头解释 JavaScript 中的语句是如何形成的，尽管我在本文中一直在使用它们，您应该对我的例子中的语句有很好的理解。

感谢您的阅读，请给我发电子邮件提出意见和建议。