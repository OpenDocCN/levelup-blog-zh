# 每个人都应该知道的 25 个 Python 一行程序

> 原文：<https://levelup.gitconnected.com/25-python-one-liners-everyone-should-know-8befc13248c3>

这里有一个 Python 一行程序(以及更多)的列表，它将帮助您学习复杂问题的快速解决方案。

![](img/b7f01280d766e19a1c6f8d519b064e22.png)

马塞尔·埃伯勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我关于 Python 一行程序的第一篇文章很受欢迎。

[](/5-powerful-python-one-liners-you-should-know-469b9c4737c7) [## 您应该知道的 5 个强大的 Python 一行程序

### 如果没有 map()函数和理解，我真不知道该怎么办。

levelup.gitconnected.com](/5-powerful-python-one-liners-you-should-know-469b9c4737c7) 

我想写第二部。

Python 是有史以来最强大、最简单的编程语言之一。我喜欢使用 Python，因为它能让复杂的问题变得简单。我收集了 25 个我最喜欢的俏皮话，我想你会觉得有用和有趣。在这篇文章中，我试图让你学习新的东西，并保持文章内容丰富。你可能知道其中的一些，但最好记住它们。因为你不知道一句话解决的问题出现的确切时间。:)

## 1.在一个表达式中合并两个词典

如果使用 **Python3.9 及以上**，可以使用`|`来实现这个目的。

```
x = {'a': 1, 'b': 2}
y = {'c': 3, 'd': 4}z = x | y
```

**结果:**

