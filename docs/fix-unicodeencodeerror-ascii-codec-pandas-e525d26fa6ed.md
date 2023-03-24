# 如何修复 unicode encoded error:“ascii”编解码器无法对 Python 和 Pandas 中的字符进行编码

> 原文：<https://levelup.gitconnected.com/fix-unicodeencodeerror-ascii-codec-pandas-e525d26fa6ed>

## 了解如何摆脱`UnicodeEncodeError when working with strings in Python and Pandas`

![](img/5c6b7e8f0a7de7486b21fef7d281dc53.png)

Philipp Katzenberger 在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

## 介绍

使用 unicode 字符时出现的一个非常常见的 Python 错误是`UnicodeEncodeError`。

```
UnicodeEncodeError: 'ascii' codec can't encode character u'\u03b1' in position 20: ordinal not in range(128)
```

此外，当将熊猫数据帧写入`csv`文件时，一个常见的缺陷是字符编码。特别是，`to_csv()`也可能触发`UnicodeEncodeError`。

在今天的简短指南中，我们将讨论字符编码以及如何在使用`to_csv`将 dfs 写入 CSV 文件时指定字符编码。此外，我们将探索几种不同的解决上述错误的方法。

请注意，这个问题主要与 Python 2 用户有关。

## 重现错误

现在让我们启动 Python 2 终端并创建一个 unicode 字符串。

```
>>> my_str = u'Hey y\u00E0'
>>> my_str
u'Hey y\xe0'
```

现在，如果我们试图用`my_str`调用`str()`方法，就会出现一个错误:

```
>>> str(my_str)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
**UnicodeEncodeError: 'ascii' codec can't encode character u'\xe0' in position 5: ordinal not in range(128)**
```

## 检查您的 Python 版本

您可能想使用 Python 3，但是您运行的是 Python 2，这可能就是您的代码会出现上述问题的原因。

您可以使用`python --version`命令来检查您正在本地运行的 Python 版本。举个例子，

```
$ python --version
Python 3.7.7
```

如果您错误地使用了 Python 2，那么请确保将默认版本更改为 3。或者，您可以通过调用`python3 ...`来使用 Python 3。

## 编码字符串

现在，如果您仍然希望(或者无法避免)使用 Python 2，那么您需要对您的字符串进行适当的编码。我们之前再现的`UnicodeEncodeError`是由于这样一个事实:当调用`str()`方法时，解释器将使用默认的字符编码来编码输入字节(在我们的例子中，这些是 unicode 字符)。

为了消除错误，您应该显式指定所需的编码。这可以通过使用`[encode()](https://docs.python.org/3/library/stdtypes.html#str.encode)`方法来实现，如下所示。在大多数情况下，`utf-8`编码会起作用。

```
>>> my_str.encode('utf-8')
>>> print(my_str.encode('utf-8'))
Hey yà
```

## 与熊猫一起工作时处理 UnicodeError 错误

现在，当使用`[pandas.DataFrame.to_csv()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_csv.html)`方法将熊猫数据帧写入 CSV 文件时，您也有机会获得`UnicodeEncodeError`。

如果是这种情况，那么您需要做的就是在调用对应于输出文件中使用的编码的`to_csv`时传递`encoding`参数。

```
my_df.to_csv('output.csv', encoding='utf-8')
```

## 最后的想法

在今天的文章中，我们讨论了最常见的错误之一，即`UnicodeEcnodeError`的潜在解决方案，以及如何通过正确处理 unicode 字符和字符串来消除它。

此外，我们展示了如何在 pandas 的上下文中解决这个问题，使用了一个示例，其中负责将 pandas 数据帧写入 CSV 文件的`to_csv()`方法可能会引发这个错误。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**你可能也会喜欢**

[](https://towardsdatascience.com/fix-pandas-parser-error-tokenizing-data-889167292d38) [## 如何修复 pandas.parser.CParserError:标记数据时出错

### 了解在 pandas 中读取 CSV 文件时出现错误的原因以及如何处理该错误

towardsdatascience.com](https://towardsdatascience.com/fix-pandas-parser-error-tokenizing-data-889167292d38) [](https://towardsdatascience.com/augmented-assignments-python-caa4990811a0) [## Python 中的扩充赋值

### 了解增强赋值表达式在 Python 中的工作方式，以及为什么在使用它们时要小心…

towardsdatascience.com](https://towardsdatascience.com/augmented-assignments-python-caa4990811a0) [](https://towardsdatascience.com/python-linked-lists-c3622205da81) [## 如何在 Python 中实现链表

### 探索如何使用 Python 从头开始编写链表和节点对象

towardsdatascience.com](https://towardsdatascience.com/python-linked-lists-c3622205da81)