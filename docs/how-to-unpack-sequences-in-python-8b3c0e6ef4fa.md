# Python 序列解包入门

> 原文：<https://levelup.gitconnected.com/how-to-unpack-sequences-in-python-8b3c0e6ef4fa>

## 序列解包介绍

![](img/ef0174dadc9ee7d37f4e4d49399cdc1f.png)

照片由[像素](https://www.pexels.com/photo/grayscale-photo-of-computer-laptop-near-white-notebook-and-ceramic-mug-on-table-169573/)上的[负空间](https://www.pexels.com/@negativespace)拍摄

无论您是软件工程师还是数据科学家，对数据结构和算法有深刻的理解都是很有价值的。在这篇文章中，我将介绍在 python 中解包不同类型序列的过程。这篇文章中的例子是基于 [*Python 食谱*](https://d.cxcore.net/Python/Python_Cookbook_3rd_Edition.pdf) 的“数据结构和算法”一章。

我们开始吧！

首先，通过在终端中键入“python”并按 enter 键打开 python 交互式 shell(确保您安装了 python)。

```
$ python
```

对于我们的例子，我们将使用股票市场信息。首先，让我们定义一个名为“favorite_stocks”的元组:

```
>> favorite_stocks = (‘MSFT’, ‘AAPL’)
```

元组用括号指定，它们是一系列不可变的 python 对象。这仅仅意味着，与列表不同，不能对元组进行元素赋值。

如果变量的数量和结构与序列相匹配，那么上面这样的序列可以被解包到变量中。例如:

```
>> x, y = favorite_stocks 
```

如果我们执行“x ”,我们会得到:

```
>> x
'MSFT'
```

接下来，让我们定义一个名为“stock_data”的列表:

```
>> stock_data = ['MSFT', 100, 162, (2020, 02, 28)]
```

第一个元素是股票名称，第二个是股票数量，第三个是股票价格，最后一个元素是日期。我们可以用以下方式解开这个序列:

```
>> name, shares, price, date = stock_data
```

如果我们执行“股份”,我们会得到:

输入:

```
>>shares 
```

输出:

```
100
```

如果我们执行 date，我们会得到:

输入:

```
>>date
```

输出:

```
(2020, 02, 28)
```

这是另一个元组。我们可以通过以下方式进一步解包这个元组:

```
>> name, shares, price, (year, month, day) = stock_data
```

例如，如果我们执行一个月，我们会得到:

输入:

```
>>month
```

输出:

```
02
```

当解包变量时，您需要确保元素的数量没有不匹配，否则您会得到一个错误。让我们回到我们的“最喜爱的股票”的例子:

输入:

```
>>x, y, z = favorite_stocks
```

输出:

```
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
ValueError: not enough values to unpack (expected 3, got 2)
```

序列解包可以应用于任何可迭代的对象。这包括字符串、迭代器和生成器。例如，如果我们有‘MSFT:

```
>>stock_name = ‘MSFT’
```

我们可以如下解包字符串:

```
>>a, b, c, d = stock_name 
```

输入:

```
>>a
```

输出:

```
'M'
```

解包变量时，也可以用下划线“_”来表示一次性变量(不使用的变量)。例如:

```
>>_, b, c, _= stock_name
```

在这里，你只会对使用 b 和 c 感兴趣。

我将讨论的最后一件事是从任意长度的可重复项中解包元素。假设我们有一个网站的用户记录，包括用户名、电子邮件和在网站上花费的时间列表:

```
>>user_record = ('robin1991', 'robin91@gmail.com', 5, 4, 2, 8, 7)
```

我们可以用以下方式打开包装:

```
>>user_name, email, *hours = user_record
```

执行“用户名”，我们得到:

输入:

```
>>user_name
```

输出:

```
'robin1991'
```

执行我们得到'小时数'，

输入:

```
>>hours
```

输出:

```
[ 5, 4, 2, 8, 7]
```

我希望这能对你有所帮助。有问题请留言评论！