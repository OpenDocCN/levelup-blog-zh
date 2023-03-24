# 用 Python 列表处理数据集合

> 原文：<https://levelup.gitconnected.com/handling-collections-of-data-with-python-lists-26e7b904e4c>

![](img/330a57e14306d04c9e592da0df46b808.png)

照片由[艾玛·马修斯数字内容制作](https://unsplash.com/@emmamatthews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将了解如何使用 Python 列表来存储和处理数据集合。

# 列表数据类型

在 Python 中，列表是包含有序序列中的多个值的值。

我们可以定义如下:

```
['apple', 'orange', 'grape']
```

我们可以将它赋给一个变量，如下所示:

```
fruits = ['apple', 'orange', 'grape']
```

然后我们可以通过索引得到每个条目的值，第一个条目的索引为 0。

例如，我们可以编写以下内容:

```
apple = fruits[0]
```

`apple`的值为`'apple'`。

我们也可以写:

```
apple = ['apple', 'orange', 'grape'][0]
```

做和上一个例子一样的事情。

如果该项的索引超过了列表中值的数量，那么我们将得到一个`IndexError`。

例如，如果我们有:

```
fruits = ['apple', 'orange', 'grape']
```

那么我们会得到一个错误，如果我们写:

```
fruits[4]
```

索引只能是整数。

我们可以在父列表中嵌套列表。例如，我们可以写:

```
nested_list = [[1, 2, 3], ['apple', 'orange', 'grape']]
```

在一个大列表中有两个列表。

我们可以访问每个列表的值，如下所示:

```
nested_list[0][1]
```

会给我们两个。

```
nested_list[1][1]
```

会返回`'orange'`。

# 负指数

我们可以使用负索引从末尾开始检索项目。-1 表示列表的最后一个索引，-2 表示倒数第二个条目，依此类推。

例如，如果我们有:

```
fruits = ['apple', 'orange', 'grape']
```

然后:

```
fruits[-2]
```

返回`'orange'`。

# 从另一个包含切片的列表中获取列表

我们可以通过使用括号中两个索引之间的冒号从另一个列表中获得一个值列表。

例如，如果我们有:

```
fruits = ['apple', 'orange', 'grape', 'pear']
```

然后:

```
fruits[1:3]
```

返回`[‘orange’, ‘grape’]`。

我们也可以省略其中一个索引。如果我们忽略了右边的索引，那么我们将返回从左边的索引到列表末尾的所有内容。

如果我们忽略左边的索引，那么我们会得到从列表开始到右边索引的所有内容。

此外，我们可以省略这两个索引来获得整个列表。

给定之前的`fruits`数组，`fruits[1:]`返回:

```
['orange', 'grape', 'pear']
```

如果我们有`fruits[:2]`，它会返回:

```
['apple', 'orange']
```

如果我们有`fruits[:]`，它将返回:

```
['apple', 'orange', 'grape', 'pear']
```

# 用 len()函数获取列表的长度

我们可以向`len`函数传递一个列表来获得数组的长度。

例如，给定`fruit`列表，我们可以如下使用它:

```
fruits = ['apple', 'orange', 'grape', 'pear']
```

`len(fruits)`返回 4，因为我们在`fruits`列表中有 4 个项目。

![](img/9486adb6bf02ffb113d6697040db8887.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 更改带有索引的列表中的值

我们可以通过使用赋值操作符将带有索引的列表设置为一个值来更改列表条目。

例如，我们可以写:

```
fruits[1] = 'banana'
```

将`fruits`的第二个条目设置为`'banana'`。

现在`fruits`的价值在于:

```
['apple', 'banana', 'grape', 'pear']
```

# 列表串联和列表复制

我们可以用`+`操作符连接两个列表。

例如，给定以下列表:

```
fruits = ['apple', 'orange', 'grape', 'pear']
nums = [1, 2, 3]
```

我们可以如下连接这两个列表:

```
big_list = fruits + nums
```

然后我们得到:

```
['apple', 'orange', 'grape', 'pear', 1, 2, 3]
```

作为`big_list`的价值。

# 用 del 从列表中删除值

我们可以使用`del`关键字从列表中删除一个值。

例如，我们可以编写下面的代码从前面的`fruits`列表中删除项目。

我们可以这样写:

```
del fruits[1]
```

删除`fruits`的第 2 个条目。

因此，我们有:

```
['apple', 'grape', 'pear']
```

作为`fruits`数组的值。

# 对列表使用 for 循环

我们可以通过将列表放在关键字`in`之后来循环遍历列表的值。

例如，我们可以编写以下代码来打印列表中的每个条目:

```
fruits = ['apple', 'orange', 'grape', 'pear']
for f in fruits:
  print(f)
```

要访问列表中每个条目的索引，我们可以使用`range`函数和`len`函数，如下所示:

```
fruits = ['apple', 'orange', 'grape', 'pear']
for index in range(len(fruits)):
  print('%s %s' % (index, fruits[index]))
```

那么我们应该看到:

```
0 apple
1 orange
2 grape
3 pear
```

印在屏幕上。

# 结论

我们可以使用列表来存储数据集合。

它们可以通过索引来访问。此外，它们可以用冒号分割，这将返回一个具有给定索引范围的新数组。

`for`循环对于遍历列表很有用。

`del`操作符用于从列表中删除项目。

`+`操作符用于将列表连接在一起。

列表也可以存储为其他列表的条目。