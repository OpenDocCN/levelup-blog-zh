# JavaScript 解决的问题:变量、数据类型、输入和输出

> 原文：<https://levelup.gitconnected.com/javascript-worked-problems-variables-data-types-input-and-output-ea1651806495>

![](img/f025a0fb6cf393da939ce863515c1273.png)

塞巴斯蒂安·赫尔曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在这篇文章中，我将提出一系列涉及 JavaScript 变量、数据类型、输入和输出的问题。快速回顾之后，我提出了问题。在问题之后，我提出了我的解决方案并做了一些解释。对于那些自学 JavaScript 的人来说，这些问题将会给你一个实践所学的机会。在看我的解决方案之前，请先试着自己解决这些问题。

我的 JavaScript 解决方案是用 Mozilla 的 SpiderMonkey JavaScript shell 编写的。

# 问题

**写一个程序，创建一个数字、字符串和布尔变量。**

使用上面的变量声明写一个程序，显示每个变量的数据类型。

**编写一个程序，通过创建一个函数 add()来演示全局变量，该函数访问两个全局变量的值并返回这些变量的和。**

**编写一个使用 for 循环演示块级范围的程序。**

编写一个程序，演示 JavaScript 中动态类型的本质。

**写一个程序，提示用户输入两个数字，并计算这两个数字的和。**

**修改上面的程序，使用反勾运算符来显示加法的结果。**

**写一个程序，接受圆周率和圆周率的值。程序应该使用公式计算圆的面积:*面积= πr2* 。**

**重写上面的程序，使圆的面积显示为小数点后只有两位数。**

# 问题和解决方案

编写一个创建数字、字符串和布尔变量的程序。

如果你已经用 JavaScript 编程有一段时间了，你需要习惯做的一件事就是使用 let 而不是 var 来声明变量，除非你想让变量成为一个全局变量。编程的一般规则应该是只有在必要时才使用全局变量，而在局部声明变量应该是规则。记住这条规则，下面是程序:

```
let number = 100;
let name = "Brendan Eich";
let flag = true;
```

使用上面的变量声明写一个程序，显示每个变量的数据类型。

数据类型是在 JavaScript 中使用`typeof()`函数发现的。该函数将变量作为其参数，并将参数的数据类型作为字符串返回。程序是这样的:

```
let number = 100;
let name = "Brendan Eich";
let flag = true;
print("number is a", typeof(number), "data type.");
print("name is a", typeof(name), "data type.");
print("flag is a", typeof(flag), "data type.");
```

**编写一个程序，通过创建一个函数** `**add()**` **来演示全局变量，这个函数访问两个全局变量的值并返回这些变量的和。**

JavaScript 中的变量如果没有在函数或块中声明，就是全局变量。下面是一个解决这个问题的程序:

```
let a = 1;
let b = 2;
function sumAandB() {
  return a + b;
}
print(sumAandB());
```

**写一个程序，演示使用** `**for**` **循环的块级作用域。**

在`for`循环内声明的循环控制变量将是块级的，只有在循环内可见，如果它是用`let`声明的。如果它用`var`声明，它将没有块级范围，所以它将在循环外可见。下面的程序演示了这一点，并用一些注释指出哪些语句可能会导致错误:

```
for (let i = 1; i <= 10; i++) {
  putstr(i + " ");
}
print(i); // this is an error
for (var i = 1; i <= 10; i++) {
  putstr(i + " ");
}
print(i); // displays 11 because i is not block-level
```

编写一个程序，演示 JavaScript 中动态类型的本质。

这个程序只需要通过赋值让一个变量承担几种不同的数据类型。我们使用`typeof()`函数来演示当不同的类型被分配给变量时，变量的类型如何变化。程序是这样的:

```
let x = 1;
print("x is of type", typeof(x));
x = "hello, world!";
print("x is of type", typeof(x));
x = false;
print("x is of type", typeof(x));
x = [1,2,3]; // array
print("x is of type", typeof(x));
```

写一个程序，提示用户输入两个数字，并计算这两个数字的和。

这个问题要求我们将用户的输入从字符串转换成数字。SpiderMonkey 的`readline()`函数从键盘读取数据，并将输入作为字符串返回。我们不能将两个字符串相加来得到它们的和。我们必须把输入转换成数字。这需要我们使用`parseInt()`函数，将字符串转换为整数，或者使用`parseFloat()`函数，将字符串转换为浮点值。

程序如下:

```
putstr("Enter the first number: ");
let number1 = parseInt(readline());
putstr("Enter the second number: ");
let number2 = parseInt(readline());
let sum = number1 + number2;
print("The sum of " + number1 + " and " + number2 + " is "
      + sum + ".");
```

**修改上面的程序，使用反勾运算符来显示加法的结果。**

反勾号允许模板文字，这只是意味着我们可以在字符串中嵌入变量，JavaScript 将访问变量的值，而不是将变量视为字符串的一部分(字符串插值)。然而，要做到这一点，我们必须使用一个特殊的符号: *${variable-name}* 。例如，看看这个简单的程序:

```
let name = "Jane";
print(`Hello, ${name}`); // displays Hello, Jane
```

下面是重写后的程序:

```
putstr("Enter the first number: ");
let number1 = parseInt(readline());
putstr("Enter the second number: ");
let number2 = parseInt(readline());
let sum = number1 + number2;
print(`The sum of ${number1} and ${number2} is ${sum}.`);
```

**写一个程序，接受圆周率和圆周率的值。程序应该使用公式计算圆的面积:*面积= πr2* 。**

这个问题要求我们使用两个不同的转换函数来检索用户的输入。为了得到π的值，我们需要使用`parseFloat()`，为了得到半径的值，我们需要使用`parseInt()`。程序如下:

```
putstr("Enter the value for pi: ");
const pi = parseFloat(readline());
putstr("Enter the radius of the circle: ");
let radius = parseInt(readline());
let area = pi * (radius * radius);
print(`The area of a circle with radius ${radius} is ${area}.`);
```

**重写上面的程序，使圆的面积显示为小数点后只有两位数。**

要编写这个程序，我们可以使用`Number.toFixed()` 函数来指定小数点后要显示的位数。程序如下:

```
putstr("Enter the value for pi: ");
const pi = parseFloat(readline());
putstr("Enter the radius of the circle: ");
let radius = parseInt(readline());
let area = pi * (radius * radius);
print(`The area of a circle with radius ${radius} is   
      ${area.toFixed(2)}.`);
```

这一套题就到此为止。请继续关注，我的下一篇文章将提出一些算术问题。

感谢您的阅读，请给我发电子邮件提出意见和建议。