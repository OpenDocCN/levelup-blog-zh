# 史蒂夫·麦康奈尔“代码完成”的教训:选择循环类型

> 原文：<https://levelup.gitconnected.com/lessons-from-steve-mcconnells-code-complete-choosing-a-loop-type-ed9c583e29b1>

![](img/f615abf4b437350a32b23672ac2cb2f0.png)

照片由 [Nareeta Martin](https://unsplash.com/@splashabout?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

初级程序员面临的最困难的任务之一是，当需要重复结构时，能够选择使用哪种循环类型。史蒂夫·麦康奈尔在他的《T21 代码全集》一书中为如何选择循环提供了一些指导。在本文中，我将使用他在书中没有使用的语言的例子来讨论这些准则——JavaScript(使用 Spidermonkey shell)和 Python

在大多数语言中，有三种通用类型的循环可供选择。这些是`for`回路、`while`回路和`do-while`回路。在`for`循环类别中，您可以选择索引`for`循环或`for-each`循环。

# 何时使用 for 循环或 for-each 循环

当您预先知道循环体将要执行的次数时，应该使用`for`循环或`for-each`循环。例如，如果您的问题是输入五个数字，然后求这些数字的平均值，那么可以使用`for`循环。以下是 Python 中的一个示例:

```
total = 0
average = 0.0
count = 5
for i in range(1,count+1):
  number = int(input("Enter a number: "))
  total = total + number
average = total / count
print("The average is",average)
```

这些类型的循环被称为计数控制循环，虽然您可以使用`while`构造来创建计数控制循环，但对于这些循环类型，使用`for`循环更为常规。

使用`for`循环的另一个明显的地方是在处理数组或列表的元素时。下面是一个用 JavaScript 计算列表中一组成绩的平均值的例子:

```
let grades = [81, 77, 93, 84, 99, 71, 82, 90, 83, 100];
let total = 0;
let average = 0.0;
for (let i = 0; i < grades.length; i++) {
  total += grades[i];
}
average = total / grades.length;
print("The average grade is",average);
```

`for`循环的另一个用途是逆向处理数组或列表。有一些函数可以反转数组中的元素，但是可能会出现这样的情况:您想要逐个元素地访问反转的数组，而不是先反转它。下面是另一个 JavaScript 示例:

```
let grades = [81, 77, 93, 84, 99, 71, 82, 90, 83, 100];
for (let i = grades.length-1; i >= 0; i--) {
  print(grades[i]);
}
```

虽然这些示例演示了如何正确使用`for` 循环来处理数组，但是当您处理数组的每个元素时，新的标准是使用`for-each`类型的循环，而不是索引`for` 循环。

使用这种类型的`for`循环的主要原因是为了最小化程序中可能的越界错误。当使用带索引的`for`循环时，您可能会犯错误，意外地试图访问数组或列表下限之前或数组或列表上限之后的索引值。下面是一个 JavaScript 数组越界时会发生什么的例子:

```
let grades = [81, 77, 93, 84, 99, 71, 82, 90, 83, 100];
let total = 0;
let average = 0.0;
const STOP = 10;
for (let i = 0; i <= STOP; i++) {
  total += grades[i];
}
average = grades / STOP;
print("The average is", average);
```

这段代码的输出是:

```
The average is NaN
```

发生的情况是，`STOP`的值是 10，但是成绩数组的最后一个元素是 9。我们访问了数组末尾以外的元素。这个值是什么？答案是 `undefined`。这意味着循环中的最后一次加法将未定义的值添加到累计总数中，这导致 JavaScript 将`undefined`赋给`total`。

避免这种错误的方法是使用 `for-each`类型的循环。在 JavaScript 中，这就是`for-of` 循环。这个循环遍历数组的每个元素，将每个元素赋给一个变量。下面是它在我演示的分数平均问题上的工作原理:

```
let grades = [81, 77, 93, 84, 99, 71, 82, 90, 83, 100];
let total = 0;
let average = 0.0;
for (let grade of grades) {
  total += grade;
}
average = total / grades.length;
print("The average is", average)
```

因为我没有对数组进行索引，所以我不能意外地越界。在 JavaScript 中，`for-of` 循环适用于任何可迭代容器，因此您可以在地图和集合等容器上使用它。

Python 有一个与 JavaScript 的 `for-of`循环类似的`for`循环类型。这是`for-in`循环。它就像`for-of l` oop 一样工作。下面是 Python 中使用`for-in`循环的成绩平均程序:

```
grades = [81, 77, 93, 84, 99, 71, 82, 90, 83, 100]
total = 0
average = 0.0
for grade in grades:
  total = total + grade
average = total / len(grades)
print("The average is",average)
```

`for`循环通常不用于文件处理，但在某些情况下会用到。许多解释型语言允许你将一个文件逐行读入一个变量。一旦发生这种情况，存储文件的变量就变成可迭代的，并且可以用 for-each 类型循环进行处理。下面是 Python 中这种循环的一个简单示例:

```
textFile = open("myfile.txt", "r")
for aLine in textFile:
  print(aLine.rstrip())
```

值得注意的是，几个计算机编程专家强烈建议在访问容器的所有元素时使用`for-each`类型的循环，不管是哪种语言或容器类型，假设容器是可迭代的。如果你感兴趣，可以在 Joshua Bloch 的 *Effective Java* 中找到使用 for-each 类型循环的特别有力的论据。

不能用`for-each` 类型循环访问的容器的一个例子是 C++数组(至少是内置数组)，当它被传递给函数时。当一个数组传递给一个函数时，C++函数不能确定数组的第一个元素从哪里开始。然而，你可以用 STL `array`类来实现。

# 何时使用 while 循环

当你不知道循环体到底需要执行多少次时，应该使用`while` 循环。读取文件就是一个例子。大多数时候，当你在读取一个文件的内容时，你不知道到底有多少行(或记录)需要被读取，所以你只能在一个`for`循环中执行这种类型的操作。读取文件的典型方式是读取文件，直到遇到某种类型的文件结尾字符或标记。

另一个例子是，当你从键盘输入数据时，你不知道什么时候才能完成输入。实现这一点的一种方法是让`while`循环寻找一个标志值，该标志值表示输入结束。下面是 Python 中这种类型的`while`循环的一个例子:

```
total = 0
count = 0
grade = int(input("Enter a grade (-1 to quit): "))
while grade != -1:
  total = total + grade
  count = count + 1
  grade = int(input("Enter a grade (-1 to quit): "))
average = total / count
print("The average is", average)
```

在本例中，sentinel 值为-1，当输入 sentinel 值时，程序停止并计算平均值。

# 何时使用 do-while 循环

一个`do-while`循环类似于一个`while`循环，除了停止循环的测试是在循环体的末尾，而不是在循环体的开始。大多数`while`循环可以重写为`do-while`循环，但是通常没有好的理由这样做。不过，偶尔会出现一种情况，即`do-while`循环比`while`循环效果更好。这里有一个例子。

您希望编写一个用户登录循环，这样，如果用户输入了错误的密码，程序就会返回到提示，让用户再次输入他们的密码。以下是 JavaScript 中的一个例子:

```
let password = "letmein";
let userEntry;
do {
  putstr("Password: ");
  userEntry = readline();
} while (userEntry != password);
print("You're in!");
```

与使用`while` 循环相比，使用`do-while`循环编写这个示例的代码更少。否则，您必须在进入 `while` 循环体之前提示用户输入密码，并在循环体底部再次提示。

# 使用循环的内容比我在这里介绍的要多

我在本文中从 *Code Complete* 中介绍的材料只是麦康奈尔在书中谈论的主题的开始。在以后的文章中，我将介绍他谈到的关于循环的一些其他主题，比如准备进入循环，如何处理循环体，以及如何处理循环的早期退出。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。