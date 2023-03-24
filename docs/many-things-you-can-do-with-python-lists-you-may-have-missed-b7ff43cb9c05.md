# 你可以用 Python 列表做很多你可能错过的事情

> 原文：<https://levelup.gitconnected.com/many-things-you-can-do-with-python-lists-you-may-have-missed-b7ff43cb9c05>

![](img/992697fd1729e32b0839b740c521175b.png)

由 [Hilthart Pedersen](https://unsplash.com/@h3p?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将看看我们可以用 Python 列表做的许多事情。

# 用 sort()方法对列表中的值进行排序

我们可以使用 list `sort`方法对列表进行排序。它适用于任何对象，包括字符串和数字。

例如，我们可以如下使用它:

```
fruits = ['apple', 'orange', 'orange', 'grape', 'pear']
fruits.sort()
```

分类是就地完成的。所以它对被调用的列表进行排序。

因此，我们得到:

```
['apple', 'grape', 'orange', 'orange', 'pear']
```

作为`fruits`的新值。

有了数字，我们可以写:

```
nums = [5,3,4,6,7]
nums.sort()
```

那么`nums`的新价值就是:

```
[3, 4, 5, 6, 7]
```

我们不能对具有混合数据类型的列表进行排序。

例如，下面的列表:

```
mixed = ['apple', 'orange',3,2,1]
mixed.sort()
```

我们会得到:

```
TypeError: '<' not supported between instances of 'int' and 'str'
```

由于`sort`无法比较条目。

# 用 reverse()方法反转列表中的值

`reverse`方法就地反转一个列表。也可以用字符串和数字数组来完成。

例如，我们可以写:

```
fruits = ['apple', 'orange', 'orange', 'grape', 'pear']
fruits.reverse()
```

那么`fruits`的新值就是:

```
['pear', 'grape', 'orange', 'orange', 'apple']
```

同样，我们可以对数字列表做同样的事情:

```
nums = [5,3,4,6,7]
nums.reverse()
```

然后我们得到:

```
[7, 6, 4, 3, 5]
```

作为`nums`的新值。

# 使用 clear()清空列表

我们可以调用列表中的`clear`来清除列表中的所有条目。

例如，我们可以这样称呼它:

```
nums = [5,3,4,6,7]
nums.clear()
```

然后我们得到`nums`的新值是一个空列表。

# 用 pop()移除列表的最后一个元素

我们可以调用列表中的`pop`来删除列表中的最后一项。

例如，我们可以编写以下内容:

```
nums = [5,3,4,6,7]
nums.pop()
```

从`nums`上取下 7。

# 用 count()计算一个项目在列表中出现的次数

`count`方法接受我们想要在列表中搜索的元素。

例如，我们可以如下使用它:

```
fruits = ['apple', 'orange', 'orange', 'grape', 'pear']
orange_count = fruits.count('orange')
```

`orange_count`应该是 2，因为`'orange'`在列表中出现了两次。

# 使用 copy()浅层复制列表

`copy`方法创建一个它所调用的数组的浅表副本并返回它。这个和`a[:]`一样。

浅层复制意味着只复制顶层项目。嵌套项仍在引用原始项。

例如，假设我们有以下代码:

```
fruits = ['apple', 'orange', 'orange', 'grape', 'pear']
fruits_copy = fruits.copy()
fruits_copy.append('banana')
```

我们应该看到`fruits`的值是:

```
['apple', 'orange', 'orange', 'grape', 'pear']
```

而`fruits_copy`的值是:

```
['apple', 'orange', 'orange', 'grape', 'pear']
```

这是因为我们通过调用`copy`制作了一份`fruits`列表的副本。因此，`fruits_copy`引用了与第二行中的`fruits`具有相同条目的新列表。

![](img/0567d5cce34858c8fc56061d35d8dc07.png)

Alex Azabache 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 列出理解

我们可以使用 list comprehension 语法来创建一个列表，方法是通过调用一个函数来映射列表项。

例如，我们可以写:

```
cubes = [x**3 for x in range(10)]
```

创建一个数组，每个条目都是从`range(10)`返回的条目的立方体。

因此，我们应该得到:

```
[0, 1, 8, 27, 64, 125, 216, 343, 512, 729]
```

由于`0**3`为 0，`1**3`为 1，`2**3`为 8，以此类推。

我们还可以使用它来返回一个新的列表，其中一些项目被过滤掉，如下所示:

```
nums = [-3,5,6]
positive_nums = [x for x in nums if x > 0]
```

上面的代码获取列表中所有大于 0 的数字并返回。

因此，我们应该得到:

```
[5, 6]
```

作为`positive_nums`的值。

# 结论

Python 列表有很多方法来完成各种操作。

我们可以用`sort`对列表进行排序，用`pop`从列表中删除最后一项，用`count`计算我们要查找的项目数，用`clear`清空列表。

Python 还有 list comprehension 语法，可以从另一个列表生成一个列表。