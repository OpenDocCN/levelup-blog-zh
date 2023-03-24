# 有用的 Python 字符串方法

> 原文：<https://levelup.gitconnected.com/useful-python-string-methods-63dd05df2008>

![](img/43e36a4a37dd8e687b5da7569494967b.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将了解如何使用 Python 字符串方法来操作字符串。

# upper()、lower()、isupper()和 islower()方法

方法将一个字符串的所有字符转换成大写并返回它。

例如，给定以下字符串:

```
msg = 'Hello Jane'
```

然后运行`msg.upper()`返回`‘HELLO JANE’`。

`lower`方法将一个字符串的所有字符转换成小写并返回。

因此，`msg.lower()`返回`‘hello jane’`。

`isupper`检查整个字符串是否转换成大写。

例如，如果我们有:

```
msg = 'HELLO JANE'
```

然后`msg.isupper()`返回`True`。

`islower`检查整个字符串是否转换成小写。例如，给定以下字符串:

```
msg = 'hello jane'
```

然后`msg.islower()`返回`True`。

`upper`和`lower`可以链接在一起，因为它们都返回字符串。

例如，我们可以写:

```
msg.upper().lower()
```

然后我们得到:

```
'hello jane'
```

已退回。

# isX()方法

还有其他方法来检查字符串的各个方面。

`isalpha`检查整个字符串是否仅由字母组成并且不为空。

例如，给定以下字符串:

```
msg = 'hello jane'
```

然后`msg.isalpha()`返回`False`，因为其中有一个空格。

`isalnum`检查是否是仅由字母和数字组成的字符串，如果是则返回`True`。

例如，给定以下字符串:

```
msg = 'hello'
```

然后`msg.isalnum()`返回`True`。

`isdecimal`返回`True`是仅由数字字符组成且不为空的字符串。

例如，如果我们有:

```
msg = '12345'
```

然后`msg.isdecimal()`返回`True`。

如果字符串仅由制表符、空格和换行符组成并且不为空，则`isspace`返回`True`。

例如，如果我们有以下字符串:

```
msg = '\n '
```

然后`msg.isspace()`返回`True`。

如果字符串中只有以大写字母开头、后跟小写字母的单词，则`istitle`返回`True`。

例如，如果我们有以下字符串:

```
msg = 'Hello World'
```

然后`msg.istitle()`返回`True`。

# startswith()和 endswith()方法

如果一个字符串以作为参数传入的子字符串开始，则`startswith`方法返回`True`。

例如:

```
'Hello, world'.startswith('Hello')
```

返回`True`。

如果一个字符串以作为参数传入的子字符串结尾，则`endswith`方法返回`True`。

例如:

```
'Hello, world!'.endswith('world!')
```

返回`True`，因为我们的字符串以`world!`结尾。

# join()和 split()方法

`join`方法通过调用它的字符将字符串数组中的多个字符串组合成一个字符串。

例如，我们可以写:

```
','.join(['apple', 'orange', 'grape'])
```

返回`‘apple,orange,grape’`。

调用它的字符串被插入到条目之间。

`split`方法用于根据调用它的字符将一个字符串分成一系列子字符串。

例如:

```
'My name is Jane'.split(' ')
```

返回`[‘My’, ‘name’, ‘is’, ‘Jane’]`。

![](img/14f6cd4e934e86c8f7ece6005e956ae8.png)

照片由[萨加尔·达尼](https://unsplash.com/@sagardani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 用 partition()方法拆分字符串

`partition`方法将一个字符串分割成分隔符字符串前后的文本。

例如:

```
'My name is Jane'.partition('is')
```

退货:

```
('My name ', 'is', ' Jane')
```

我们可以使用多重赋值语法将各部分赋给它们自己的变量，因为调用的字符串总是分成 3 部分。

例如，我们写下如下内容:

```
before, sep, after = 'My name is Jane'.partition('is')
```

那么`before`具有值`‘My name ‘`。`sep`是`'is'`，`after`是`' Jane'`。

# 用 rjust()、ljust()和 center()方法对齐文本

`rjust`方法将一个字符串向右移动作为参数传递的给定数量的空格。

例如:

```
'foo'.rjust(5)
```

退货:

```
'  foo'
```

它还需要第二个参数来填充东西而不是空格。例如，`‘foo’.rjust(5, ‘-’)`返回`‘--foo’`

`ljust`根据传入文本右侧参数的数量添加空格。

例如:

```
'foo'.ljust(5)
```

退货:

```
'foo  '
```

它还需要第二个参数来填充东西而不是空格。例如，`‘foo’.ljust(5, ‘*’)`返回`‘foo**’`

`center`方法将传入的空格数作为参数添加到字符串的左边和右边。

例如:

```
'foo'.center(15)
```

退货:

```
'      foo      '
```

它还需要第二个参数来填充东西而不是空格。例如，`‘foo’.center(5, ‘*’)`返回`‘*foo*’`。

# 结论

Python 有将字符串转换成大写和小写的字符串方法。

我们还可以在字符串中添加空格和其他字符。

多个字符串也可以连接在一起。此外，它们可以被分割成多个字符串。

还有许多方法来检查字符串的各种特征。