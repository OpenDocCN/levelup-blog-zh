# 元组的 10 个高级特性提升了您的 Python 技能

> 原文：<https://levelup.gitconnected.com/10-advanced-features-of-tuples-that-level-up-your-skills-in-python-5ea0345ace90>

让我们探索隐藏的元组宝石，这将提高您的 Python 知识。

![](img/144b131b8911448ab8760f48793273c6.png)

丹尼斯·帕夫洛维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在编程中，为你的目的选择最佳的数据集合是一门艺术。Python 有许多有用的内置数据集合，如列表、元组、集合等。我看到 Python 开发者倾向于使用列表作为数据容器，因为它很灵活。我决定写这篇文章来突出元组的高级特性。

元组本质上是一个静态数组。换句话说，一旦创建了元组，就不能对其进行修改和调整大小。与元组相比，人们更喜欢列表，因为列表很容易操作，并且它们有许多有用的方法，这与元组相反。但是元组也有很大的特点！它们用于保护您不希望被更改的数据。此外，它们还经过优化以提高内存效率。

让我们潜入元组的世界吧！

# 1 元组是不可变的

一旦创建了元组，就不可能修改它的元素。

```
>>> my_tuple = (1, 2, 3)>>> my_tuple[0]
1
```

否则，**类型错误**出现。

```
>>> my_tuple[0] = 'a'
```

**输出:**

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

这就是为什么存储在元组中的数据被认为是安全的，不会被修改。如果其他开发人员试图改变你的程序中元组的内容，就会引发 **TypeError** 。

# 二元组不是完全不可变的

我说元组不能被修改并不完全正确。的确，有一个例外情况。如果一个元组的元素是可变的，那么你可以修改这个元素。请记住，Python 中的列表是可变的，因此您可以更改它们的元素，添加或删除其中的项目。在下面的示例中，我们将向列表中添加一个项目:

```
>>> my_tuple = (1, 2, ['a'])>>> my_tuple[2]
['a']>>> my_tuple[2].append('b')
>>> my_tuple[2]
['a', 'b']>>> my_tuple
(1, 2, ['a', 'b'])
```

但是您只能修改该元素，而不能修改其他元素。其他元素仍然受到保护。

```
>>> my_tuple = (1, 2, ['a'])
>>> t[0] = 0
```

**输出:**

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

所以每当有人说你不能改变一个元组的任何元素，现在你知道真相了。下次解释这个功能，纠正他们的错误。

# 三元组由括号创建(并不总是如此)

这里还有一个关于元组的误解。您可能已经知道元组是由括号创建的。

```
>>> t = (1, 2, 3, 'c')
```

这并不完全正确，因为您可以创建不带括号的元组。

```
>>> t = 1, 2, 3, 'c'
>>> type(t)
```

**输出:**

```
<class 'tuple'>
```

一个只有一个元素的元组呢？你仍然可以去掉括号:

```
>>> t = 1,
>>> type(t)
```

**输出:**

```
<class 'tuple'>
```

有时，此功能可能会导致不良结果。假设你想定义一个字符串。

```
>>> t = "Tuples are awesome"
```

Upps，你的手滑了一下，无意中在结尾加了一个逗号:

```
>>> t = "Tuples are awesome",
```

现在它不是一个字符串:

```
>>> t = "Tuples are awesome",
>>> type(t)
```

**输出:**

```
<class 'tuple'>
```

您刚刚创建了一个只有一个元素的元组。

# 4-用单个元素创建一个元组

存储数据序列不是必须的。您可能只想在一个元组中存储一个元素，但是要确保在该元素后面加一个逗号。

```
>>> t = (1,)
>>> type(t)
```

**输出:**

```
<class 'tuple'>
```

如果你忘了放逗号:

```
>>> t = (1)
>>> type(t)
```

你猜怎么着？

**输出:**

```
<class 'int'>
```

没有逗号，变量不会变成元组。相反，它只是一个整数。在这种情况下，括号起到了数学运算的作用。在这里，它们指定了数学运算中的最高优先级。您可能会感到困惑，因为圆括号内只有一个数字。括号的功能与以下内容相同:

```
>>> 2 + 3 * 5
>>> (2 + 3) * 5
>>> (5) * 5
```

**输出:**

```
15
25
25
```

# 5-连接

可以用`+`运算符连接元组:

```
>>> ('blue', 'red') + ('yellow', 'green')
('blue', 'red', 'yellow', 'green')
```

等等，我知道你在想什么。不可能将元素插入元组。但我没有那么做。我只是连接两个元组，最后创建一个新的。通过比较对象的身份，我们可以很容易地检查这个操作。记住我们用内置的 **id()** 函数来实现。

```
>>> colors1 = ('blue', 'red')
>>> colors2 = ('yellow', 'green')
>>> concat_colors = colors1 + colors2>>> id(colors1)
4528667376>>> id(colors2)
4528656784>>> id(concat_colors)
4521705200
```

您也可以连接两个以上的元组:

```
>>> extended_colors = concat_colors + ('black',)
>>> extended_colors
```

**输出:**

```
('blue', 'red', 'yellow', 'green', 'black')
```

