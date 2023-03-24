# Python 中的字典数据类型——不仅仅是字典

> 原文：<https://levelup.gitconnected.com/dictionary-data-types-in-python-more-than-just-dict-a9ff84d38738>

![](img/66c095ce3e8b46c805ca758d8bb23a40.png)

由 [Waldemar Brandt](https://unsplash.com/@waldemarbrandt67w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/dictionary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 快速浏览 Python 中三种常见的字典数据类型。

字典是 Python 和任何其他现代编程语言(如 Javascript 和 Swift)中必不可少的数据结构。它们也被称为映射、散列表、散列表和关联数组。尽管名称不同，但它们有着相同的特性——它们的元素是键值对。简单地说，字典由一个或多个条目组成，我们可以使用唯一的键来标识每个元素。使用一个特定的键，我们将能够找到与那个键相关的值。

如果你学过 Python，你大概知道`dict`是最常用的内置数据类型之一。是 dictionary 的简称，但是你知道 Python 中还有其他的字典数据类型吗？让我们在这篇文章中了解一下它们是什么。我们还将介绍它们自己的基本用法。

## 内置字典

`dict`数据类型是 Python 标准库中唯一的内置映射数据结构。它的主要用途非常简单，如下所述。

字典的基本用法

关于`dict`数据类型的常见用法，下面列出了一些要点。

*   钥匙需要[可拆卸](https://docs.python.org/3/glossary.html#term-hashable)。如果一个对象的类型实现了`__hash__()`方法，那么这个对象就是可散列的，这样就可以为该类型的每个对象计算一个散列值。重要的是，可散列对象在其生命周期中不应该改变，这意味着像`list`和`dict`这样的可变对象是不可散列的，因此不能用作键，如下所示。

```
>>> dict_example = {1: 1, 'three': 3, (4, 5): 0}
>>> dict_example[[1,2]] = 5
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
```

*   如果我们使用方括号方法(即`student[‘firstName’]`)访问一个值，如果键不在`dict`中，解释器将抛出一个`KeyError`。因此，访问特定键值的更安全的方法是使用`get(key[, default])`方法。当键不存在时，除非设置默认值并相应返回，否则将返回`None`。

```
>>> dict_missing = {'zero': 0, 'one': 1}
>>> dict_missing['two']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'two'
>>> print(dict_missing.get('two'))
None
>>> print(dict_missing.get('two', -1))
-1
```

*   我们可以分别使用`keys()`、`values()`和`items()`方法来访问`dict`的键、值和键值对(显示为元组)。这些被称为[视图对象](https://docs.python.org/3/library/stdtypes.html#dict-views)，这意味着它们只是`dict`的动态视图，并且随着`dict`的变化而变化。此外，这些视图是可迭代的，因此它们都可以在 for 循环中使用。

```
>>> dict_iterable = {'zero': 0, 'one': 1}
>>> dict_iterable_keys = dict_iterable.keys()
>>> dict_iterable_keys
dict_keys(['zero', 'one'])
>>> dict_iterable['two']=2
>>> dict_iterable_keys
dict_keys(['zero', 'one', 'two'])
>>> for i in dict_iterable_keys:
...     print(i)
... 
zero
one
two
```

## 默认字典

作为内置`dict`类的子类，`defaultdict`对象有时被用作类似字典的对象。应该注意的是，`defaultdict`数据类型不是标准库的一部分，它在`collections`模块中可用。

除了拥有一个`default_factory`属性之外，`defaultdict`类型具有与`dict`类相同的功能。`defaultdict`的构造函数是`defaultdict([*default_factory*[, *...*]])`。具体来说，`default_factory`参数的默认值是`None`。当它被设置时，当给定的键不在`defaultdict`中时，它被不带参数地调用以生成默认值。下面给出一个例子。

```
>>> from collections import defaultdict
>>> letter_counts = defaultdict(int)
>>> for i in 'abbcccddddeeeee':
...     letter_counts[i] += 1
... 
>>> letter_counts.items()
dict_items([('a', 1), ('b', 2), ('c', 3), ('d', 4), ('e', 5)])
```

如上图所示，当`letter_counts`没有任何键时，访问缺少的键的值(如‘a’)，调用`default_factory`(即`int`)，返回 0，因为`int()`返回 0。因此，`defaultdict`最突出的特点就是**通过调用** `**default_factory**` **函数来返回缺省值。**

需要注意的一点是`default_factory`参数只能用可调用的东西来设置，比如函数或 lambda。所以上面的例子可以改写成下面这样。我们使用λ函数`lambda: 0`来代替`int`函数。

```
>>> from collections import defaultdict
>>> letter_counts = defaultdict(lambda: 0)
>>> for i in 'abbcccddddeeeee':
...     letter_counts[i] += 1
... 
>>> letter_counts.items()
dict_items([('a', 1), ('b', 2), ('c', 3), ('d', 4), ('e', 5)])
```

## 有序的

另一个有用的类似字典的类型是`OrderedDict`，它也作为`defaultdict`类型在`collections`模块中可用。过去，`OrderedDict`和`dict`类型的主要区别在于前者记得物品的顺序。然而，从 Python 3.7 开始，`dict`类型也实现了基于键值对插入顺序的排序，因此这种跟踪顺序的差异消失了。除此之外，还有两件事值得注意。

*   它们以不同的方式实现了`popitem()`方法。在`dict` (Python 3.7+)中，该方法使用 LIFO(即后进先出)顺序移除并返回一个键值对。然而，在`OrderedDict`中，我们可以设置一个布尔参数 last。当它为真时，被移除和返回的键值对将是 LIFO 顺序，当它为假时，顺序将是 FIFO(即先进先出)，这样`OrderedDict`在我们想要使用哪个顺序移除项目方面给了我们更多的灵活性。相关的例子如下。

```
>>> from collections import OrderedDict
>>> dict_remove = {0: 'zero', 1: 'one', 2: 'two'}
>>> dict_remove.popitem()
(2, 'two')
>>> ordereddict_remove = OrderedDict({0: 'zero', 1: 'one', 2: 'two'})
>>> ordereddict_remove.popitem(last=False)
(0, 'zero')
>>> ordereddict_remove.popitem(last=True)
(2, 'two')
```

*   它们实现相等测试的方式不同。当我们比较两个`OrderedDict`时，条目的顺序很重要。然而，当我们与`dict` s 比较时，项目的顺序并不重要。除了在各自的数据类型中进行比较，当我们比较一个`OrderedDict`和一个`dict`时，顺序并不重要。这里有一些例子。

```
>>> ordereddict0 = OrderedDict({0: 0, 1: 1})
>>> ordereddict1 = OrderedDict({1: 1, 0: 0})
>>> ordereddict2 = OrderedDict({0: 0, 1: 1})
>>> dict0 = {0: 0, 1: 1}
>>> dict1 = {1: 1, 0: 0}
>>> ordereddict0 == ordereddict1
False
>>> ordereddict0 == ordereddict2
True
>>> dict0 == dict1
True
>>> ordereddict0 == dict0
True
>>> ordereddict0 == dict1
True
```

# 外卖食品

在本文中，我们学习了 Python 中三个最常用的字典。我们什么时候知道用哪个？以下是一些通用指南。

*   在大多数情况下，我们可以使用内置的`dict`数据类型。不仅因为它为我们的需求提供了最多的功能，还因为它不需要导入任何模块。
*   当我们构造一个字典时，如果我们知道应该为一个丢失的键设置什么默认值，我们应该考虑使用`defaultdict`数据类型，因为它针对这种用法进行了优化。
*   总的来说，`OrderedDict`和`dict`有相似的用法，所以我们可以互换使用它们，除非我们运行相等测试时考虑到字典的顺序。

感谢您阅读这篇文章。