# 掌握抽象编程

> 原文：<https://levelup.gitconnected.com/get-a-grip-on-programming-with-abstractions-29bb34a3737>

Python 中抽象的例子——我开始从头开始学习如何设计程序——分享我是如何掌握抽象的。

![](img/380e427d03ad28075684765eaf333286.png)

通过 Adobe Stock 从[vitaliymetaha](https://stock.adobe.com/contributor/202405468/vitaliymateha?load_type=author&prev_url=detail)获得许可。

在花了一年左右的时间自学 Python 编程之后，我点击了重置按钮，通过参加*的密集程序设计*课程重新开始。仅仅过了几周，我就学到了很多以前从未想过要学的重要的软件编写基础课程。在某种程度上，我终于学到了足够的东西来学习*如何学习编程*，这是一项我不知道自己需要的技能。

在这个故事中，我记住了到目前为止我所学到的一部分，一部分是为了让我不会忘记，但也是为了分享一些关于如何理解抽象以及为什么它很重要的技巧。

# 第一课——你已经知道抽象，毫不费力

曾经在 Python 中使用过像 **sum()** 这样的内置函数来添加数字列表或者 **len()** 来获得对象的长度吗？如果是这样，你已经知道什么是抽象，也就是说，一个隐藏了它是如何工作的函数，这样你就可以继续你的生活了。

例如，通过下面一个简单的例子，我们可以看到 **sum()** 是如何隐藏*的，以及*是如何通过手动创建一个执行相同工作的循环来添加数字列表的。

```
**# how to add a list of numbers, two ways**
lst_of_numbers = [2,3,5]**# example of using a built-in function as an abstraction**
sum_with_abstraction = sum(lst_of_numbers)print('With Built-in Function:', sum_with_abstraction)**# example of getting sum without a built-in function**
sum_with_loop = 0for i in lst_of_numbers:
  sum_with_loop += iprint('Without Built-in Function:', sum_with_loop)**# Output**
# >>> With Built-in Function: 10
# >>> Without Built-in Function: 10
```

## **第 1 课结论:那么，将内置函数视为一种抽象形式有什么大不了的呢？**

最重要的是——当有人抛出抽象这个词时，不要被吓倒，因为你已经知道它是什么了。此外，值得一提的是，内置函数可以满足你的大部分编程需求，并且可能比从头构建要好。但是，没有随时可用功能的其他任务怎么办？

# **第二课——用抽象思维进行设计**

曾几何时，呃，就在我开始学习设计程序之前，我在一个 python 文件中写了无数行代码，做了我想做的一切。然而，有了抽象函数的概念，我开始欣赏创建函数来处理程序中的单个任务。换句话说，这里的经验是为每个任务创建一个函数，或者将一组逻辑任务组合在一起。

比如说，我们有一个程序。在我们的程序中，我们希望得到一个字符串形式的数字列表，并需要它们的总和来进行其他操作。作为一个额外的约束，我们不能使用像 sum()这样的内置函数。

在接下来的第一个例子中，我们有一系列完成工作的操作。该代码通过首先将字符转换为整数，然后将整数相加来计算字符串列表。虽然下面的代码对于简单的求和例子来说似乎足够好了，但是在更复杂的情况下，它可能会变得混乱和失控。

```
**# Example of a first pass with no substantial abstraction:****# sum a list of strings** 
lst_of_strings = ['6','7','8']**# first, convert a list of strings to a list of numbers**
lst_of_numbers = []for char in lst_of_strings:
  lst_of_numbers.append(int(char))**# second, sum a list of numbers**
sum_with_loop = 0for i in lst_of_numbers:
  sum_with_loop += i**# third, then do something with the sum ...**
```

因为我们知道我们有两个不同的任务，即从字符串转换然后对一系列数字求和，让我们把这些操作放到它们自己的函数中。在下面的第二遍中，我们将这两个操作表示为两个不同的函数，这样做时，我们通过将一组字符串传递给 **string_to_int()** 和 **sum_lst()** 函数，抽象出了这个东西是如何做的。

```
**# Example of a second pass with some functional abstraction:****# sum a list of strings**
lst_of_strings = ['6','7','8']# call on two functions that we abstract away
***result*** = ***sum_lst***(***string_to_int***(lst_of_strings))
print(result)**# Output**
# >>> 21**## the functions from above are abstracted away below****# a function to convert a list of strings to a list of numbers**
def ***string_to_int***(lst):
  lst_of_numbers = []

  for char in lst:
    lst_of_numbers.append(int(char))

  return lst_of_numbers**# a function to sum a list of numbers**
def ***sum_lst***(lst):
  sum_with_loop = 0 for i in lst:
    sum_with_loop += i

  return sum_with_loop**# then do something with the *result* ...**
```

很好，是吧？为了清楚起见，我为这个特定的任务集设计了比必要的更复杂的东西，但是我这样做是为了展示我们如何能够识别单个的任务并将它们抽象成定制的函数。话虽如此，让我们把这个例子更进一步，把 **string_to_int()** 和 **sum_lst()** 组合成一个函数。

> 关于函数抽象的一种非技术性的思考方式:我们不关心一个字符列表如何变成一个数字列表，然后变成一个总和——我们不关心——我们只想要总和。

为什么要组合功能？虽然情况并非总是如此，但在这种情况下， **string_to_int()** 函数除了为 **sum_lst()** 准备一些数据之外，没有任何用途。因此，在下面的第三次传递中，两个函数被合并成一个函数，该函数接受一个字符串列表并返回一个总和。

```
**# Example of a third pass; everything abstracted to one function****# sum a list of strings** 
lst_of_strings = ['6','7','8']result = sum_lst(lst_of_strings)
print(result)**# Output**
# >>> 21**# a single function that completes the task**
def **sum_lst**(lst):
''' Input: a-list-of-strings -> An Integer as a sum of the Input''' **# string_to_int is now only a local function** inside sum_lst
  def string_to_int(alst):
    lst_of_numbers = []

    for char in alst:
      lst_of_numbers.append(int(char))

  return lst_of_numberssum_with_loop = 0**# evaluate a list of numbers (alst)**
alst = string_to_int(lst)for i in alst:
  sum_with_loop += ireturn sum_with_loop**# then do something with the sum ...**
```

## 第 2 课结论:将 like 操作分组到函数中。

将一堆操作转变为有组织的功能和组功能，它们一起帮助其他功能，即助手功能或辅助功能。

![](img/5f941fb71bad43fdf98d9b5663f3eb04.png)

由[paweczerwiński](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第 3 课—使用 Map()、Reduce()和 Lambdas 进行抽象

在熟悉了前面隐藏函数内部工作方式的例子之后，我们如何进一步应用抽象概念来使生活变得更容易呢？简单，继续抽象！

有一个想法，也许更理想的是，让代码尽可能简单。换句话说，简单意味着计算机可以有效地评估你的代码，其他程序员可以很容易地阅读和理解你写的东西。在某些情况下，最理想的是成为一行程序的大师，在这种情况下，你可以成为一名绝地大师，将一堆代码缩减为一行代码。

[](https://medium.com/python-in-plain-english/unlock-basic-lambdas-in-python-2796a4a762a8) [## 在 Python 中解锁基本 Lambdas

### 对于任何人——通过一个实际的 lambdas、map 和 reduce 的工作示例，在 Python 中征服 lambdas

medium.com](https://medium.com/python-in-plain-english/unlock-basic-lambdas-in-python-2796a4a762a8) 

如果我们仍然不能使用 Python 内置的 **sum()** 函数，我们如何继续将我们的任务抽象成一行代码呢？在下面的例子中，我们有两个 lambda 函数，我们用 map 和 reduce 来应用它们。

```
**# Example of abstraction with reduce, map, and lambdas**lst_of_strings = ['6','7','8']

result = sum_lst(lst_of_strings)
print(result)**# Output**
# >>> 21def sum_lst(lst):
  **return reduce(lambda a, b: a+b, (map(lambda x : int(x), lst)))**
```

首先，在上面的例子中，我创建了一个 lambda 函数，它将列表中的每个元素转换为整数，然后将其映射到字符串列表中；下面突出显示。我们基本上替换了之前创建的 **string_to_int()** 函数中的 for 循环。

```
**(map(lambda x : int(x), lst)**) -> returns an object that can represent a list of numbers
```

其次，如果我们不能使用 **sum()** ，我们可以创建第二个 lambda，将数字列表的每个元素相加，并使用 **reduce()** 返回一个数字作为列表的总和。

```
**reduce (lambda a, b: a+b,** *a map object [a list of numbers]*)# in this case, reduce and lambda are like using:**sum(***some list of numbers***)**
```

第三，也是最后一点，如果我们去掉约束并允许使用 sum()，那么最终的函数可能如下所示，我们不再需要 reduce()，但仍然保留了一个简单的一行程序。

```
**# A one-liner example; abstracting with sum, map, and a lambda**def sum_lst(lst):
  return sum(map(lambda x : int(x), lst))
```

[](https://towardsdatascience.com/become-a-python-one-liners-specialist-8c8599d6f9c5) [## 成为 Python“一行程序”专家

### 少写代码，多成就！

towardsdatascience.com](https://towardsdatascience.com/become-a-python-one-liners-specialist-8c8599d6f9c5) 

## 第三课结论:继续抽象，如果可能的话，一行是理想的

在理解了如何实现这个函数之后，继续将其精简到最简单的形式——使用 map()、reduce()和 lambdas()来帮助简化到尽可能少的行。

![](img/7492ae44dbe5ffddac940218cf3e052d.png)

照片由 [Rodion Kutsaev](https://unsplash.com/@frostroomhead?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

至此，我们已经从一个从字符串列表中计算总和的函数中删除了几乎所有不必要的细节，方法是将一些足够好的代码抽象为函数、嵌套函数，最后利用 map()、reduce()和 lambdas 等函数将解决方案简化为一行代码。

这远不是一个全面的抽象指南，而只是一个开始。更多内容，请阅读 medium.com 上关于学习编码、抽象、合成和函数式编程的文章。

[](https://code.likeagirl.io/beginners-guide-to-programming-15c06bfc2d72) [## 学习学习编码

### 我是一名自学成才的开发人员。当我开始学习编码时，最大的困惑是学习哪门或哪些课程…

code.likeagirl.io](https://code.likeagirl.io/beginners-guide-to-programming-15c06bfc2d72) [](https://medium.com/javascript-scene/abstraction-composition-cb2849d5bdd6) [## 抽象与构成

### 注:这是“作曲软件”系列的一部分(现在是一本书！)关于学习函数式编程和…

medium.com](https://medium.com/javascript-scene/abstraction-composition-cb2849d5bdd6) [](https://towardsdatascience.com/elements-of-functional-programming-in-python-1b295ea5bbe0) [## Python 中函数式编程的要素

### 了解如何使用 Python 中的 lambda、map、filter 和 reduce 函数来转换数据结构。

towardsdatascience.com](https://towardsdatascience.com/elements-of-functional-programming-in-python-1b295ea5bbe0) 

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)