# 6-创建空元组

当您知道 Python 中存在一个空元组时，您可能会感到惊讶。

**语法:**

```
()
```

这有点奇怪。对吗？

```
>>> empty_tuple = ()
>>> type(empty_tuple)
```

**输出:**

```
<class ‘tuple’>
```

乍一看，很难理解 Python 中为什么会有空元组，因为你不能在空元组中插入任何项。毕竟空元组也是不可变的。

那么拥有空元组的目的是什么呢？

举个例子，您打算用文件中的数据填充一个元组。因此，您将逐行读取文件，并将数据存储到一个元组中。你设计了一个 Python 程序，使得`data`变量是一个元组。如果`data`被初始化为`None`，那么在读取空文件的情况下`data`就不会是一个元组。这种情况下将`data`初始化为空元组更有意义。

**例子:**

```
with open("file.txt") as f:
    data = ()
    lines = f.readlines()
    for line in files:
        data = data + (line,)
```

如果我说 Python 中只存在一个空元组呢？你会再次感到惊讶吗？

```
>>> empty_tuple1 = ()
>>> empty_tuple2 = ()
>>> id(empty_tuple1)
>>> id(empty_tuple2)
```

**输出:**

```
4502782032
4502782032
```

他们的 id 是一样的。我希望你没有检查所有的数字是否相同，因为有一个简单的方法可以做到这一点:

```
>>> empty_tuple1 **is** empty_tuple2
```

**输出:**

```
True
```

# 7-拆包

元组解包是指将一个元组分配给多个变量。

```
>>> t = ('a', 'b', 'c')
>>> x, y, z = t>>> x
'a'>>> y
'b'>>> z
'c'
```

如果您想深入了解元组解包的细节，我还有另一篇有趣的文章:

[](/improve-your-python-coding-tuple-packing-and-unpacking-44bd92daab31) [## 改进您的 Python 编码:元组打包和解包

### 打包和解包确实提高了代码的可读性。让我们复习一下，学习使用下划线和…

levelup.gitconnected.com](/improve-your-python-coding-tuple-packing-and-unpacking-44bd92daab31) 

# 8-使用星号' * '运算符扩展解包

`*`操作符用于扩展解包。

```
>>> alphabet = ('a', 'b', 'c', 'd', 'e')
>>> first, *others = alphabet>>> first
'a'>>> others
['b', 'c', 'd', 'e']
```

注意，当我们使用`*`操作符时，返回的是一个列表，而不是一个元组。

```
>>> first, *others, last = alphabet>>> first
'a'>>> others
['b', 'c', 'd']>>> last
'e'
```

最后一个拆包的例子是:

```
>>> *others, last = alphabet>>> others
['a', 'b', 'c', 'd']>>> last
'e'
```

# 9-方法

使用元组的一个优点是保护数据序列，以便数据只支持只读操作。与列表不同，元组没有调整大小的方法:**。** **【追加】。移除()**，**。pop()** 等。那还剩下什么？

元组有什么？

可以肯定的是，**指数()**和**计数()**:

*   **index()** — *返回一个元素第一次出现的索引*
*   **count()** — *返回某个值出现的次数*

```
>>> t = (1, 2, 3, 1, 1)>>> t.index(1)
0>>> t.count(1)
3
```

# 10 元组和字典

*   元组作为键-值对来构建字典:

在元组的帮助下，你可以很容易地建立一个字典。为此，您应该在集合中存储一个元组序列。这个集合可以是一个元组，甚至是一个列表。值得一提的是，这些元组的第一个元素应该是键，第二个元素应该是这样的值:

```
((key1, value1), (key2, value2), (key3, value3))
```

**例子:**

```
>>> winter_list = [('jan', 1), ('feb', 2), ('dec', 12)]
>>> winter_tuple = (('jan', 1), ('feb', 2), ('dec', 12))>>> winter = dict(winter_list)
>>> winter.get('jan')
1>>> winter = dict(winter_tuple)
>>> winter.get('dec')
12>>> winter
{'jan': 1, 'feb': 2, 'dec': 12}
```

让我们再举一个元组用法的例子。假设我们在太空中有一个 *2 x 2* 网格系统，想要存储一个航天器从点经过的次数。这里，我们将使用元组作为字典中的键。

**例 2:**

```
>>> grid_data = {(0, 0): 100, (0, 1): 50, (1, 0): 75, (1, 1): 75}
>>> grid_data
{(0, 0): 100, (0, 1): 50, (1, 0): 75, (1, 1): 75}>>> grid_data.get((0, 0))
100>>> grid_data.get((1, 0))
75
```

**结论**

如您所见，元组提供了广泛的用途和特性。更好地使用元组会让你在程序中更加舒适和灵活。

不像元组，你的 Python 知识不应该是未经修改的！可以延长！

总是有新的东西要学。我希望你在阅读时喜欢我的文章。

如果你愿意，你可以看看我发表的另一篇文章:

[](https://betterprogramming.pub/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569) [## 在 Python 中停止使用“或”来检查多个条件

### 还有更精密的解决方案！

better 编程. pub](https://betterprogramming.pub/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569)