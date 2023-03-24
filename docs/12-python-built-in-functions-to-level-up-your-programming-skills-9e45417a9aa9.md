# 12 个 Python 内置函数来提升您的编程技能

> 原文：<https://levelup.gitconnected.com/12-python-built-in-functions-to-level-up-your-programming-skills-9e45417a9aa9>

让我们来谈谈 Python 中的内置函数，它们将帮助您提高 Python 技能！

![](img/02f65f4a7db1c2cd5badcda374bae619.png)

由 [Marvin Meyer](https://unsplash.com/@marvelous?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 全部

如果给定 iterable 的所有元素都为真或者 iterable 为空，则`all`返回`True`。

它可以用来快速检查一个 iterable 是否包含任何不真实的元素。

```
my_list = [1,2,3,True]print(all(my_list))
```

以上输出到`True`。

```
my_list = [1,2,30, 1,""]print(all(my_list))
```

这输出到`False`。

# 任何的

如果给定 iterable 的任何元素为真，则`any`返回`True`。如果 iterable 为空，则返回`False`。

```
my_list = [1,0,0,False]
print(any(my_list))
```

上面打印了`True`作为输出。

# 箱子

这个函数将给定的整数转换成前缀为`0b`的二进制字符串。

```
print(bin(100))
```

这条语句的输出是`0b1100100`。

![](img/9d97483948df9a6ac76d35018cecd17e.png)

诺德伍德主题公司在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 目录

当不带任何参数使用时，`dir`返回当前局部范围内的名称列表。

```
dir()#Output : 
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']
```

当一个对象作为参数被传递时，对象的`__dir__()`方法被调用，返回它的属性列表。

```
dir(list)#Output : 
['__add__', '__class__', '__class_getitem__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

# **列举**

此方法返回一个元组，其中包含:

*   iterable 和中的位置
*   与该索引相关联的值

当迭代 iterable 时

```
coffee_shops = [“Costa Coffee”, “Starbucks”, “Cafe Nero”]for index, value in enumerate(coffee_shops):
    print(index, value)#Output:
0 Costa Coffee
1 Starbucks
2 Cafe Nero
```

# evaluate 评价

当用**字符串**参数调用`eval`时，传递的表达式被评估为 Python 表达式。

```
x = 4eval("x+4")#Output: 8
```

![](img/daf8c941754e6ee4aaa85c104e049eda.png)

照片由[蒂姆·范德奎普](https://unsplash.com/@timmykp?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# **过滤器**

该函数从另一个 iterable 的元素中过滤并返回一个 iterator 对象，对于该 iterable，给定的*函数*返回`True`。

```
def greater_than_2(x):
    return x > 2my_list = [1,2,3,4,5,6]print(list(filter(greater_than_2, my_list)))#Output:
[3,4,5]
```

# 十六进制

这个函数将一个整数**转换成一个带前缀`0x`的小写十六进制字符串。**

```
print(hex(123))#Output:
"0x7b"
```

# 地图

在将指定的函数应用于另一个 iterable *的每一项后，该函数返回一个 iterable。*

```
def double(x):
    return x * 2my_list = [1,2,3,4]print(list(map(double, my_list)))#Output:
[2,4,6,8]
```

![](img/f77e23acb96e9dd402487bc04a013e9e.png)

由 [Paul Esch-Laurent](https://unsplash.com/@pinjasaur?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 颠倒的

这个函数返回原始迭代器的反向迭代器对象。

```
my_list = [1,2,3,4,5]print(list(reversed(my_list)))#Output:
[5,4,3,2,1]
```

# **已排序**

该函数从传递的 iterable 中返回一个新的排序列表。

如果需要一个反向排序的列表，函数的参数`reversed`可以设置为`True`。

```
my_list = [4,3,2,1,5]print(sorted(my_list))#Output:
[1,2,3,4,5]print(sorted(my_list, **reverse** = True))#Output:
[5,4,3,2,1]
```

# 活力

该函数将不同 iterables 中相同索引处的不同值组合起来，创建一个元组。

```
coffee_types = ["Latte", "Mocha", "Flat White"]price = [3,4,3.5]
```

当我们用这些列表作为参数调用`zip`函数时，会返回一个`zip object`，可以使用内置的`list`函数将它转换成一个列表。

```
print(zip(coffee_types, price))#Output: 
<zip object at 0x000128DB8B338>print(list(zip(coffee_types, price)))#Output:
[('Latte', 3), ('Mocha', 4), ('Flat White', 3.5)] 
```

或者，可以使用内置的`dict`函数将 zip 对象转换为字典。

```
print(dict(zip(coffee_types, price)))#Output:
{'Latte': 3, 'Mocha': 4, 'Flat White': 3.5}
```

![](img/f1d6de79b6f9fb0647556dec95203c5f.png)

照片由[微软 Edge](https://unsplash.com/@microsoftedge?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 进一步阅读

 [## 内置函数- Python 3.11.0 文档

### 该函数将您放入调用点的调试器中。具体来说，它调用、传递和直接通过。由…

docs.python.org](https://docs.python.org/3/library/functions.html) 

*感谢阅读本文！希望对你有帮助！*

*如果你是 Python 或编程的新手，可以看看我的新书《没有公牛的学习 Python 指南》**’***下面:**

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)