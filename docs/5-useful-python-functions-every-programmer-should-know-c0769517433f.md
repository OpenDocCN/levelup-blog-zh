# 每个程序员都应该知道的 5 个有用的 Python 函数

> 原文：<https://levelup.gitconnected.com/5-useful-python-functions-every-programmer-should-know-c0769517433f>

## 在本教程中，我们将学习每个程序员都应该知道的五个有用的 Python 函数。

![](img/bb78387f0639a5bc7db5b08af277301f.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 拍摄的照片

Python 是一种功能强大的编程语言，具有广泛的应用。对于初学者来说，它很容易学习，并且有许多模块和库允许健壮的编程。

在本教程中，我们将学习每个程序员都应该知道的五个有用的 Python 函数。这些功能是:

-map()
-filter()
-reduce()
-zip()
-enumerate()

我们将通过例子来展示每个函数，告诉你如何在你的代码中使用它们。我们开始吧！

**地图()**

***map()*** 函数用于将函数应用于元素序列。也就是说，它将一个函数和一个序列作为参数，并返回一个新的序列，该序列由应用于原始序列的每个元素的函数的返回值组成。

例如，假设我们有一个数字列表，我们想对列表中的每个数字求平方。我们可以使用 map()函数来实现这一点，如下所示:

```
 >>> numbers = [1, 2, 3, 4, 5]
>>> def square(x):
… return x * x
… 
**>>> map(square, numbers)
[1, 4, 9, 16, 25]** 
```

可以看到， **map()** 函数以 **square()** 函数和数字列表作为参数，返回一个新的列表，其中包含原列表中每个数字的平方。

如果我们有一个字符串列表，我们可以使用 **map()** 函数对列表中的每个字符串应用一个函数。

例如，假设我们有一个单词列表，我们想计算每个单词的长度。我们可以使用 map()函数来实现这一点，如下所示:

```
 >>> words = [‘cat’, ‘dog’, ‘elephant’]
>>> def length(word):
…     return len(word)
… 
**>>> map(length, words)
[3, 3, 8]** 
```

可以看到， **map()** 函数以 **length()** 函数和单词列表作为参数，返回一个新的列表，其中包含原列表中每个单词的长度。

**过滤器()**

函数的作用是过滤一系列的元素。也就是说，它将一个函数和一个序列作为参数，并返回一个新的序列，该序列只包含原始序列中函数返回 True 的那些元素。

例如，假设我们有一个数字列表，我们想过滤掉奇数。我们可以使用 filter()函数来做到这一点，如下所示:

```
 >>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> def is_even(x):
…     return x % 2 == 0
… 
**>>> filter(is_even, numbers)
[2, 4, 6, 8, 10]** 
```

如您所见， **filter()** 函数将 **is_even()** 函数和数字列表作为参数，并返回一个仅由原始列表中的偶数组成的新列表。

如果我们有一个字符串列表，我们可以使用 **filter()** 函数过滤掉不是回文的字符串。

例如，假设我们有一个单词列表，我们想过滤掉不是回文的单词。我们可以使用如下的**过滤器()**函数来实现:

```
 >>> words = [‘cat’, ‘dog’, ‘elephant’, ‘madam’, ‘racecar’]
>>> def is_palindrome(word):
… return word == word[::-1]
… 
**>>> filter(is_palindrome, words)
[‘madam’, ‘racecar’]** 
```

如您所见，**过滤器()**函数将**is _ 回文()f** 函数和单词列表作为参数，并返回一个只包含回文单词的新列表。

**reduce()**

**reduce()** 函数用于将函数应用于一系列元素。也就是说，它将一个函数和一个序列作为参数，并返回一个值，该值是将函数应用于序列元素的结果。

例如，假设我们有一个数字列表，我们想计算列表中所有数字的乘积。我们可以使用 reduce()函数来实现这一点，如下所示:

```
 >>> numbers = [1, 2, 3, 4, 5]
>>> from functools import reduce
>>> def product(x, y):
…      return x * y
… 
**>>> reduce(product, numbers)
120** 
```

可以看到， **reduce()** 函数将 **product()** 函数和数字列表作为参数，返回列表中所有数字的乘积。

如果我们有一个字符串列表，我们可以使用 ***reduce()*** 函数来连接列表中的所有字符串。

例如，假设我们有一个单词列表，我们希望将所有单词连接成一个字符串。我们可以使用 reduce()函数来实现这一点，如下所示:

```
 >>> words = [‘cat’, ‘dog’, ‘elephant’]
>>> from functools import reduce
>>> def concatenate(x, y):
… return x + y
… 
**>>> reduce(concatenate, words)
‘catdogelephant’** 
```

如您所见， ***reduce()*** 函数将***concatenate()***函数和单词列表作为参数，返回一个字符串，该字符串是列表中所有单词的连接。

**zip()**

***zip()*** 函数用于压缩两个或多个序列。也就是说，它将两个或更多序列作为参数，并返回一个新序列，该序列由第一个序列的元素与第二个序列的元素配对组成。

例如，假设我们有两个数字列表，我们想把它们压缩在一起。我们可以使用 zip()函数来做到这一点，如下所示:

```
 >>> numbers1 = [1, 2, 3, 4, 5]
>>> numbers2 = [6, 7, 8, 9, 10]**>>> zip(numbers1, numbers2)
[(1, 6), (2, 7), (3, 8), (4, 9), (5, 10)]** 
```

如您所见， **zip()** 函数将 numbers1 和 numbers2 列表作为参数，并返回一个由 numbers1 的元素和 numbers2 的元素组成的新列表。

如果我们有一个字符串列表，我们可以使用 zip()函数将它们压缩在一起。

例如，假设我们有一个单词列表，我们想把它们压缩在一起。我们可以使用 zip()函数来做到这一点，如下所示:

```
 >>> words = [‘cat’, ‘dog’, ‘elephant’]**>>> zip(words)
[(‘cat’,), (‘dog’,), (‘elephant’,)]** 
```

正如您所看到的，zip()函数将单词列表作为一个参数，并返回一个由单词列表中的元素成对组成的新列表。

**枚举()**

***枚举()*** 函数用于枚举一个序列。也就是说，它将一个序列作为参数，并返回一个由原始序列的元素及其索引组成的新序列。

例如，假设我们有一个数字列表，我们想要枚举它们。我们可以使用 ***enumerate()*** 函数来实现，如下所示:

```
 >>> numbers = [1, 2, 3, 4, 5]**>>> list(enumerate(numbers))
[(0, 1), (1, 2), (2, 3), (3, 4), (4, 5)]** 
```

如您所见，enumerate()函数将 numbers 列表作为一个参数，并返回一个新列表，该列表由原始列表中的元素及其索引组成。

如果我们有一个字符串列表，我们可以使用 ***enumerate()*** 函数来枚举它们。例如，假设我们有一个单词列表，我们想要枚举它们。我们可以使用 ***enumerate()*** 函数来实现，如下所示:

```
 >>> words = [‘cat’, ‘dog’, ‘elephant’]**>>> list(enumerate(words))
[(0, ‘cat’), (1, ‘dog’), (2, ‘elephant’)]** 
```

如您所见， ***enumerate()*** 函数将单词列表作为参数，并返回一个新列表，该列表由原始列表中的元素及其索引组成。

在本教程中，我们学习了每个程序员都应该知道的五个有用的 Python 函数。这些功能是:

-map()
-filter()
-reduce()
-zip()
-enumerate()

我们通过例子了解了如何使用每个函数。现在，您应该很好地理解了如何在自己的代码中使用这些函数。

***出发前:***

如果你喜欢这篇文章，别忘了给我几个掌声，如果你关注我，就能收到所有关于新出版物的更新。

如果你喜欢阅读这样的故事，可以考虑[报名](https://medium.com/@alains/membership)成为[灵媒会员](https://medium.com/@alains/membership)。每月 5 美元，你可以无限制地阅读媒体上的故事。

所以不要等待，现在就注册，开始享受这一媒介所提供的一切。

**作者简介:
*Alain saame go***:SelfGrow.co.uk 的软件工程师、作家、内容策略师

***电子邮件***:alain@selfgrow.co.uk

如果你想要更多的内容，请在 Twitter 上关注我。