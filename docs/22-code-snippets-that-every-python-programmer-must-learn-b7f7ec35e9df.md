# 每个 Python 程序员都必须学习的 22 个代码片段

> 原文：<https://levelup.gitconnected.com/22-code-snippets-that-every-python-programmer-must-learn-b7f7ec35e9df>

## 使用这些代码片段来解决日常问题

![](img/f0a079c0a55085f9656a5ea7149461f1.png)

由 [Toa Heftiba](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

近年来，Python 的用户数量有了巨大的增长。初级程序员选择 Python 作为他们的第一语言，因为它有简单的语法和应用。

在本文中，我将分享一些可以用来解决 python 日常问题的 Python 代码片段。我们开始吧！

# 1.合并两本词典

在`Python 3.5`之后，合并多个字典变得更加容易。我们可以使用`(**)`操作符将多个字典合并成一行。我们需要在{}中传递字典以及(**)操作符，这样就完成了。

> 语法:{ * *词典 1，* *词典 2}

```
Output:Merged dictionary: {'name': 'Joy', 'age': 25, 'city': 'New York'}
```

# 2.链式比较

这个代码片段允许您在一行中对所有类型的操作符进行多重比较。

```
Output:True
False
```

# 3.打印一个字符串 N 次

我们可以使用这个代码片段输出一个字符串 N 次，而不使用任何循环。

```
Output:
Hello!Hello!Hello!Hello!Hello!
```

# 4.检查文件是否存在

在进行文件处理和任何其他操作时，有必要知道我们在代码中使用的文件是否存在。使用这个代码片段，我们可以知道文件是否存在于我们的目录或给定的路径中。

```
Output:Does file exist: False
```

# 5.检索列表的最后一个元素

我们可以使用以下方法从列表中检索最后一个元素。

# 6.列表理解

List comprehension 可以用来在一行代码中基于现有列表的元素创建一个新列表。

```
Output:Vowels are:  ['i', 'i', 'o', 'e', 'a', 'o', 'i']
```

# 7.计算代码执行时间

我们可以使用`time`库来计算执行特定代码所需的时间。

```
Output:Sum: 45
Time:  0.0009965896606445312
```

# 8.查找出现次数最多的元素

该代码片段返回列表中出现最频繁的项目。

```
Output:most frequent item is: 2
```

# 9.将两个列表转换成字典

这段代码可以用来将两个列表转换成一个字典。在这个方法中，我们将两个列表作为输入值。第一个列表将是字典的键，另一个列表中的值将是字典的值。

```
Output:{1: 'one', 2: 'two', 3: 'three'}
```

# 10.错误处理

和其他编程语言一样，Python 也提供了一种使用`try`、`except`和`finally`块处理异常的方法。

```
Output:Can not divide by zero
Executing finally block
```

# 11.反转字符串

我们可以使用 Python 切片操作来反转字符串。这也是反转字符串的最有效和最直接的方法。

```
Output:Reversed string is: dlroW olleH
```

# 12.将字符串列表组合成单个字符串

我们可以使用`join()`函数将一列字符串组合成一个单独的字符串。

```
Output:Hello world Ok Bye!
```

# 13.获取缺失键的默认值

在 Python 字典中，`get()`方法用于从字典中检索具有指定键的项。如果字典中不存在这个键，那么我们可以返回这个键的默认值。

```
Output:three
Original dictionary: {1: 'one', 2: 'two', 4: 'four'}
```

# 14.在没有额外变量的情况下交换两个值

这段代码可以用来交换两个值，而不需要额外的变量。

# 15.正则表达式

正则表达式(或 RE)指定一组与之匹配的字符串。模块中的函数让你检查一个特定的字符串是否匹配一个给定的正则表达式。

```
Output:True
```

# 16.回文

这个片段可以用来检查给定的字符串是否是回文。回文是一个单词或一系列字符，前后读起来是一样的。

```
Output:True
False
```

# 17.过滤值

这个`filter()`方法在一个函数的帮助下过滤 iterable，该函数检查 iterable 中的每个值是否为真。

```
Output:[1, 3, 7, 9, 11]
```

# 18.字谜

此方法可用于确定两个字符串是否是变位词。变位词是通过重新排列不同单词或短语的字母而形成的单词或短语。

```
Output:True
```

# 19.变量的内存使用

这段代码可以用来检查一个对象的内存使用情况。

```
Output:28
104
```

# 20.链式函数调用

使用这个代码片段，您可以在一行中调用多个函数。

```
Output:15
```

# 21.获取对象的唯一 ID

`id()`方法可以用来访问一个对象的唯一 id。您需要在方法中传递对象名称，它将返回该对象的唯一 id。

```
Output:1830078640
1452073210688
1452054478728
```

# 22.从列表中删除重复项

这些方法可用于从列表中删除重复值。

```
Output:[1, 2, 3, 4, 'Ana', 'John', 'Mark']
[1, 2, 3, 4, 'John', 'Ana', 'Mark']
```

# 结论

这就是这篇文章的全部内容。我们讨论了一些代码片段，我发现它们非常有用，可以用在日常问题中。您可以在日常编码和竞争性编程问题中使用这些代码片段，以加快您的工作并使您的代码更快。

感谢阅读！

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev) 

> *走之前……*

如果你喜欢这篇文章，并希望**继续关注**更多**精彩的**文章，请考虑使用我的推荐链接[https://pralabhsaxena.medium.com/membership](https://pralabhsaxena.medium.com/membership)成为一名中级会员。

另外，你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)。