# 算法入门(13 个练习算法)

> 原文：<https://levelup.gitconnected.com/getting-started-with-algorithms-with-13-practice-algorithms-d0a8872c7>

算法依赖于一些技能，简单的算法只需要对代码有基本的理解。我在本文中包括了基本的 13 种算法。

![](img/443d667c2bab4cfcd0677ce3173bc0a6.png)

由[黑脸田鸡岛崎](https://www.pexels.com/photo/crop-unrecognizable-developer-using-laptop-and-smartphone-5926389/)拍摄

对于大多数新程序员来说，算法是一个抽象的概念。

一般公众都知道它们是向我们的社交媒体源提供某些内容的机制。然而，算法(在很大程度上)并不是消耗大量数据来填充特定于用户的 YouTube 提要的内容霸主。

在大多数情况下，算法只是可以执行一项任务的指令的集合。

## 算法解释

正如我在[这篇关于如何学习算法在 FAANG](/how-to-use-algoexpert-to-land-a-faang-maang-job-cc54b7419315) 找到工作的文章中解释的那样，在停车标志前停下来可能就是算法的一个例子。

下面是一个例子:

1.  你处理看到停车标志的信息
2.  你减速到停止
3.  等待十字路口畅通无阻
4.  最后，你继续。
5.  然后，每当你接近停车标志时，就重复这个过程。

算法的支柱之一是它们必须是可重复的过程，并且当两个条件为真时它们是最好的:

1.  时间(根据算法执行任务的方式，算法理论上完成任务所需的时间)
2.  空间(算法完成任务所需的理论最大空间量)

算法的这两个方面叫做时间和空间复杂度。然而，在我看来，当你刚开始使用算法时，时间和空间的复杂性并不重要。

尽管这在未来非常重要。

# 该过程

## 学习基础知识

学习一门语言的基础。任何语言都可以。很多初学者选择 Python。我写了一篇[文章，可以帮助你学习 Python](/learn-these-things-to-master-python-a-roadmap-for-beginners-18c83e61049d) 的基础知识。

为了做最简单的算法，你需要有基本的代码知识。有了良好的基础，你就会知道哪些操作被用来执行特定的任务——这是所有算法的基本要素。

这就是为什么算法也是提高你解决问题能力的好方法。你有工具箱，你要用你的工具去做事。

## 算法

它们具有挑战性；他们很有趣；它们令人沮丧；它们是迷你程序。

算法允许你**修补**。修改代码是至关重要的，因为你有机会看到一些东西是如何工作的，以及如果某些参数被改变会发生什么。

修修补补可以让你真正探索并对编程充满好奇，这就是它的美妙之处。

像 Codewars.com 这样的网站是寻找算法解决问题的好网站，但我是从做基础 13 开始的。

我将写另一篇文章，包含带有更多描述和输出示例的基本 13 条，并且我将在本文结尾包含一个链接，但这里是基本 13 条，所以您可以马上开始。

## 基本 13

> 注意:Print 简单地说就是打印到控制台。此外，我用 Python 编写了解决方案(Python 中的缩进很重要)，所以如果需要复制其中一个解决方案，一定要调整代码的缩进方式。

1.

```
Print 1-255 Write a program that would print all the numbers from 1 to 255.Solution:i = 0while i < 256:
print(i)
i += 1
```

2.

```
Print odd numbers between 1-255 Write a program that would print all the odd numbers from 1 to 255. Solution:i = 0
while i < 256:
if i % 2 != 0:
print(i)
i += 1
```

3.

```
Print the sum of all numbers from 1 to 255.Solution:i = 0
sum = 0
while i < 256:
sum += i
i += 1
print(sum)
```

4.

```
Given an array, Example: [1,3,5,7,9,13], write a program that would iterate through each member of the array and print each value. Solution:array = [1,3,5,7,9,13]
for num in array:
print(num)
```

5.

```
Write a program that takes any array and prints the maximum value in the array.Solution:array = [1,3,5,16,9,13]
maxValue = array[0]
for num in range(1,len(array)):
if array[num] > maxValue:
maxValue = array[num]
print(maxValue)
```

6.

```
Write a program that takes an array, and prints the AVERAGE of the values in the array. For example for an array [2, 10, 3], your program should print an average of 5.
array = [2, 10, 3]Solution:sum = 0
for num in range(len(array)):
sum += array[num]
avg = sum/len(array)
print(avg)
```

7.

```
Write a program that creates an array that contains all the even numbers between 1 to 255.Solution:array = []
i = 0
while i < 256:
if i % 2 == 0:
array.append(i)
vi += 1
print(array)
```

8.

```
Write a program that takes an array and a number 'y' and returns the values in the array that are greater than a given 'y'.Solution:def valuesGreaterThan(array, y):
for x in array:
if x > y:
print(x)valuesGreaterThan([2,2,8,4,1,78], 3)
```

9.

```
Given any array x, say [1, 5, 10, -2], create an algorithm that squares each value in the array. Output: [1, 25, 100, 4].Solution:arraySquared = []
def squareValues(array):
for x in array:
arraySquared.append(x*x)
print(arraySquared)squareValues([2,2,8,4,1,78])
```

10.

```
Given any array x, say [1, 5, 10, -2], create an algorithm that replaces any negative number with the value of 0.Solution:updatedArray = []
def replaceNeg(array):
for x in array:
if x < 0:
x = 0
updatedArray.append(x)
print(updatedArray)replaceNeg([2,-2,8,4,1,-78])
```

11.

```
Given any array x, say [1, 5, 10, -2], create an algorithm that returns a hash with the maximum value, the minimum value, and the average of all values in the array.Solution:values = {"min": 0, "max": 0, "avg": 0}
def minMaxAvg(array):
sum =  0
for x in array:
if x < values["min"]:
values["min"] = x
if x > values["max"]:
values["max"] = x
sum += x
values["avg"] = sum/len(array)
print(values)minMaxAvg([1, 5, 10, -2])
```

12.

```
Given any array x, say [1, 5, 10, 7, -2], create an algorithm that shifts each number by one to the front. For example, when the program is done, the array [1, 5, 10, 7, -2] should become [5, 10, 7, -2, 0].Solution:def minMaxAvg(array):
for x in range(1, len(array)):
array[x-1] = array[x]
array[len(array)-1] = 0
print(array)minMaxAvg([1, 5, 10, 7, -2])
```

13.

```
Write a program that takes an array of numbers and replaces any negative number with the string 'Negative'. For example, if array x is initially [-1, -3, 2] after your program is done that array should be ['Negative', 'Negative', 2].Solution:def replaceNegWithString(array):
for x in range(len(array)):
if array[x] < 0:
array[x] = 'Negative'
print(array)replaceNegWithString([-1,-2,3,4,12])
```

## 后续步骤

在能够完成这些算法之后。有一些免费的网站可以让你进一步提高技能。

1.  代码战争
2.  黑客银行

## 技术面试水平

一旦你在上述两个网站中的任何一个网站上对算法都非常擅长，我建议 LeetCode 或 AlgoExpert 开始为编码面试做准备。

## 结论

算法是编程的基本要素，尤其是软件工程审查过程。如果你想通过编码面试，算法是必不可少的。另外，如果你像我一样喜欢解决问题，它们会很有趣。

同样，这是我在 AlgoExpert 上使用的学习方法的文章:

[**如何使用 AlgoExpert 获得 FAANG (MAANG)工作**](/how-to-use-algoexpert-to-land-a-faang-maang-job-cc54b7419315?source=your_stories_page-------------------------------------)

保重。

[**通过电子邮件获取我的文章点击这里**](https://anthonycg_.medium.com/subscribe) **|** [**购买 5 美元中等会员**](https://medium.com/@anthonycg_/membership)

*大家好，我是安东尼！我当然希望你喜欢这个故事，更重要的是，我希望它能让你思考，这一直是我的目标。我目前正在进行成为一名熟练软件工程师的个人旅程，我希望你能加入我的行列。给我一个关注(和一两个掌声)，我们下次再见！*