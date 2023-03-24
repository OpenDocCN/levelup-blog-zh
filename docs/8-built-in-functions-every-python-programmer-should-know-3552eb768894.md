# 每个 Python 程序员都应该知道的 8 个内置函数

> 原文：<https://levelup.gitconnected.com/8-built-in-functions-every-python-programmer-should-know-3552eb768894>

## 令人惊叹且有用的 Python 函数

![](img/001a409bf8ced7afb1b33d90e313f1ac.png)

照片由[布鲁斯·马斯](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Python 内置函数非常方便易用。它们使 python 脚本编写速度更快，并有助于优化代码。内置函数不仅节省了我们的时间，还帮助我们避免了不必要的代码行。

Python 有许多内置函数，我们可以用它们来提高代码的清晰度，并将复杂的问题分解成更简单的代码。知道这些内置函数的人不多，一旦你了解了这些内置函数，你就会对它们得心应手。

在本文中，我将分享我根据自己的 python 编程经验总结出的最有用的八个 python 内置函数。

## 1.哈希()

`hash()` *方法*用于返回一个对象的哈希值，如果它有一个的话。哈希值是在字典查找过程中用于比较字典键的整数。

该方法将一个**不可变对象**作为输入，然后返回该对象的哈希值。

> 语法:哈希(input_object)

```
Output:hash value for a string:  5497742200142074869
hash value for a decimal:  1152921504606846988
hash value for a tuple:  -5693404743884429734
```

## 2.地图()

`map()`函数允许您为 iterable 中的每一项执行指定的函数，并将其作为输入(函数和 iterable)。

> 语法:map(function，iterable)

```
Output:
Mapped result is:  [1, 4, 9, 16]
```

## 3.zip()

这个`zip()`函数用于映射传递的迭代器的相似索引，以便它们可以在单个实体中使用。

如果我们在 zip()函数中传递两个迭代器，两个迭代器包含相同数量的元素，那么 zip()函数将返回一个元组的迭代器。每个元组包含来自给定迭代器的相同索引元素的映射。

> 语法:zip(*迭代器)

```
Output:
The zipped result is:  {(3, 'Three'), (1, 'One'), (2, 'Two')}
```

## 4.eval()

是 python 的一个神奇功能。这个`eval()`函数允许您从基于字符串或基于编译代码的输入中计算 Python 表达式。

简而言之，eval 函数将字符串输入作为 python 表达式进行计算，并将输出作为整数返回。

> 语法:eval(字符串)

```
Output:
25
24
```

## 5.拆分()

这个`split()`方法在指定的分隔符分割给定的字符串后返回一个字符串列表。

例如，如果您想要拆分由逗号(“，”)分隔的字符串 str，那么您可以通过在 split(“，”)函数中传递分隔符来实现。

> 语法:string.split(分隔符，最大拆分)

```
Output:['apple', 'banana', 'cherry', 'orange']
['Hello', 'how', 'are', 'you']
```

## 6.订单()

这个函数用于返回给定字符的 Unicode 码位。`ord()`函数接受一个字符作为输入，然后返回一个表示给定输入字符的 Unicode 码位的整数。

> 语法:order(字符)

```
Output:
97 
36
32
```

## 7.目录()

`dir()`是一个强大的 python 内置函数，它返回指定对象的所有属性的有效列表。它返回所有属性，甚至是所有对象的默认内置属性。

> 语法:dir(object)

```
Output:['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', **'age', 'name', 'rollNo'**]
```

## 8.功率()

这个`pow()`函数返回 x 的 y 次方值

```
x = pow(2, 3)
y = pow(3, 3)print(x)
print(y)Output:
8
27
```

## 结论

这就是这篇文章的全部内容。我希望你喜欢它。实践所有这些功能，使它们使用起来得心应手。

感谢阅读！

你可以在这里免费订阅我的时事通讯:[普拉拉布的时事通讯](https://pralabhsaxena.medium.com/subscribe)