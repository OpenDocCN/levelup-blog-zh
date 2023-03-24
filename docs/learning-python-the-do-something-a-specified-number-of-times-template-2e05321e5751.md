# 学习 Python:做指定次数的事情模板

> 原文：<https://levelup.gitconnected.com/learning-python-the-do-something-a-specified-number-of-times-template-2e05321e5751>

![](img/df02e27b625f8646b7a85ef0b2aeaae8.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将讨论如何使用 Python 中的`for`循环来实现*做指定次数的事情*模板。当您想要将一组任务重复设定的次数时，可以使用此模板。在我的上一篇文章中，我讨论了如何使用 while 循环来执行一组任务，或者执行指定的次数(就像这个模板一样),或者直到满足一个条件。

`for`循环和`while`循环代表了 Python 中执行重复的两个主要结构。

# 做某事指定次数模板

重复的许多用途是基于执行一组任务一组次数。输入 5 个测试等级，计算平均值；获取 20 个单词，按字母顺序排列；或者读取文件中的前 10 条记录并显示每条记录。这些任务都可以用“做指定次数的事情”模板来处理。

以下是该模板的伪代码:

*执行以下给定次数:
要执行的任务*

下面是一个更具体的伪代码示例，用于从用户那里获得五个测试等级:

*做五次:
提示用户测试等级
读取测试等级*

# 范围函数

在我讨论如何使用 for 循环来执行重复之前，我需要介绍一下 for 循环中经常使用的一个重要函数——`range`函数。`range`函数接受一个数字下界和一个数字上界，并返回界内的数字，从下界开始，到上界前一个数字结束。

例如，表达式`range(1,6)`返回`1,2,3,4,5`，而不是`6`。

我将在我的`for`循环示例中广泛使用`range`函数，因为这是在使用`for`循环时指定做某事的次数的最常见方式。

# `for`循环

当您想要一次或多次执行一组语句时，`for`循环是执行重复的主要方式。`for`循环也可以用来处理数据元素，比如字符串和列表，但是我们不会在这里讨论这些用法，所以我们可以专注于我们正在实现的模板。

下面是 for 循环这种特定用法的语法模板:

*对于范围内的变量名(开始，停止):
语句；*

让我们看一个在屏幕上显示五次`“Hello, world!”`的具体例子:

```
for i in range(1,6):
  print("Hello, world!")
```

变量`i`将从`range`函数接收值 1，2，3，4，5，当`i`得到值 6 时循环停止。这意味着循环将重复或迭代 5 次，对值 1 到 5 中的每一个值重复一次。

这个程序的输出是:

```
Hello, world!
Hello, world!
Hello, world!
Hello, world!
Hello, world!
```

没有必要让`range`函数接收值 1 和 6 来控制循环。我可以使用值 0 和 5，或者 2 和 7，或者起始值和终止值之差为 5 的任何其他组合。

# 实现“做指定次数的事情”模板

我们准备实现这个模板来执行一些计算。我将向您展示的第一个示例将让用户输入 5 个考试分数，并计算和显示平均考试分数。程序如下:

```
total = 0
average = 0.0
numGrades = 5
for i in range(1, numGrades+1):
  grade = int(input("Enter a grade: "))
  total = total + grade
average = total / numGrades
print("The average test grade is", average)
```

*执行指定次数*在`for`循环中执行，方法是执行循环中的语句 5 次，以交互方式获得一个分数，并将其添加到中所有分数的总和中以计算平均值。

您会注意到变量`numGrades`存储了要平均的等级数，它也用于控制输入等级的次数。使用变量来表示您希望循环迭代的次数总是一个好主意。这样，如果发生了变化，您所要做的就是改变变量来表示正确的循环迭代次数。

下面是该程序的一次运行:

```
Enter a grade: 81
Enter a grade: 77
Enter a grade: 82
Enter a grade: 89
Enter a grade: 91
The average test grade is 84.0
```

这是另一个实现*做指定次数的事情*模板的例子。问题是建立一个由用户输入的 6 个单词的句子。程序如下:

```
sentence = ""
numWords = 6
for i in range(1, numWords+1):
  word = input("Enter a word: ")
  sentence = sentence + word + " "
print(sentence)
```

模板在`for`循环中实现，就像上面的程序一样。

下面是这个程序的一次运行:

```
Enter a word: The
Enter a word: quick
Enter a word: brown
Enter a word: fox
Enter a word: jumped
Enter a word: over
The quick brown fox jumped over
```

# for 循环还有其他用途

使用`for`循环实现*做指定次数的事情*只是 Python 编程中`for`循环的一种用法。在以后的文章中，我将在实现其他模板时再次使用`for`循环。

感谢您的阅读，请在[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)给我发电子邮件，提出意见和建议。