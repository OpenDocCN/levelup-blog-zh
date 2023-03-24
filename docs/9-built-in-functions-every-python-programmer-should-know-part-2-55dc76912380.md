# 每个 Python 程序员都应该知道的 9 个内置函数(第 2 部分)

> 原文：<https://levelup.gitconnected.com/9-built-in-functions-every-python-programmer-should-know-part-2-55dc76912380>

## 非常有用的 python 函数

![](img/aff954f1370f3dd5ffc9ae5f41fe17e2.png)

照片由[弗拉德·chețan](https://www.pexels.com/@chetanvlad?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[佩克斯](https://www.pexels.com/photo/low-angle-photography-of-man-jumping-2923156/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

Python 有很多非常方便易用的内置函数。使用这些内置函数，我们可以避免编写不必要的代码行。这些函数不仅使我们的 python 代码更快，而且节省了时间。

在本文中，我将分享一些我根据个人经验总结出的有用的 python 内置函数。我们开始吧！

# 1.abs()

这个`abs()`方法返回一个给定数字的绝对值。该参数可以是整数、浮点数或对象。如果参数是一个复数，那么它返回该数字的大小。

> 语法:abs(数字)

```
Output:Absolute value of integer number is: 15
Absolute value of float number is: 13.33
Absolute value of complex number is: 5.0
```

# 2.len()

这个`len()`函数返回一个对象的长度。它只接受一个参数作为输入。参数可以是字符串、列表、元组或集合(如字典或集合)。

> 语法:len(object)

```
Output:Length of list is: 5
Length of string is: 11
```

# 3.chr()

这个`chr()`函数用于返回一个表示字符的字符串，该字符的 Unicode 点是给定的整数。

这个函数接受一个有效的整数作为输入，然后返回一个表示输入整数的 Unicode 的字符。

> 语法:chr(数字)

```
Output:D
e
a
```

# 4.订单()

这个`ord()`函数是`chr()`函数的逆函数。这个函数接受一个字符作为输入，然后返回一个表示给定输入字符的 Unicode 码位的整数。

> 语法:order(字符)

```
Output:65
122
32
```

# 5.全部()

如果给定 iterable 的所有元素都为真，这个`all()`函数返回**真**。

> 语法:all(iterable)

```
Output:True 
False
```

# 6.任何()

如果给定 iterable 的任何元素为真，此`any()`函数将返回**真**。如果 iterable 为空，则返回 false。

> 语法:any(iterable)

```
Output:True
True
```

# 7.目录()

这个`dir()`函数是 python 中一个强大的内置函数。该函数返回当前局部范围内任何对象的属性和方法列表。

该函数将一个对象作为输入，然后返回该指定对象的所有属性和特性。

> 语法:dir(object)

```
Output:['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', **'age', 'city', 'name'**]
```

# 8.圆形()

此`round()`函数用于将给定的浮点数四舍五入到小数点后 n 位精度。如果未指定 n-digits 或为 None，则返回与其输入最接近的整数。

> 语法:round(数字，位数)

```
Output:5.76
5
```

# 9.已排序()

这个`sorted()`函数是 python 中对 iterable 进行排序的最简单的方法。该函数将 iterable 作为输入，然后返回指定 iterable 对象的排序列表。

还可以指定希望 iterable 排序的升序或降序。

> 语法:sorted(iterable，key=None，reverse=False)

```
Output:Sorted list is: [1, 2, 3, 4, 5, 6, 8]
Sorted list is: ['a', 'b', 'c', 'd', 'h']
```

# 结论

这就是这篇文章的全部内容。我们已经讨论了一些有用的 python 内置函数，这些函数可以用来清晰地编写代码，还可以节省时间。

感谢阅读！