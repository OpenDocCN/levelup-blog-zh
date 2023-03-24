# 学习 Python:搜索列表

> 原文：<https://levelup.gitconnected.com/learning-python-search-a-list-de6d98aea418>

![](img/f5c4484dea5ecd6cb4c683295392a588.png)

科琳·库兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当数据存储在列表中时，您通常需要搜索列表以查看特定值是否存储在列表中。本文介绍了处理这项任务的模板——*搜索列表*模板。在文章的最后，我将向您展示如何使用内置的 Python 函数来执行这项任务，而无需编写自己的函数。

# 搜索列表模板

*搜索列表*模板实现列表的顺序或线性搜索。这意味着搜索从列表的第一个元素开始，在找到要搜索的元素或搜索到达列表末尾时结束。

我要用一个函数来实现这个模板。如果搜索成功，模板的一个版本将返回真值，如果搜索失败，它将返回假值。在第二个版本中，如果搜索成功，函数将返回找到的值的位置，如果搜索失败，函数将返回`-1`。失败的搜索返回`-1`的原因是它不是有效的列表位置。

下面是*搜索列表*模板的伪代码，使用了一个`while`循环，返回真或假:

*found = false
初始 list-position-variable 为 0
而未找到所搜索的值:
如果到达列表末尾:
表示搜索失败
如果找到元素:
表示搜索成功
将 list-position-variable 递增 1*

您也可以使用一个`for`循环来实现这个模板，我将把它留给读者作为练习。

下面是返回列表位置或`-1`的版本的伪代码，这次使用了 for 循环:

*对于列表中的每个元素:
如果找到元素:
将 foundPos 设置为元素位置
中断循环
返回 foundPos*

过早地跳出循环是有争议的，但是我相信效率高于软件工程纯粹主义，所以我在伪代码中加入了一个 break。

# 实施搜索列表模板—返回真或假

现在让我们来看看实现上面给出的两个伪代码示例中的第一个。这个模板最好用一个函数来实现，在这个函数中，我们把想要搜索的列表和要搜索的值作为参数传入。如果在列表中找到该值，则该函数返回 true 值，否则返回 false 值。

第一个实现是针对返回布尔值的`while`循环版本。下面是函数定义:

```
def find(list, value):
  found = False
  listPos = 0
  while not found:
    if listPos == len(list):
      return found
    elif list[listPos] == value:
      found = True
      return found
    else:
      listPos = listPos+1
```

让我们测试一下这个函数，用它来搜索一个随机生成的数字列表。程序是这样的:

```
from random import randintgrades = []
numGrades = 20
for i in range(0, numGrades):
  grades.append(randint(1,101))
  print(grades)
searchVal = int(input("Enter a value to search for: "))
if (find(grades, searchVal)):
  print(searchVal,"is in grades.")
else:
  print(searchVal,"is not in grades.")
```

下面是该程序两次运行的输出:

```
[83, 73, 81, 97, 56, 13, 53, 46, 77, 90, 40, 81, 5, 65, 5, 56, 6, 82, 45, 31]
Enter a value to search for: 77
77 is in grades.
[91, 12, 32, 56, 64, 35, 96, 30, 17, 82, 97, 96, 82, 47, 98, 83, 50, 101, 48, 71]
Enter a value to search for: 55
55 is not in grades.
```

现在让我们编写一个带有`for`循环的`find`函数版本:

```
def find1(list, value):
  for i in range(0, len(list)):
    if list[i] == value:
      return True
  return False
```

注意，这一次我没有使用布尔变量，而是在列表中找到要搜索的值时返回`True`。如果搜索不成功，函数自动返回`False`，因为该返回语句是函数的最后一行。

有些人认为这是糟糕的编程风格，因为函数中有两个 return 语句。其他人只是认为这是编写函数的一种更有效的方式。你必须明白，一旦一个函数命中了 return 语句，函数中的其他语句都不会被执行。我不会判断哪个版本是最好的，因为两种方法都可以完成工作，但只返回 true 或 false 值肯定更有效，代码更少，但代码也同样容易理解。

# 实现搜索列表模板—返回位置或-1

现在让我们看看如何实现 *Search a List* 模板，该模板返回值在列表中的位置，或者如果值不在列表中，则返回一个`-1`。我将使用一个`while` 循环和一个`for`循环来实现这个函数。

下面是`find` 函数的`while` 循环版本:

```
def find(list, value):
  pos = 0
  while pos < len(list):
    if list[pos] == value:
      return pos
    pos = pos + 1
  return -1
```

该函数返回列表中找到被搜索值的位置，或者该函数返回`-1`。

下面是测试该功能的程序:

```
grades = []
numGrades = 20
for i in range(0, numGrades):
  grades.append(randint(1,101))
  print(grades)
searchVal = int(input("Enter a value to search for: "))
position = find(grades, searchVal)
if (position > -1):
  print("Found",searchVal,"at position",position)
else:
  print(searchVal,"is not in the list.")
```

下面是该程序两次运行的输出:

```
[92, 37, 64, 59, 48, 61, 56, 8, 59, 10, 20, 29, 75, 39, 49, 30, 75, 62, 12, 52]
Enter a value to search for: 8
Found 8 at position 7[20, 61, 72, 84, 28, 56, 11, 5, 17, 74, 73, 30, 17, 1, 66, 6, 76, 53, 74, 53]
Enter a value to search for: 10
10 is not in the Now let's write a for loop version:list.
```

现在让我们写一个`for`循环版本:

```
def find(list, value):
  for i in range(0, len(list)):
    if list[i] == value:
      return i
  return -1There's really not much difference between the two functions, though most programmers would write this function with a for loop as for loops are the most natural way to traverse lists.You can plug this function into the program above and you'll get similar results. The results won't necessarily be the same because the program uses random number generation for creating the lists.
```

# in 函数

Python 提供了一个内置函数`in`，可以用来判断一个列表是否包含值。以下是`in`函数的语法模板:

*列表名称中的布尔数据值*

`in`函数看起来不像传统的函数，其操作更像 C++中的条件运算符。`in`关键字的左边是要搜索的值，右边是要搜索的列表。如果在列表中找到该值，函数返回`True`；否则函数返回`False`。

以下是演示如何使用`in`功能的程序:

```
from random import randint
from random import seedseed(1)
numbers = []
for i in range(1,11):
  numbers.append(randint(1,100))
  print(numbers)
value = int(input("Enter a number to search for: "))
if value in numbers:
  print(value,"is in numbers.")
else:
  print(value,"is not in numbers.")
```

以下是该程序几次运行的输出:

```
[18, 73, 98, 9, 33, 16, 64, 98, 58, 61]
Enter a number to search for: 33
33 is in numbers.[18, 73, 98, 9, 33, 16, 64, 98, 58, 61]
Enter a number to search for: 15
15 is not in numbers.
```

和往常一样，你应该使用语言提供的内置函数，而不是你自己编写的函数，但是用你自己的代码重新实现一种语言的函数是成为更好的程序员的一种方式，这是非常有启发性的。

这篇关于用 Python 实现搜索列表模板的文章到此结束。请务必发送电子邮件至 mmmcmillan1@att.net 的[，告诉我您的意见和建议。](mailto:mmmcmillan1@att.net)