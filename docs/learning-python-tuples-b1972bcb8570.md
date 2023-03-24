# 学习 Python:元组

> 原文：<https://levelup.gitconnected.com/learning-python-tuples-b1972bcb8570>

![](img/74c6d429bccd7603c0c7e576cf24f53a.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

元组是最不为人知和最难理解的数据结构之一，但是当列表或字典不太正确时，元组在某些情况下会很有用。在本文中，我将讨论什么是元组以及如何在 Python 中使用它们。

# 定义和创建的元组

元组(发音为“too-ple”或“tuh-ple”)是一个逗号分隔的值列表。当您希望在一个对象中存储相关信息时，可以使用元组。例如，您可以在一个元组中存储学生的姓名和他们在一次测试中的成绩。这比在列表中存储这样的数据更有意义，因为通常列表的元素都具有相同的数据类型。您还可以将这些数据存储在一个字典中，将学生姓名作为键，将成绩列表作为值，但是检索单个成绩比使用元组要困难得多。

元组的另一个有趣的用途是用于执行值交换，我们将在文章的最后看看它是如何工作的。

元组最重要的特征是它们是不可变的。这意味着如果不重新分配完整的元组，就不能改变元组中的一个或多个元素，就像处理字符串一样。

元组是通过向变量分配逗号分隔的列表来创建的。这里有一个例子:

```
letters = 'a', 'b', 'c', 'd','e'
```

因为这看起来很奇怪，更常见的是将元组创建为括号内的列表，如下所示:

```
letters = ('a', 'b', 'c', 'd','e')
```

您可以通过使用元组变量作为参数调用 type 函数来验证对象是否为元组:

```
>>> letters = ('a','b','c','d','e')
>>> letters
('a', 'b', 'c', 'd', 'e')
>>> print(type(letters))
<class 'tuple'>
```

但是，请注意，仅将单个值放在括号中并不能使其成为元组:

```
>>> notTuple = ('z')
>>> print(type(notTuple))
<class 'str'>
```

多添加一个值确实会使对象成为元组:

```
>>> aTuple = ('y','z')
>>> print(type(aTuple))
<class 'tuple'>
```

也可以用`tuple`函数创建一个元组。下面是一个创建空元组的示例:

```
>>> newTuple = tuple()
>>> print(type(newTuple))
<class 'tuple'>
```

使用`tuple`函数，您可以从单个字符序列(比如一个字符串)创建一个元组。例如:

```
>>> wordTuple = tuple("hello")
>>> print(type(wordTuple))
<class 'tuple'>
```

否则，对象`wordTuple`将保存一个字符串而不是一个元组。

# 使用元组

您可以使用索引来访问元组值，如下例所示:

```
>>> letters = ('a','b','c','d','e')
>>> print(letters[0], letters[1])
a b
```

切片操作符也可以工作:

```
>>> letters
('a', 'b', 'c', 'd', 'e')
>>> print(letters[2:4])
('c', 'd')
```

您可以使用关系运算符来比较元组。Python 将基于所使用的关系运算符进行逐个元素的比较。例如:

```
>>> grades1 = (91, 88, 84)
>>> grades2 = (91, 89, 84)
|>>> grades1 > grades2
False
>>> grades1 < grades2
True
```

第二元组大于第一元组，因为第二元素`grades2`89 大于第二元素`grades1`88。

元组可以是函数的返回值。这很有趣，因为函数只能返回一个值。当您需要从一个函数返回两个或多个值时，您可以将这些值放入一个元组中并返回该元组。

这里有两个例子。第一个示例返回数字列表中的最大值和最小值:

```
from random import seed
from random import randintdef minMax(lyst):
  return max(lyst), min(lyst)numbers = []
for i in range(1, 11):
  numbers.append(randint(1,100))
print(numbers)
print("Max/min values:",minMax(numbers))
```

这个程序的输出是:

```
[24, 72, 57, 83, 38, 52, 32, 37, 19, 45]
Max/min values: (83, 19)
```

# 使用元组作为变长函数参数

通过使用`*`操作符，您可以编写一个接受可变数量参数的函数。这个操作符收集了一个函数的所有参数，并将它们放入一个元组中。然后可以迭代元组来访问参数。

下面是一个示例，它实现了一个对不同数量的参数求和的函数。函数定义使用了`len`函数，它作用于元组就像作用于列表和字典一样。下面是函数定义和一个测试程序:

```
def sum(*numlist):
  total = 0
  for i in range(0, len(numlist)):
    total += numlist[i]
  return totalprint(sum(1,2,3))
print(sum(1,2,3,4,5))
print(sum(1,2,3,4,5,6,7,8,9,10))
```

# 元组、列表和 zip 函数

`zip`函数获取两个列表，并通过将每个列表的连续元素放在一起形成一个元组来构建一组元组。例如，如果一个列表是`[“Jones”,”Smith”]`，另一个列表是`[88, 81]`，那么得到的元组是`((“Jones”, 88), (“Smith”, 81))`。

下面是说明该示例的代码:

```
names = ["Jones","Smith"]
grades = [88,81]
roster = zip(names, grades)
for student in roster:
  print(student)
```

这个程序的输出是:

```
('Jones', 88)
('Smith', 81)
```

# 元组、字典和项目方法

`items`方法将字典中的键/值对作为元组序列返回。例如:

```
>>> grades = {'Smith':88, 'Jones':81, 'Brown':90}
>>> print(grades.items())
dict_items([('Smith', 88), ('Jones', 81), ('Brown', 90)])
```

您可以使用它来遍历字典，并在遍历过程中打印键/值对:

```
>>> grades
{'Smith': 88, 'Jones': 81, 'Brown': 90}
>>> for student,grade in grades.items():
...   print(student,":",grade)
...
Smith : 88
Jones : 81
Brown : 90
```

# 使用元组交换变量值

交换两个变量的值通常涉及到第三个变量。下面的程序演示了这一点:

```
a = 1
b = 2
print("a:",a,"b:",b)
temp = a
a = b
b = temp
print("a:",a,"b:",b)
```

这个程序的输出是:

```
a: 1 b: 2
a: 2 b: 1
```

通过使用元组，您可以在不需要临时变量的情况下完成同样的事情。您可以通过创建元组`(b,a)`并将其分配给元组`(a,b)`来实现这一点。这个程序是这样的:

```
a = 1
b = 2
print("a:",a,"b:",b)
a,b = b,a
print("a:",a,"b:",b)
```

# 排序和反转元组

内置函数`sort`和`reverse`不处理元组，但是还有另外两个函数`sorted`和`reversed`处理元组，尽管这两个函数都将排序或反转的元组作为列表返回。

让我们从排序一个元组开始。下面是一个程序，它构建一个由十个元素组成的随机数元组，然后使用`sorted`函数将该元组按升序排序:

```
from random import seed
from random import randintnumbers = []
for i in range(1,11):
  numbers.append(randint(1,100))
nums = tuple(numbers)
print(nums)
sortedNums = sorted(nums)
print(sortedNums)
```

这个程序的输出是:

```
(39, 74, 11, 95, 51, 47, 53, 19, 100, 89)
[11, 19, 39, 47, 51, 53, 74, 89, 95, 100]
```

现在，假设我们希望看到降序排列的元组。我们可以通过使用`reversed`函数反转元素的顺序来实现。这个函数返回一个迭代器，允许以相反的顺序访问给定的序列。在调用函数之后，你还需要将对象转换成一个列表，以便于访问。

下面是前面的程序，最后添加了对`reversed` 函数的调用:

```
from random import seed
from random import randintnumbers = []
for i in range(1,11):
  numbers.append(randint(1,100))
nums = tuple(numbers)
print(nums)
sortedNums = sorted(nums)
print(sortedNums)
numsSorted = tuple(sortedNums)
nums = tuple(numsSorted)
descNums = list(reversed(nums))
print(descNums)
```

下面是这个程序运行一次的输出:

```
(77, 53, 48, 98, 15, 41, 93, 100, 75, 15)
[15, 15, 41, 48, 53, 75, 77, 93, 98, 100]
[100, 98, 93, 77, 75, 53, 48, 41, 15, 15]
```

我对元组的讨论到此为止。元组不是 Python 中最常用的数据结构，但是对于交换变量和变长函数参数之类的东西有一些明确的用途。

如果您以前从未使用过元组，请尝试一下，看看它们如何简化您的 Python 程序。

感谢您的阅读，请将您的意见和建议发邮件至 mmmcmillan1@att.net[给我。](mailto:mmmcmillan1@att.net)