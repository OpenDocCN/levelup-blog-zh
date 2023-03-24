# 在 Python 中添加和删除列表项的所有方法

> 原文：<https://levelup.gitconnected.com/all-the-ways-to-add-and-remove-items-from-a-list-in-python-c6ee1442244a>

你知道多少种方法？还有比`append()`和`remove()`更多的方法。方法不是唯一的方法，所以让我们学习所有的方法。

![](img/59b1d5d685abaabd688f417eb4420298.png)

照片由[丹尼尔·托马斯](https://unsplash.com/@dtbosse?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

列表是 Python 中广泛使用的内置数据类型之一。在任何 Python 项目中看到列表都不足为奇。列表之所以如此重要，是因为它是可变的。也就是说，列表支持调整大小。您可以添加和删除项目。

让我们抓紧时间复习一下。

# 添加项目的内置方法

我们首先从添加项目的内置方法开始。在 Python 中，有三种内置方法可以向列表中添加项目。分别是 **append()** 、 **extend()** 和 **insert()** 。我们将涵盖所有这些，但首先，看看 **append()** 方法。

## 追加()

如果你想在一个列表的末尾添加一个元素，你应该使用 **append()** 方法。可以使用到将任何类型的数据添加到现有列表中。

**例子:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.append("banana")
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'banana']
```

但是当您试图从另一个列表中追加项目时，您会得到:

**例 2:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.append(["banana", "peach"])
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', ['banana', 'peach']]
```

你注意到了吗？括号，即列表，意外地出现在末尾。您没有向列表中添加项目。您刚才所做的是在现有列表的末尾追加另一个列表。

## 扩展()

有一个方法就是为了这个目的: **extend()** 。它将不同列表中的元素添加到列表的末尾。

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.extend(["banana", "peach"])
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'banana', 'peach']
```

事实上， **extend()** 方法用于添加任何可迭代对象中存在的元素。

让我们将一个元组作为参数传递给 **extend()** 。

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.extend(("banana", "peach"))
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'banana', 'peach']
```

让我们将一个字符串作为参数传递给 **extend()** 。

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.extend(“banana”)
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'b', 'a', 'n', 'a', 'n', 'a']
```

猜猜发生了什么？在 Python 中，字符串是可迭代的对象，字符串中的字符被迭代。

总之， **extend()** 所做的是添加一个可迭代对象的成员。

## 插入()

如果你想把一个项目添加到你想要的任何地方，你应该使用 **insert()** 方法。这个方法有两个参数:需要添加新元素的索引和元素值。

**例如:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.insert(0, “banana”)
>>> print(fruits)
```

**输出:**

```
['banana', 'apple', 'mango', 'cherry']
```

**例二:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits.insert(2, “banana”)
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'banana', 'cherry']
```

# 删除项目的内置方法

现在，我们将介绍从现有列表中删除项目的方法。在 Python 中，有三种内置的方法可以从列表中移除项目。分别是 **remove()** 、 **pop()** 、 **clear()** 。我们首先来看一下 **remove()** 方法。

## 移除()

您可以使用 **remove()** 方法从列表中删除一个项目。使用 **remove()** 方法时，给出了需要从列表中删除的元素值。

**例子:**

```
>>> fruits = ["banana", "apple", "banana", "mango", "cherry"]
>>> fruits.remove(“apple”)
>>> print(fruits)
```

**输出:**

```
['banana', 'banana', 'mango', 'cherry']
```

如果列表中有多个元素具有相同的值怎么办？此时，您将删除第一次出现的值。

**例 2:**

```
>>> fruits = ["banana", "apple", "banana", "mango", "cherry"]
>>> fruits.remove(“banana”)
>>> print(fruits)
```

**输出:**

```
['apple', 'banana', 'mango', 'cherry']
```

## 流行()

您可以从元素的元素列表索引中删除最后一个元素，作为 pop 函数的参数。

**例如:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> fruits.pop(2)
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'banana']
```

**pop()** 方法返回已删除的值。因为传递了索引，所以您可能想知道被移除项的值。

**例如:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> deleted = fruits.pop(2)
>>> print(fruits)
>>> print(deleted)
```

**输出:**

```
['apple', 'mango', 'banana']
'cherry'
```

如果没有值传递给 **pop()** 方法，那么列表的最后一项被删除。

**示例:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> deleted = fruits.pop()
>>> print(fruits)
>>> print(deleted)
```

