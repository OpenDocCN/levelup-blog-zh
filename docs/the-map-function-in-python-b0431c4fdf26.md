# Python 中的 map()函数

> 原文：<https://levelup.gitconnected.com/the-map-function-in-python-b0431c4fdf26>

## 它的用途和使用时间，以及它与地图数据结构有何不同

不要与 C++和 Python 中的地图数据结构相混淆:

```
Map<String, Integer> myMap = new Map();
```

这些语言中的地图的 Python 等价物实际上是 Python 中的字典。刷新一下，Python 中的字典只是一组键值对。像这样做一个新字典:

```
mydict = {}
```

或者用一些默认值填充它:

```
mydict = { "key1": "value1", "key2": "value2" }
```

另一方面，Python 中的`map()`实际上是一个方便向下迭代数组并返回处理过的数组的函数。

## Python 地图

在将给定函数应用于给定可迭代对象(列表、元组等)的每一项后，`**map()**`函数返回结果的映射对象(迭代器)。) [2]

## 句法

```
map(fun, iter)
```

## 因素

```
**fun**: it is a function to which passes each element of a given iterable**iter**: It is an iterable which is to be mapped
```

**注意:**你可以向 map()函数传递一个或多个 iterable。

## 返回

将给定函数应用于给定 iterable(列表、元组等)的每一项后，返回结果列表