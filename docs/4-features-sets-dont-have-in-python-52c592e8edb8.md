# Python 中没有的 4 个特性集

> 原文：<https://levelup.gitconnected.com/4-features-sets-dont-have-in-python-52c592e8edb8>

这一次，我们没有学习现有的功能。相反，我们将学习在 Python 中使集合成为唯一数据类型的不存在的特性！

![](img/dddb8564424cc9fe9a10af2227534e59.png)

安妮·尼加德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

一个`set`可以定义为一个数据类型，在一个变量中存储多个元素。它是 4 个内置数据容器之一:

*   **设定**
*   元组
*   目录
*   词典

用花括号`{}`创建一个集合:

```
seasons = {"winter", "spring", "summer", "autumn"}
```

用`set()`创造是可能的。这一次您需要将一个 iterable 对象传递给`set()`:

```
# takes a list
seasons = set(["winter", "spring", "summer", "autumn"])# takes a tuple
seasons = set(("winter", "spring", "summer", "autumn"))
```

但是不要混淆集合和字典。字典是由键值对组成的数据集合。

```
my_dictionary = {"season": "summer", "month": "june", "day": 4}
```

这里的`"season"`、`"month"`和`"day"`是按键。相应的，`"summer"`，`"june"`，`4`是它们的值。

用`type()`函数，我们可以得到任何对象的类型。

```
type(seasons)
```

会给我们:

```
<class 'set'>
```

记住 Python 中的 a `set`是什么之后，我们就可以跳入它们不存在的特性了。

# 1-无重复

在`set`中没有重复的值。让我们在集合`"seasons"`的末尾再加一个`"autumn"`,看看里面只有一个`"autumn"`:

```
seasons = {"winter", "spring", "summer", "autumn", "autumn"}
print(seasons)
```

**结果:**

```
{'summer', 'spring', 'winter', 'autumn'}
```

你注意到了吗？

# 2-无序

`sets`以无序的方式存储它们的内容。换句话说，不能保证`sets`以你定义的方式存储数据。让我们创建一个名为`set-tutorial.py`的 python 文件，并将这两行代码放入该文件:

```
seasons = {"winter", "spring", "summer", "autumn"}
print(seasons)
```

我想做的是运行这个脚本五次，看看每次终端上都写了什么。为此，让我们在控制台上编写一个简单的 For 循环来运行我们的脚本:

```
for ((i=1; i<=5; i++))
do
    python set-tutorial.py
done
```

**结果:**

```
{'autumn', 'summer', 'winter', 'spring'}
{'winter', 'summer', 'spring', 'autumn'}
{'spring', 'winter', 'summer', 'autumn'}
{'spring', 'summer', 'autumn', 'winter'}
{'winter', 'spring', 'autumn', 'summer'}
```

正如你所看到的，所有的都彼此不同，没有一个像最初定义的那样有相同的顺序。

# 3-无索引-无关键字

让我们尝试访问集合中的第一个项目:

```
seasons = {"winter", "spring", "summer", "autumn"}
print(seasons[0])
```

**结果:**

```
Traceback (most recent call last):
  File "<string>", line 2, in <module>
TypeError: 'set' object is not subscriptable
```

什么？怎么会？

我知道通过索引访问一个`set`的项目看起来很自然，但这是不可能的。逻辑非常简单，因为数据在一个`set`中是无序存储的，所以通过索引来访问它的条目是没有意义的。此外，`sets`不是为存储键值对而设计的，所以不能使用键来获取元素。

对于访问项目，有几种方法。例如，您可以使用 For 循环。

```
seasons = {"winter", "spring", "summer", "autumn"}

**for** season **in** seasons:
    print(season)
```

**结果:**

```
winter
autumn
summer
spring
```

或者，您可以利用`next`和`iter`关键字:

```
seasons = {"winter", "spring", "summer", "autumn"}
seasons = **iter**(seasons)

first_item = **next**(seasons)
print(first_item)
second_item = **next**(seasons)
print(second_item)
```

**结果:**

```
summer
winter
```

但是我不得不承认，把我的变量叫做`first_item`和`second_item`还是没有意义的。没有订购！

# 4-没有可变元素

Python 中的`set`只能包含不可变的对象，比如字符串、元组、浮点数等。所以你不能把一个列表或者一个字典放到一个集合中。

这很有效:

```
my_set = {(1, 2,), 64, "hi", 1.023}
```

让我们看看一个列表或者一个字典是否可以成为一个集合的元素。不要忘记列表和字典是可变的对象。

首先，我们尝试将`{"hello": "world"}`放入一个集合中。

```
my_set = {"hi", {"hello": "world"}}
```

**结果:**

```
Traceback (most recent call last):
  File "<string>", line 1, in <module>
TypeError: unhashable type: 'dict'
```

现在，我们尝试创建一个包含列表的集合:

```
my_list = ["hello", "world"]
my_set = {my_list}
```

**结果:**

```
Traceback (most recent call last):
  File "<string>", line 2, in <module>
TypeError: unhashable type: 'list'
```

# 结论

像它的元素一样，集合是 Python 中唯一的数据类型。它们是无序的，不允许不可变的对象和重复的元素。不要试图通过索引来访问它们的元素！

我希望你喜欢和学习新的东西！

感谢您阅读我的文章。你想看看另一个好的吗？

[](https://betterprogramming.pub/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569) [## 在 Python 中停止使用“或”来检查多个条件

### 还有更精密的解决方案！

better 编程. pub](https://betterprogramming.pub/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569)