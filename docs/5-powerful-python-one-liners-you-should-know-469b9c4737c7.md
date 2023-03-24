# 您应该知道的 5 个强大的 Python 一行程序

> 原文：<https://levelup.gitconnected.com/5-powerful-python-one-liners-you-should-know-469b9c4737c7>

## 如果没有 map()函数和理解，我真不知道该怎么办。

![](img/930b863985743531b258dc6c5919923f.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我喜欢用 Python，因为它简单。我总是着迷于用一条线解决复杂的问题。当然，Python 中的大多数一行程序都是用`map()`和 comprehensions 编写的。它们有利于用一条线解决你的问题。

您还可以使用`map()`和列表理解来提高 Python 程序的效率。我就这个问题写过一篇文章:

[](https://python.plainenglish.io/5-ways-to-make-your-python-code-faster-463a3c946534) [## 提高 Python 代码速度的 5 种方法

### 让我们来学习如何让 Python 更快！

python .平原英语. io](https://python.plainenglish.io/5-ways-to-make-your-python-code-faster-463a3c946534) 

我们开始吧！

## 1-列表中所有项目的类型转换

当您需要更改列表中值的类型时，这很有用。

**例 1:**

```
>>> list(map(int, ['1', '2', '3']))
```

**输出:**

```
[1, 2, 3]
```

**例 2:**

当我们想要制作一个包含异质同质项目的列表时，这也很有用。

```
>>> list(map(float, ['1', 2, '3.0', 4.0, '5', 6]))
```

**输出:**

```
[1.0, 2.0, 3.0, 4.0, 5.0, 6.0]
```

## 2-整数的位数之和

你不必使用数学运算符，即`/`(除法)和`%`(模)运算符来计算一个整数的位数之和。有另一种方法可以做到这一点。如果我们把数字转换成字符串，一切就变得容易了。但是怎么做呢？我们已经知道字符串是可迭代的，所以我们可以用`map()`函数迭代字符串中的字符。我们基本上会用这个逻辑。

```
>>> sum_of_digits = lambda x: sum(map(int, str(x)))
```

**例子:**

```
>>> print(sum_of_digits(1789))
```

**输出:**

```
25
```

## 3-包含子列表的平面列表

假设我们有一个这样的列表:

```
>>> l = [[1, 2, 3], [4, 5], [6], [7, 8], [9]]
```

我们希望简化列表，使结果变成:

```
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

这是首先想到的一种方法:

但是我们可以进一步应用列表理解来改进我们的代码:

```
>>> flattened_list = [item for sublist in l for item in sublist]
```

**例如:**

```
>>> l = [[1, 2, 3], [4, 5], [6], [7, 8], [9]]>>> flattened_list = [item for sublist in l for item in sublist]
>>> print(flattened_list)
```

**输出:**

```
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 矩阵的 4-转置

使用`zip()`功能是一个很好的做法。

```
>>> transpose_A = [list(i) for i in zip(*A)]
```

**例如:**

```
>>> A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]>>> transpose_A = [list(i) for i in zip(*A)]
```

让我们使用`numpy`来打印结果。打印二维数组很有用。

**输出:**

```
[[1 2 3]
 [4 5 6]
 [7 8 9]][[1 4 7]
 [2 5 8]
 [3 6 9]]
```

## 5-交换字典中的键和值

假设我们将人员数据存储在一个`dict`中，并希望交换键-值对。

```
>>> staff = {'Data Scientist': 'John', 'Django Developer': 'Jane'}
```

我们可以使用字典理解来交换键值对。

```
>>> staff = {i:j for j, i in staff.items()}
```

让我们打印结果:

```
>>> print(staff)
```

**输出:**

```
{'John': 'Data Scientist', 'Jane': 'Django Developer'}
```

就是这样！希望这篇文章对你有用。

要不你再看一篇有用的文章？

[](https://python.plainenglish.io/11-python-tricks-to-boost-your-python-skills-significantly-1a5221dfa5c7) [## 显著提升你的 Python 技能的 11 个 Python 技巧

### 你不会相信有了这些技巧，Python 中的事情会变得多么简单！

python .平原英语. io](https://python.plainenglish.io/11-python-tricks-to-boost-your-python-skills-significantly-1a5221dfa5c7)