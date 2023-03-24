# Python lambda 拯救世界

> 原文：<https://levelup.gitconnected.com/python-lambda-to-the-rescue-5eb9f30a595a>

![](img/cd2b77ddceeccfd8f383f761e377d904.png)

由[麦克斯韦·纳尔逊](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 一点历史

Python 和其他编程语言中的 Lambda 表达式源于 lambda calculus，一种由阿隆佐·邱奇发明的计算模型。阿隆佐·邱奇在 20 世纪 30 年代形式化了λ演算 T21，一种基于纯抽象的语言。Lambda 函数也被称为 lambda 抽象，直接引用阿隆佐·邱奇最初创造的抽象模型。

函数式语言直接继承了 lambda 演算哲学，采用强调抽象、数据转换、组合和纯度(无状态和无副作用)的声明式编程方法。虽然 python 本质上不是一种函数式编程语言，但它很早就采用了一些函数式概念。1994 年 1 月，语言中增加了`map()`、`filter()`、`reduce()`和`lambda`运算符。

# λ函数

Python 中的`lambda`表达式只是一个匿名函数。由于语法的原因，它比常规函数稍微受限一些，但是通过它可以做很多事情。一个`lambda`函数可以带**任意**个参数，但只能有**一个**表达式。

因为`lambda`是一个表达式，而不是一个语句，所以它可以用在`def`不能用的地方(例如，在字典文字表达式或函数调用的参数列表中)。因为 lambda 计算单个表达式而不是运行语句，所以它不适合复杂函数(使用`def`)

首先让我们看看`lambda`函数的语法，然后我们将在一个简单的例子中使用它。

```
lambda arguments: expression
```

现在让我们实现第一个使用 lambda 函数的例子。在这个例子中，我们将使用 python 的`sorted`函数。下面是`sorted`函数的语法:

```
sorted(iterable, key=None, reverse=False)
```

因此，我们将使用 lambda 函数作为`sorted`函数中的`key`。

看看我们如何将 lambda 参数定义为`t`，将 expression 定义为`t.value`。虽然该函数可以单独编写，但这是获得您想要的快速排序函数的一种简单方法。这并不是说常规函数会很冗长，但是通过使用匿名函数，您有一个小小的优势；你没有用额外的函数污染你的局部作用域。该示例的输出是:

```
[Test: 1, Test: 5, Test: 15, Test: 55, Test: 62]
```

# 一些漂亮的例子

`lambda`主要用于内置函数`map`和`filter`。与`lambda`一起使用，这些函数提供了紧凑的方式来执行一些伟大的操作，同时避免了对循环的需要。

## 旁边有地图

在第一个例子中，我们将使用`map`函数和`lambda`创建一个范围为`0`到`8`的平方数列表。

```
>>> list(map(lambda x: x ** 2, range(8)))
[0, 1, 4, 9, 16, 25, 36, 49]
```

在第二个例子中，我们将使用`map`和`lambda`大写`string`中的所有单词。

```
>>> message = "we are going to capitalize all words in this sentence"
>>> " ".join(list(map(lambda x: x.capitalize(), message.split(" "))))
'We Are Going To Capitalize All Words In This Sentence'
```

## 与过滤器并排

在第一个例子中，我们将过滤所有符合条件`number%3==0.`的数字

```
>>> filter_me = [1, 2, 3, 4, 6,7 ,8, 11, 12, 14, 15, 19, 22, 33, 51, 62, 75]
>>> list(filter(lambda x: x%3==0, filter_me))
[3, 6, 12, 15, 33, 51, 75]
```

在第二个例子中，我们将过滤所有包含`find`和`me`序列的字符串。

```
>>> import re
>>> filter_me = ["Ok", "maybe this", "find not me", "find me", "test find", "test me", "test find me"]
>>> list(filter(lambda x: re.match("^.*find.*me.*$", x), filter_me))
['find not me', 'find me', 'test find me']
```

# 告诉我这件事

至于样式，请注意`PEP8`表明将 lambda 赋给变量是一个坏主意。从逻辑上来说，是的。匿名函数的思想就是它就是匿名的。如果你给它一个身份，你应该把它定义为一个普通的函数。如果你想保持简短，它真的不会太长。请注意，以下语句被视为不良风格，仅用于示例目的:

```
# Bad style (Do not assign lambda to a variable)
key = lambda spam: spam.value
```

> lambda 函数唯一有效的用例是作为函数参数使用的匿名函数，并且最好是在它们足够短以适合单行的情况下。([精通 Python](https://www.amazon.com/Mastering-Python-beautiful-powerful-features/dp/1785289721) ，**瑞克·范·哈特姆**)

# 结论

虽然在 Python 中，lambda 函数非常有限，但是有一些使用它们的有趣用例。记住**简单比复杂好。因此，如果你想写简单的代码，lambda 函数可以帮你。**