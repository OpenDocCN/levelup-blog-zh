# 用于更好格式化的 5 个 Python 字符串方法

> 原文：<https://levelup.gitconnected.com/5-python-string-methods-for-better-formatting-a2c3b92a2d33>

![](img/c5a43b23e3dcc86567e2e2a362a99274.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

解析字符串不仅仅是获取您想要的值并继续前进。有时，您需要为控制台输出或向用户显示更优雅的格式。基本的打印和插值只能让你到此为止。无论您需要一个健壮的数据表还是一组格式清晰的帮助文本，Python 都有大量内置的字符串方法来完成大多数工作。

在本文中，我们将关注集成到 Python 中的普通字符串方法，并避免使用第三方库。当然，第三方库和包有更多的功能，但有时你不需要厨房水槽。您可能只需要几个格式整齐的字符串。这些方法将做到这一点，没有额外的库开销。让我们开始吧。

## 1.向左或向右对齐内容

```
ljust(width[,fill_character])
rjust(width[,fill_character])
```

首先，我们有两种优秀的字符串格式化方法，可以帮助你以更有序、更合理的格式打印出字符串。这些方法完全按照您的想法来做，它们将通过一些字符和使用您选择的分隔符(默认为空格)来使内容左右对齐。

在这个例子中，我们将使用这些 justify 方法在列中垂直对齐一些字符串。如果您想要快速打印出一个简单的数据表，这很有用。

```
string_1 = "column_1_string_1"
string_2 = "column_2_string_1"
string_3 = "column_1_string_2"
string_4 = "column_2_string_2"left_column = string_1 + string_2.rjust(20)
right_column = string_3 + string_4.rjust(20)print(left_column + '\n' + right_column)
```

## 2.创建标题

```
title()
```

这种方便的方法对于生成符合标题格式的字符串非常有用，其中每个单词的第一个字母都是大写的。您不必拆分单词并分别使用大写字母，也不必手动确保您的标题格式正确，您只需将字符串传递给这个方法，就可以得到一个简单、外观合适的标题。

如果您发现自己在为标题组合来自多个不同来源的字符串，这种方法也很方便。你不必关心每个来源的资本流入。第二个 print 语句展示了这将如何纠正最难看的字符串。

```
title_string = 'this is a title'print(title_string.title())really_bad_title_string = 'tHiS iS a TITLE'print(really_bad_title_string.title())
```

## 3.替换字符串中的单词

```
replace(old, new[, count ])
```

Replace 是几乎每种语言的经典工具箱方法。Python 也不例外。使用 replace 可以让您找到字符串中出现的单词或短语，并轻松地用新的替换它们。您甚至可以提供一个可选的`count`参数来替换这个单词出现的次数。

在这个例子中，我似乎忘记了我已经说过我喜欢苹果。该单词在字符串中出现了两次。让我们仅用`pears`替换第一次出现的内容。

```
my_string = 'I like apples, oranges and apples.'print(my_string.replace('apples', 'pears', 1))
```

## 4.将绳子居中

```
center(width[,fill_character])
```

这个方法是另一个非常简单，但微妙有效的方法。使用`center()`将允许你使用你选择的填充来居中对齐一个字符串。就像`ljust()`和`rjust()`一样，你也可以指定一个宽度和一个想要的填充字符来填充字符串。

这个方法可以很方便地与前面的`title()`方法一起使用，很好地将标题字符串居中。在这个例子中，我们将获得标题字符串的长度，这样我们就可以将它包含在中间填充中。让我们来看看。

```
poorly_formatted_title = 'baD tiTLE STRing'
formatted_title = poorly_formatted_title.title()padding = len(formatted_title) + 30print(formatted_title.center(padding))
print('some other body text that is not centered')
```

## 5.去掉多余的字符

```
strip([characters])
```

有时候你要格式化的字符串不一定是最干净的。它们可能来自用户或远程数据库等输入源。偶尔，您会遇到这些来源的字符串中有错误的字符。一串前导或尾随空格是一个经典问题，这取决于你是如何获得字符串的。有时，像制表符、回车符或其他转义序列这样的控制字符会直接出现在字符串中。

为了解决这个问题，我们有了`strip()`方法。该方法允许您默认删除前导/尾随空格，或者指定您自己要删除的字符。

```
my_string_with_spaces = '     oh no spaces everywhere!     'print(my_string_with_spaces)
print(my_string_with_spaces.strip())my_string_with_tabs = '\t\t\t\ttabs all over the place!\t\t\t\t'print(my_string_with_tabs)
print(my_string_with_tabs.strip('\t'))
```

*感谢您的阅读！Python 有更多的内置字符串方法，您可以使用它们来进行更有趣的格式化。在文档* [*这里*](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str) *查看更多方法。如果你热爱 Python，想要更多好玩的 Python 编码工具，那就看看* [*4 个工具，写出更好的 Python*](https://medium.com/better-programming/3-tools-for-writing-better-python-155b159faad8) *。*