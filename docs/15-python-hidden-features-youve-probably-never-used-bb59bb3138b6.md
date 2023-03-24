# 你可能从未用过的 15 个 Python 特性

> 原文：<https://levelup.gitconnected.com/15-python-hidden-features-youve-probably-never-used-bb59bb3138b6>

## 大多数人不知道 Python 的这些不可思议的特性。

![](img/0cf733a4bc0c83cb36c5e027bed9b095.png)

照片由 [krakenimages](https://unsplash.com/@krakenimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/amazed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

今天在这篇文章中，我将向您展示 Python 的 15 个以上您可能不知道的隐藏特性。这些功能是我从很多网站上发现的，也看了很多 StackOverflow 的回答，得到了对你最有用的功能。

# 👉1# —函数参数解包

你知道吗，你可以使用`*`和`**`将一个列表或字典解包为一个函数参数。当在函数中将元组、列表和字典作为参数容器传递时，这非常有用。

```
def fun(x, y):
    # do something
    print(x, y)foo1 = [2, 5]
foo2 = {'x':2, 'y':5}
fun(*foo1)
fun(*foo2)
```

# 👉2 #—字符串样式格式化

您可以使用`format`方法和关键字`f`进行字符串样式格式化。在 format 方法中，通过花括号{}中的 match key 传递一个字典，该字典中的键值将替换字符串中的键值。

```
#method 1print("I'm a {foo} and {bar} Developer".format(foo="Programmer", bar="Android"))#method 2foo = "Programmer"
bar = "Android"
print(f"I'm a {foo} and {bar} Developer")
```

# 👉3# —就地价值交换

通常情况下，在第三方变量 temp 的帮助下交换值。但是您可以用一种简单快速的方式来完成这个任务，而不需要使用任何 temp 变量。

```
# old method
a = 5
b = 6
temp = a
a = b
b = a
print(a, b) # 6  5# new method
a = 5
b = 6a, b = b, a
print(a, b) # 6  5
```

# 👉4# —字典 Get()方法

字典有一个`get()`方法，它和我们访问字典的数据有相同的功能。但是好处是，不像字典括号值访问方法在一个键不可用时给出异常，Get 方法返回`None`。

```
dic={1 :2, 3: 5, 6:8}print(dic[8]) #IT will throw an Errorprint(dic.get(8)) # None
```

# 👉5# —步骤参数

step 参数用作切片运算符来对列表进行切片。看看下面就知道它是如何工作的了。

```
a = [1, 2, 3, 4, 5, 6, 7, 8]print(a[::2]) # [1, 3, 5, 7]
print(a[::3]) # [1, 4, 7]#reverse a string
print(a[::-1]) # [8, 7, 6, 5, 4, 3, 2, 1]
```

# 👉6# —列表理解

在我看来，列表理解是迭代并将条件放入循环的最好和最快的方式。在这一行代码中，您可以做多件事情。

```
# finding even numbers from a list
a  = [1, 2, 3, 4, 5, 6]result = [even for even in a if even % 2 == 0]print(result) # [2, 4, 6]
```

# 👉7# —链比较

链比较是 Python 的一个有趣特性，可以在一个链中进行多次比较。假设你做了`x < 10`，接下来，你做了`10 > y`，但是通过链式比较，你可以在一行中完成。

```
a = 5
b = 2
c = 4print(a > 10 > b) # false
print(a < 10 > b) # True
print(a < 12 < b > c) # False
```

# 👉8# —多重分配

诸如 C、Java、C++之类的语言允许一次用一个值声明一个变量。但是在 Python 中你可以做多重赋值。简单地说，声明多个变量并同时对它们赋值。

```
a, b = 4, 5a, b, c, d = 1 , 2, 3, 4#multiple list assignment to variblel = [1, 2]
a , b = l
```

# 👉9# — For/Else 语句

您熟悉 Python 中的条件语句。但是你知道 For else 子句吗？。

```
a = [1, 2, 3, 4, 5]for x in a:
    if x == 0:
        break
else:
    print("0 is not found in list")
```

当 for 循环成功运行且没有任何中断时，此代码将运行 else 语句。如果我们的列表中有 0，这意味着如果语句为真，那么在这种情况下，else 语句将不执行。

# 👉10 #—负索引

在编程语言中，我们可以访问从 0 到任意正数的数据。但是 Python 允许访问负索引的数据。负索引从后向访问数据。

```
# negative index
lst = [1, 2, 3, 4, 5]print(lst[-5]) # 1print(lst[-1]) # 5print(lst[-3]) # 3
```

# 👉11 #—Python 中的表情符号

你知道 python 允许你把表情符号打印成字符串吗？你可以用 Unicode 或 CLDR 名字打印特定的表情符号。查看下面的代码示例。

```
#By Unicodes# grinning face
print("\U0001f600")

# grinning squinting face
print("\U0001F606")#By CLDR Names# grinning face
print("\N{grinning face}")

# slightly smiling face
print("\N{slightly smiling face}")
```

# 👉12# —序列乘法

在 Python 中，可以通过乘以任何正数来使数据翻倍。看看下面的代码就知道它是如何工作的了。

```
d =  "abc" * 3
print(d) # abcabcabcd = [1,2,3] * 2
print(d) # [1, 2, 3, 1, 2, 3]
```

# 👉13 #—Python 之禅

你知道 Python 有一个名为“this”的秘密模块吗？它展示了 Python 的禅意。根据 [**维基百科**](https://en.wikipedia.org/wiki/Zen_of_Python) 的说法，它是 19 个影响 Python 编程语言设计的编写计算机程序的“指导原则”的集合。

```
The Zen of Python, by Tim PetersBeautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

# 👉14# —用总和展平列表

你应该知道用 map()内置函数扁平化一个列表。但是 Python 有一个特性可以用 sum()内置函数做同样的工作。

```
lst = [[1, 3, 4], [3, 34, 9, 0, 1], [94 ,5]]print(sum(lst,[])) # [1, 3, 4, 3, 34, 9, 0, 1, 94, 5]#Note this feature not work on highly unordered list
```

# 👉15# —反重力

你知道 Python 有一个隐藏的模块叫反重力吗？当您导入库并执行代码时。Python 将打开您的浏览器，并将您重定向到一个网站。想了解网站，现在就试试下面的代码。

```
import antigravity
```

# 最后的想法

毫无疑问，Python 是一种很棒的编程语言，在它上面尝试一些疯狂有趣的东西很有趣。我希望您会发现这些功能很有趣，并且乐于学习。请随意分享您的回答或您认为对 Python 程序员有用的片段。

如果你觉得这篇文章很有帮助，请点击下面的❤️按钮，在 Facebook 或 Twitter 或你的任何社交媒体上分享这篇文章，这样你的朋友也可以从中受益。

# 了解更多信息

如果您喜欢这篇文章，那么您应该看看我的其他编程文章。

[](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [## 针对日常问题的 25 个有用的 Python 片段

### 以下是我为您的日常 Python 问题提供的 25 个有用且省时的片段

levelup.gitconnected.com](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [## 20 个必要的代码片段，让你在 JavaScript 中像专家一样工作

### 你可以在 30 秒或更短时间内学会 20 个 JavaScript 代码片段

levelup.gitconnected.com](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [## 学习编码的同时在线赚钱的 20 种方法

### 如果你是一名程序员，却没有在网上赚到钱，那你就错过了一个大好机会

levelup.gitconnected.com](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [## 使用 NLTK 的 Python 自然语言处理初学者指南

### 自然语言处理是人工智能的一个分支，它帮助计算机理解自然语言

levelup.gitconnected.com](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [## 掌握 Python 的面向对象编程(OOP)

### 通过掌握面向对象编程(OOP ),学习用 Python 编写更简洁、更模块化的代码。

levelup.gitconnected.com](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [## 如何让你的 python 代码运行速度提高 10 倍

### 让您的 python 代码运行速度提高 10 倍的简单提示和指南

levelup.gitconnected.com](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [](/build-a-desktop-app-with-python-4a847e3b596c) [## 用 Tkinter 和 Python 构建桌面应用程序

### 在本文中，我们将学习如何使用 python 和 Tkinter 模块开发现代桌面应用程序。一个…

levelup.gitconnected.com](/build-a-desktop-app-with-python-4a847e3b596c) [](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4) [## 使用 Python 阅读和编辑 PDF 文档

### 在本文中，我们将了解如何使用 python pdf 模块来读写 pdf 文件。PyPDF2 是一个…

levelup.gitconnected.com](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4)