```
>>> z{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

如果想把结果赋给第一个字典，可以简单地使用`|=`。

```
x |= y
```

**结果:**

```
>>> x{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

`*|=*` *是* `*x = x | y*` *的快捷方式。*

除了`|`，还有其他方法。如果你没有 **Python 3.9** ，也不用担心！举个例子，

```
>>> x.update(y)
>>> x{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

或者

```
>>> z = {**x, **y}
>>> z{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

## 2.合并两个列表

假设你有两个列表

```
x = ['a', 'b']
y = ['c', 'd', 'e']
```

你想把它们合并成一个列表。然后，你可以使用 list-objects 的 **extend()** 方法。

```
>>> x.**extend**(y)
>>> x['a', 'b', 'c', 'd', 'e']
```

你可能想知道为什么我们没有通过`+`或`+=`使用连接操作。其实两者没有太大区别。

```
x = ['a', 'b']
y = ['a', 'b', 'c']
>>> x += y
>>> x['a', 'b', 'c', 'd', 'e']
```

## 3.获取最常用的元素

让我们从**集合**模块中获得一些帮助，并使用`most_common()`方法。

```
>>> from collections import Counter>>> my_list = ['a', 'b', 'b', 'a', 'a', 'a', 'c', 'c', 'b', 'd']
>>> Counter(my_list).most_common()[0][0]'a'
```

好的，那很好。但是`most_common()`方法末尾的`[0][0]`代表什么？

```
>>> Counter(my_list).most_common()[('a', 4), ('b', 3), ('c', 2), ('d', 1)]
```

如您所见，它返回一个由元组组成的列表。对于每个元组，我们有

```
(element, number_of_occurrences)
```

此外，元组按出现次数排序。所以我们挑第一个带`most_common()[0]`的元组，也就是`('a', 4)`。然后我们得到使用`most_common()[0][0]`最频繁的元素，也就是`'a'`。

## 4.同时得到商和余数

`divmod()`返回一个元组，它的功能来自于模`%`和除法`/`操作符的组合。

```
>>> quotient, remainder = divmod(37, 5)>>> quotient
7>>> remainder
2
```

## 5.从列表中获取最长的字符串

假设我们有一个由单词组成的列表:

```
words = ['Python', 'is', 'awesome']
```

我们想得到这个列表中最长的单词。我们需要做的就是:

```
>>> **max**(words, key=len)'awesome'
```

## 6.按逆序获取列表

**这是一个简单的问题。**

```
>>> words = ['Python', 'is', 'awesome']
>>> reverse_words = words[::-1]
>>> reverse_words['awesome', 'is', 'Python']
```

**您可以将此应用于所有数据容器。**

```
>>> sentence = 'Python is awesome'
>>> reverse_sentence = sentence[::-1]
>>> reverse_sentence'emosewa si nohtyP'
```

## ****7。回文检查****

**我们先记住回文是什么意思。**

> **回文是一个单词、数字、短语或其他向前和向后读起来一样的字符序列，如 madam 或 racecar。([维基百科](https://en.wikipedia.org/wiki/Palindrome))**

**再用`[::-1]`吧。**

```
>>> my_word = 'madam'
>>> is_palindrome = my_word == my_word[::-1]
>>> is_palindromeTrue
```

## **8.写入文件**

**让我们写一篇课文**

```
data = 'Python is awesome'
```

**到一个文件。**

```
**with** **open**('file.txt', 'a', newline='\n') **as** f: f.write(data)
```

*****注:*** `*'a’*` *是* ***模式下 open()*** *功能。如果不存在，模式* `*'a’*` *创建一个新文件。***

## **9.在单行中读取文件**

**现在让我们试着读取我们刚刚写入文件的内容。**

**与 Java 或 C++等其他编程语言相比，用 Python 读取文件相当容易。**

```
>>> data = [line.strip() **for** line **in** **open**("file.txt")]
>>> data['Python is awesome']
```

## **10.如果-否则**

**我们将使用的可以形式化为:**

```
<on True> **if** <Condition> **else** <on False>
```

**这就是所谓的**三元运算符**。**

> **三元(来自拉丁语 ternarius)或 trinary 是一个形容词，意思是“由三个项目组成”([维基百科](https://en.wikipedia.org/wiki/Ternary))。**

**让我们写一个小程序来检查用户是否大于 18 岁。**

```
age = 21print("Enter!") **if** age >= 18 **else** print("Can't enter!")
```

## **11.在一行中查找整数列表的和**

```
>>> n = [1, 2, 3, 4, 5, 6]
>>> **sum**(n)21
```

**每个人都知道，但你可能不知道的是`sum()`函数接受另一个参数，即`start`。你知道吗？因此`sum()`被定义为**

```
**sum**(iterable, start=0)
```

**`start`值被添加到 iterable 的项目总和中。**

```
>>> **sum**(n, start=100)121
```

**所以没什么大不了的。**

**让我们把手弄脏，试着找出列表前半部分的总和。**

```
>>> **sum**(n[:len(n)//2])6
```

**我知道你在想什么。如果我们的列表长度是奇数呢？**

```
>>> n = [1, 2, 3, 4, 5, 6, 7]
```

**你可以用我们目前所学的来计算。我会给出解决方案，但是稍微考虑一下也是好的。没有找到解决办法就不要看！**

```
>>> **sum**(n[:**len**(n)//2 if **len**(n)%2 == 0 else **len**(n)//2 + 1])10
```

## **12.求第 n 个斐波那契数**

**这将是一个很好的练习来记住 Python 和递归中的 lambda 函数。**

```
fib = **lambda** x: x **if** x <= 1 **else** fib(x - 1) + fib(x - 2)
```

**让我们试试我们的方法:**

```
>>> fib(5)
5>>> fib(10)
55>>> fib(15)
610
```

## **13.计算一个字符在字符串中出现的次数**

```
>>> 'I love Python'.**count**('o')2
```

## **14.单行排序**

```
>>> numbers = [5, -3, 428, -102, 4, 1]
>>> numbers.**sort**()
>>> numbers[-102, -3, 1, 4, 5, 428]
```

**如果我们想逆序排序呢？然后传递 **reverse=True** 作为参数。**

```
>>> numbers.sort(**reverse**=True)[428, 5, 4, 1, -3, -102]
```

**您甚至可以按字母顺序排列字符串列表。**

```
>>> cars = ['Ford', 'Volkswagen', 'BMW', 'Volvo', 'Audi']
>>> cars.**sort**()
>>> cars['Audi', 'BMW', 'Ford', 'Volkswagen', 'Volvo']
```

## **15.从列表中创建词典**

```
>>> cars = ['Audi', 'BMW', 'Ford', 'Volkswagen', 'Volvo']
>>> cars_dict = **dict**(**enumerate**(cars))
>>> cars{0: 'Audi', 1: 'BMW', 2: 'Ford', 3: 'Volkswagen', 4: 'Volvo'}
```

## **16.用键值对创建字典**

```
>>> **dict**(name='Jack', country= 'USA', age=25){'name': 'Jack', 'country': 'USA', 'age': 25}
```

## **17.交换多个值**

```
x, y = y, x
```

**你想换多少就换多少。**

```
a, b, c = c, a, b
```

## **18.在一行中分配变量**

**这就像交换多个变量一样。**

```
x, y, z = 1, 2, 3
```

**这里有一个有趣的案例。如果我们想给多个变量分配相同的数呢？**

```
x = y = z = 1
```

## ****19。拆包值****

```
>>> x, y, *z = [1, 2, 3, 4, 5, 6]
>>> x
1>>> y
2>>> z
[3, 4, 5, 6]
```

## **20.过滤列表**

```
>>> cars = ['Ford', 'Volkswagen', 'BMW', 'Volvo', 'Audi']
>>> v_cars = [car **for** car **in** cars **if** car[0] == 'V']
>>> v_cars['Volkswagen', 'Volvo']
```

**我们也可以使用`filter()`和`lambda`函数。**

```
>>> cars = ['Ford', 'Volkswagen', 'BMW', 'Volvo', 'Audi']
>>> v_cars = **list**(**filter**(**lambda** c: c[0] == 'V', cars))
>>> v_cars['Volkswagen', 'Volvo']
```

## **21.从列表中删除重复元素**

**在 Python 中，**集合**中的每个元素都是唯一的，所以不会有任何重复。**

```
>>> **list**(**set**(['a', 'a', 'b', 'a', 'c']))['a', 'b', 'c']
```

## **22.将字符串列表转换为整数**

**这里我们将使用 **map()** 函数。记住 **map()** 有两个参数:一个函数和一个 iterable。它将函数应用于 iterable 的每一项。**

```
>>> **list**(**map**(**int**, ['1', '2', '3']))[1, 2, 3]
```

**这等于:**

```
result = []
**for** i **in** ['1', '2', '3']:
    result.append(**int**(i))
```

## ****23。找出所有小于等于一个数的质数****

```
**for** i **in** **range**(1, 6):
    print(i)
```

****结果:****

```
1
2
3
4
5
```

**让我们把它排成一行:**

```
print(***range**(1, 6))
```

****结果:****

```
1 2 3 4 5
```

**但是我们如何将它们打印在单独的行中呢？**

```
print(***range**(1, 6), sep='\n')
```

****结果:****

```
1
2
3
4
5
```

## **24.用另一个子字符串替换一个子字符串**

```
>>> text = 'I like Java because Java is simple.'
>>> text.replace('Java', 'Python')'I like Python because Python is simple.'
```

## **25.删除列表中的多个元素**

**让我们试着删除前三个元素。**

```
>>> cars = ['Ford', 'Volkswagen', 'BMW', 'Volvo', 'Audi']
>>> **del** cars[:3]
>>> cars['Volvo', 'Audi']
```

## **额外收获:创建一个无限 while 循环**

**小心，不要忘记停止程序。:)**

```
**while** 1: 0
```

**感谢您阅读我的文章。**

**请查看我的另一篇文章:**

**[](https://betterprogramming.pub/3-essential-decorators-in-python-you-need-to-know-654650bd5c36) [## 你需要知道的 Python 中的 3 个基本装饰器

### 更擅长装饰

better 编程. pub](https://betterprogramming.pub/3-essential-decorators-in-python-you-need-to-know-654650bd5c36)**