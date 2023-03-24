# 在 JavaScript 中明智地使用集合

> 原文：<https://levelup.gitconnected.com/use-collections-wisely-in-javascript-c1440f2ff595>

## 原创->()-->()-->()-->最终

![](img/2bf1d9de21faa082df436fe28042a6a2.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

集合是用于存储值列表的数据结构。这些被称为数组、集合和映射。在 JavaScript 中，经常需要获取元素的集合，并对每个元素应用一些函数来映射每个元素、过滤元素、查找元素或以任何其他方式缩减原始数组。

对集合的操作主要有三类，分别是`.map()`、`.filter()`和`.reduce()`。围绕这三个函数进行思考是编写干净、功能性代码的重要一步。

假设我们有一个包含多个对象的集合，每个对象代表一个`developer`。

# 地图图案

map 模式将一个函数应用于集合中的每个元素，并返回一个包含每个函数应用结果的新集合。

```
The complexity is O(n)
```

例如，我们需要的是一个只包含每个`developer`的`birth year`的数组。

**输入:** `an array of developers`
**输出:** `an array contains only birth years of developers`

## 简单的方法

使用`for()`:

使用`.forEach()`:

## 最佳接近方式

如果我们想通过使用`.map()`获得一组开发人员的出生年份，我们将编写以下代码(5 行- > 1 行)。

两种情况下的输出都是`[ 1950, 1955, 1969, 1956, 1943 ]`。

## 如何写自己的？map()函数

`.map()`函数可以通过递归实现。

# 过滤模式

`filter`模式将一个谓词应用于集合中的每个元素，并返回一个新的集合，该集合包含为所提供的谓词返回 true 的元素。

```
The complexity is O(n
```

让我们观察下面的情况。我们只想得到 1950 年以前出生的开发者。

**输入:** `an array of developers`
**输出:** `an array contains only developers to be born before 1950.`

## 最好的方法

使用`.filter()`函数，我们将编写以下代码。

## 简单的方法

而不是遵循命令式对等。

这是输出。

```
[ 
 { id: 1, name: 'Bjarne Stroustrup', born: 1950 },
 { id: 5, name: 'Ken Thompson', born: 1943 } 
]
```

## 如何编写自己的`.filter()`函数

我们可以使用递归来实现它:

# 减少模式

`reduce`模式通过用户提供的函数依次组合每个元素，将集合转换为单个值。

```
The complexity is O(n)
```

我们收集了这些开发者和他们的出生年份。我们需要知道他们所有人的平均出生年份。

**输入:**T2
输出:

## 最好的方法

使用`.reduce()`，非常简单:

## 简单的方法

与此相比。

平均出生年份为`1954.6`。

## 如何写自己的？reduce()函数？

我们可以定义其他必要的实用函数来帮助处理 JS 中的数组，比如 find 和 reduce。请用你的方式在这里分享。

# 奖金

## 使用`.join()` 功能。

举一个使用`.join()`函数的简单例子，如果我们想获得一个开发者的完整信息，我们可能会编写下面的代码。

这很好，但是我们可以这样写。

## 使用。最小()或。max()函数

我遇到的一个相当常见的问题是比较两个值，然后返回较小的值。

这样做的典型方法如下。

```
Math.min.apply(Math, (developers.map(developer => developer.born)))
<< 1946
```

让我进一步解释一下…

`Math.min()`函数返回任意一组给定数字中的最小值。每个参数都要单独输入，并且不能将多个数字的数组作为输入。为了说明这一点，请看下面的代码。

```
Math.min(1, 2, 3, 4)
**<< 1**
Math.min([ 1, 2, 3, 4 ])
**<< NaN**
```

所以为了做到这一点，在 ES6/ES2015 之前使用了`.apply()`功能。

## 自己写。sum()函数

`.sum()`函数返回给定数组中所有值的总和。

试着用它来计算出生年份的总和。

```
sum(developers.map(developer => developer.born))
**<< 9773**
```

这完全是关于 JavaScript 中的集合。

[](https://medium.com/javascript-in-plain-english/dont-use-collection-directly-93a5457a8e40) [## 不要直接使用集合

### 创建一流的收藏

medium.com](https://medium.com/javascript-in-plain-english/dont-use-collection-directly-93a5457a8e40) 

感谢阅读😘，再见👋，别忘了👏最多 50 次并跟随！