**输出:**

```
['apple', 'mango', 'cherry']
'banana'
```

## 清除()

内置列表方法 **clear()** 不带任何参数，移除所有项。

**例如:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> fruits.clear()
>>> print(fruits)
```

**输出:**

```
[]
```

如你所见，列表完全是空的。

# 经营者

除了内置方法之外，还可以使用`+`和`*`操作符来调整列表的大小。

## +'运算符

您可以通过`+`操作符连接列表。它的工作原理类似于 **extend()** 方法。但是最后，它返回一个新的列表。

**例 1:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits = fruits + ["banana"]
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'banana']
```

让我们检查对象的 **id** 。

**例二:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> print(id(fruits))
>>> fruits = fruits + ["banana"]
>>> print(id(fruits))
```

**输出:**

```
4557568992
4555334032
```

正如您所看到的，它们并不相同，因此会创建一个新列表。

## * '运算符

使用`*`操作符可以重复列表。

**例子:**

```
>>> fruits = ["apple", "mango"]
>>> fruits = fruits * 2
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'apple', 'mango']
```

像`+`操作符一样，在列表中使用`*`操作符会创建一个新的列表，所以它不应该被算作添加一个条目的方法，因为一个新的条目不会被添加到现有的列表中。事实上，新项目出现在新创建的列表中。

让我们通过比较对象的 id 来证明:

```
>>> fruits = ["apple", "mango"]
>>> print(id(fruits))
>>> fruits = fruits * 2
>>> print(id(fruits))
```

**输出:**

```
4555234112
4557568992
```

总之，使用`+`和`*`操作符会生成一个新的列表。

# 倒三角形

在 Python 中， **del** 关键字主要用于删除对象，使对象在内存中不再可用。

因为在 Python 中一切本质上都是一个对象，所以我们可以用 **del** 关键字删除列表中的元素。为此，我们需要通过索引指定要删除的项目。

**例 1:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> del fruits[1]
>>> print(fruits)
```

**输出:**

```
['apple', 'cherry', 'banana']
```

**例 2:**

```
>>> fruits = ["apple", "cherry", "banana"]
>>> del fruits[1]
>>> print(fruits)
```

**输出:**

```
['apple', 'cherry']
```

我不知道您是否注意到，删除项目的内置方法要么一次删除一个元素，要么清除列表中的所有元素。但是我们可以用 **del** 关键字删除多个元素。

**例二:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> del fruits[1:3]
>>> print(fruits)
```

**输出:**

```
['apple', 'banana']
```

# 切片分配

支持切片是列表的关键特性之一。可以通过切片来访问列表的子部分。让我们记住如何在列表中使用切片:

**例如:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> print(fruits[1:3])
```

**输出:**

```
['mango', 'cherry']
```

我们已经使用切片来获取特定索引范围内的数据，但是我们也可以使用切片来更改列表的子部分。

让我们尝试使用切片赋值向列表中添加一个条目。

**例 2:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits[3:3] = ["banana"]
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'banana']
```

**例 3:**

```
>>> fruits = ["apple", "mango", "cherry"]
>>> fruits[3:4] = ["banana", "peach"]
>>> print(fruits)
```

**输出:**

```
['apple', 'mango', 'cherry', 'banana', 'peach']
```

让我们移除一个子部件，并为其指定一个新项目:

**例 4:**

```
>>> fruits = ["apple", "mango", "cherry", "banana"]
>>> fruits[1:3] = ["lime"]
>>> print(fruits)
```

**输出:**

```
['apple', 'lime', 'banana']
```

我们通过使用切片从列表中移除了`"mango"`和`"cherry"`。

感谢您阅读我的文章。别忘了试试上面的例子，亲自测试一下。

您可能会对另一篇有用的文章感兴趣:

[](https://python.plainenglish.io/11-python-tricks-to-boost-your-python-skills-significantly-1a5221dfa5c7) [## 显著提升你的 Python 技能的 11 个 Python 技巧

### 你不会相信有了这些技巧，Python 中的事情会变得多么简单！

python .平原英语. io](https://python.plainenglish.io/11-python-tricks-to-boost-your-python-skills-significantly-1a5221dfa5c7)