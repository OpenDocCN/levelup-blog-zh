# Python 中你必须知道的正则表达式函数

> 原文：<https://levelup.gitconnected.com/regular-expression-functions-that-you-must-know-in-python-2961bf08a2e7>

## 你应该知道的正则表达式函数

![](img/e3f25b5fbfa2572c000b449d29278fcf.png)

christian buehner 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

正则表达式(regex)在从一段给定的文本中提取模式和信息时非常有用。正则表达式用于文本中的模式匹配，但另一方面，它被大多数程序员忽略了，因为他们发现学习和理解它很困难。

正则表达式可以用于各种目的，例如在文本中寻找模式、验证文本、web 抓取等等。

正则表达式最好的一点是，它们不局限于任何特定的编程语言。正则表达式的规则和表达式对于所有编程语言都是一样的。

在这篇文章中，我用正则表达式提出了从给定文本中提取信息的函数，并给出了一些例子。我们开始吧！

# 1.findall()

这个`findall()`函数返回与模式匹配的字符串列表。该方法从左到右扫描输入字符串，然后返回所有与模式匹配的非重叠结果。

这个方法接受两个参数，`pattern`和`string`作为输入。

> 语法:re.findall(模式，字符串)

```
import retext = "Hello 12, how are you? This is 13"
pattern = "\d+"result = re.findall(pattern, text)
print(result)Output:
['12', '13']
```

# 2.搜索()

这个`search()`方法用于在给定的文本/字符串中搜索特定的模式。如果在文本中找到匹配的模式，该方法返回一个**匹配**对象。否则，它返回`None`。

该方法将两个参数`pattern`和`string`作为输入。该方法在模式的第一次匹配后停止。因此，测试正则表达式比提取数据更有帮助。

> 语法:re.search(pattern，string，flags=0)

```
import retext = "Python is a programming language"#cheking if 'Python' is present at the beginning
match = re.search('\APython', text)if match:
    print("Pattern found")
else:
    print("Pattern not found") Output:
Pattern found
```

# 3.拆分()

这个`split()`函数在模式匹配时分割字符串，并返回模式分割新字符串的字符串列表。

该函数将两个参数`pattern`和`string`作为输入。第一个参数是表示正则表达式的模式。每当在字符串中找到该模式时，它就会拆分字符串。

> 语法:re.split(pattern，string，maxsplit=0)

```
import retext = "Hello 12, how are you? This is 13"
pattern = "\d+"result = re.split(pattern, text)
print(result)Output:
['Hello ', ', how are you? This is ', '']
```

# 4.sub()

这个`sub()`函数用于在一个字符串中寻找一个模式，然后用其他文本替换这个模式。该函数以三个参数`pattern`、`replace`、`string`作为输入。

pattern 参数用于查找文本中的模式，replace 参数是我们希望用提取的模式替换的文本，string 参数是我们希望在其中执行操作的文本。

该函数在用`replace`参数的内容替换匹配项后返回一个字符串。

> 语法:re.sub(模式，替换，字符串)

```
import retext = "Namaste World! How are you doing?"#replacing whitespace characters with '!!' 
result = re.sub("\s", "!!", text)print(result)Output:
Namaste!!World!!!How!!are!!you!!doing?
```

在这里，我们可以看到在输出字符串中，**空白字符**已经被替换为"！!"。

# 5.subn()

这个`subn()`方法和`sub()`方法差不多。唯一的区别是两种方法的输出。

`sub()`方法返回用`replace`参数文本替换匹配模式后的字符串，而`subn()`方法返回一个元组，该元组由修改后的字符串以及字符串中的替换数组成。

> 语法:re.subn(pattern，replace，string)

```
import retext = "Namaste world. I prefer to work at night"
pattern = "wo"
result = re.subn(pattern, "~", text)print(result)Output:
('Namaste ~rld. I prefer to ~rk at night', 2)
```

在这里，我们可以看到输出是一个元组，由修改后的字符串和替换数组成。

# 结论

这就是这篇文章的全部内容。在本文中，我们讨论了一些最常用的正则表达式函数。这些函数在查找文本模式和验证字符串时非常方便。

正则表达式不仅仅是这几个函数，但是通过阅读本文中的这些函数，您将清楚地了解正则表达式是如何工作的。

感谢阅读！

你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)。