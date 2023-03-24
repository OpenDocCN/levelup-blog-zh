# Python 速记技术将节省您的时间

> 原文：<https://levelup.gitconnected.com/python-shorthand-techniques-that-will-save-your-time-ce774b2a163c>

## 每个开发人员都应该知道的 Python 速记技术

![](img/f0fa7718c6b7db96ed9514e9568d4061.png)

照片由[罗曼·士林](https://unsplash.com/@romashilin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将分享一些 python 速记方法和技术，它们可以用来解决 Python 中的日常问题。我们开始吧！

# 1.交换两个值

Python 提供了一种简单而直观的方法来交换一行中的两个变量，而无需使用额外的变量。

```
Output:
a, b = 10, 5
```

# 2.反转字符串

在 python 中，我们可以使用切片操作轻松地反转字符串。这是在 python 中反转字符串的最有效和最直接的方法之一。

```
Output:Reversed string is: dlroW olleH
```

# 3.将字符串列表组合成单个字符串

在 python 中，我们可以使用`join()`函数将一列字符串组合成一个单独的字符串。

```
Output:Hello world Ok Bye!
```

# 4.查找出现次数最多的元素

这段代码可以用来查找列表中最频繁出现的元素。

```
Output:most frequent item is: 2
```

# 5.变量的内存使用

要检查一个变量或对象的内存使用情况，我们可以按照下面的方式使用`getsizeof()`方法。

```
Output:28
96
61
```

# 6.打印一个字符串 N 次

我们可以简单地使用`*`操作符打印字符串 N 次。使用下面的例子，我们可以不使用任何循环打印一个字符串 N 次。

```
Output:Hello!Hello!Hello!Hello!Hello!
```

# 7.字谜

我们可以使用**集合**包中的`Counter()`函数来检查两个单词是否是变位词。变位词是通过重新排列不同单词或短语的字母而形成的单词或短语。

```
Output:
True
True
False
```

# 8.查找导入模块的文件路径

我们可以用下面的方法找到导入模块的路径。首先，我们需要导入模块，然后将模块名传递给 print()函数，它将返回模块在设备中的路径。

```
Output:<module 'os' from '/usr/lib/python3.6/os.py'>
<module 'pandas' from '/usr/lib/python3.6/pandas.py'>
```

# 9.转置矩阵

```
Output:Before transpose:
[1, 2, 3]
[4, 5, 6]After transpose:
[1, 4]
[2, 5]
[3, 6]
```

# 10.Zip 功能

Python `zip()`函数将多个可迭代对象作为输入，然后返回一个迭代器对象，从所有这些输入迭代器映射值。

```
Output:The zipped result is: {(1, 'One'), (4, 'Four'), (3, 'Three'), (2, 'Two')}
```

# 11.将列表转换为单个列表

我们可以使用这个代码片段将一个列表转换成一个列表。

```
Output:[-2, -3, 10, 30, 'apple', 'orange']
```

# 12.将两个列表转换成字典

```
Output:{'Name': 'Roy', 'Age': 26, 'City': 'New York'}
```

# 13.λ函数

在 python 中，lambda 函数是没有名字的匿名函数。这个函数提供了一种用 Python 编写闭包的好方法。

```
Output:[33, 90, 21, 60]
```

# 14.合并两本词典

我们可以使用`(**)`操作符将多个字典合并成一行。我们需要在{}中传递字典和`(**)`操作符，字典将被合并。

```
Output:Merged dictionary is: 
{'name': 'Jeff', 'age': 26, 'city': 'New York'}
```

# 15.计算列表中元素的出现次数

```
Output:{'B': 5, 'A': 3, 'C': 2}
{5: 3, 1: 2, 6: 2, 2: 1, 3: 1, 4: 1}
```

# 16.列表理解

列表理解用于基于现有列表的项目创建新列表。

```
Output:Even numbers are: [0, 2, 4, 6, 8]
Squared numbers are: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

# 17.检查回文

```
Output:True
False
```

# 18.从列表中删除重复项

这些方法可用于从列表中移除重复的元素。

```
Output:[1, 2, 3, 4, 'John', 'Mark', 'Ana']
[1, 2, 3, 4, 'John', 'Ana', 'Mark']
```

# 19.从函数中返回多个值

我们可以通过以下方式从 python 函数中返回多个值。

```
Output:30
10
200
```

# 20.从列表中检索最后一个元素

使用这些方法，我们可以检索列表的最后一个元素。

```
Output:pineapple
pineapple
pineapple
```

# 结论

这就是这篇文章的全部内容。在本文中，我分享了一些 python 速记技巧，这些技巧对于解决 python 中的日常问题非常有用。练习这些代码片段，使它们便于使用。

感谢阅读！

> *在你走之前……*

如果你喜欢这篇文章，并希望**继续关注更多**精彩**文章的**——请考虑使用我的推荐链接:[**https://pralabhsaxena.medium.com/membership**](https://pralabhsaxena.medium.com/membership)成为中级会员。

还有，你可以在这里免费订阅我的简讯: [**Pralabh 的简讯**](https://pralabhsaxena.medium.com/subscribe) 。