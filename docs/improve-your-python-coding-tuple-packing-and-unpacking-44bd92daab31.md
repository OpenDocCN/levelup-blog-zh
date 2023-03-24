# 改进您的 Python 编码:元组打包和解包

> 原文：<https://levelup.gitconnected.com/improve-your-python-coding-tuple-packing-and-unpacking-44bd92daab31>

打包和解包确实提高了代码的可读性。让我们回顾一下它们，并学习在元组中使用下划线和星号。

![](img/7141bf0228fc0189c87a6eaecc92589b.png)

照片由[安德鲁·尼尔](https://unsplash.com/@andrewtneel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

元组是 Python 中不可变的数据类型。它们速度更快，内存效率高，并且防止修改。打包和解包可以用于任何可迭代对象，但是其中一个众所周知的用法属于元组对象。使用打包和解包的最大影响之一是，它提高了代码的可读性。

## 什么是元组打包和解包？

让我们从简单的定义开始。

**元组打包**是指将多个值赋给一个元组。

**元组解包**是指将一个元组赋给多个变量。

您已经使用了**元组打包**,因为它很简单:

```
>>> django_movie = ("Tarantino", 2012, 8.4, "Waltz & DiCaprio")
```

我们只是把多个变量，*也就是* `"Tarantino"`，`2012`，`8.4`，`"Waltz & DiCaprio"`赋值到一个元组，*也就是* `django_movie`。

让我们尝试访问`django_movie`的元素:

```
>>> director = django_movie[0]
>>> year = django_movie[1]
>>> imdb_rating = django_movie[2]
>>> stars = django_movie[3]
```

与其这样做，还有一种更 Pythonic 化的处理方式:**元组解包**。

```
>>> director, year, imdb_rating, stars = django_movie
```

如您所见，**元组解包**与**元组打包**完全相反，因此我们将元组的每个元素分配给多个变量。事实上，**元组解包**在 Python 中与一个元组的多次赋值有着相同的含义。

当然，在使用**元组解包**时，有一定的规则必须遵守。请记住，变量的数量必须与元组的长度相同。如果元素的数量过多或过少， **ValueError** 就会出现。

```
>>> director, year, imdb_rating = django_movie
```

**输出:**

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
ValueError: too many values to unpack (expected 3)
```

它说我们试图将一个较大的元组分解成较小数量的变量。

```
>>> director, scenarist, year, imdb_rating, stars = django_movie
```

**输出:**

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
ValueError: not enough values to unpack (expected 5, got 4)
```

这个错误告诉我们，我们试图将一个较小的元组解包成更多的变量。

## 带有下划线“_”的不需要的项目

下划线`_`代表你不关心其值的变量。换句话说，它们是什么并不重要。

假设您不会在项目中使用`imdb_rating`。在这种情况下，你可以用`_`来表明你对`imdb_rating`变量的值不感兴趣。

```
>>> director, year, _, stars = django_movie
```

请记住，`_`是一个有效的变量名，而不是 Python 中的特殊关键字，所以它仍然保存着`imdb_rating`的值。

```
>>> print(_)
```

**输出:**

```
8.4
```

如果想只保留`director`和`stars`怎么办？然后，您将省略`year`和`imdb_rating`，对它们分别使用`_`。

```
>>> director, _, _, stars = django_movie
```

您可以使用`_`指定您想要忽略的所有值。

但这看起来不妙。对吗？

## 用星号' * '运算符扩展解包

`*`操作符用于扩展解包。

```
>>> director, *_, stars = django_movie
```

我们可以打印出它们的值来看看发生了什么。

```
>>> print(director)
>>> print(stars)
```

**输出:**

```
Tarantino
Waltz & DiCaprio
```

让我们看进一步的例子。

```
>>> numbers = (1, 2, 3, 4, 5)
>>> first, *others = numbers
>>> print(first)
>>> print(others)
```

**输出:**

```
1
[2, 3, 4, 5]
```

注意，当我们使用`*`操作符时，返回的是一个列表，而不是一个元组。

```
>>> first, *others, last = numbers
>>> print(first)
>>> print(others)
>>> print(last)
```

**输出:**

```
1
[2, 3, 4]
5
```

最后一个例子:

```
>>> *others, last = numbers
>>> print(others)
>>> print(last)
```

**输出:**

```
[1, 2, 3, 4]
5
```

希望这篇文章对你有用。

您可能感兴趣的另一篇有用的文章:

[](https://python.plainenglish.io/9-python-tricks-to-become-a-better-data-scientist-eb26970b4160) [## 成为更好的数据科学家的 9 个 Python 技巧

### 你应该知道这些可怕的技巧，以便拥有更好的数据科学家职业生涯

python .平原英语. io](https://python.plainenglish.io/9-python-tricks-to-become-a-better-data-scientist-eb26970b4160)