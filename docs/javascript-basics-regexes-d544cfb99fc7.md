# JavaScript 基础—正则表达式

> 原文：<https://levelup.gitconnected.com/javascript-basics-regexes-d544cfb99fc7>

![](img/669f959bf4d0ca953ff49ffdaac92b7e.png)

安德斯·吉尔登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将看看如何定义和使用正则表达式。

# 分组子表达式

我们可以用括号将子表达式分组。

例如，我们可以写:

```
/foo+(bar)+/i.test('foobar')
```

`+`适用于任何带括号或括号前的字符。

在`o`的情况下，`+`应用于`o`。

第二个`+`是`bar`。

因为`'bar'`和`'o'`是字符串的一部分，所以模式匹配，所以表达式返回`true`。

# 匹配和分组

我们可以使用`exec`方法从字符串中返回匹配项。

例如，我们可以写:

```
/\d+/.exec("200 100");
```

然后我们得到作为匹配返回的`['200']`。

它还有一个`index`属性，返回匹配的字符串索引。

我们可以写:

```
const match = /\d+/.exec("200 100");
console.log(match.index);
```

然后我们在控制台日志中得到 0，因为这是匹配的第一个索引。

字符串有一个做同样事情的`match`方法。

例如，我们可以写:

```
"200 100".match(/\d+/)
```

我们得到了同样的结果。

如果我们有一个没有匹配的模式，那么索引将是`undefined`。

例如，我们可以写:

```
/correct(ly)?/.exec("correct")
```

然后我们得到:

```
['correct', undefined]
```

# 日期

JavaScript 的标准库有代表时间点的`Date`类。

要获得当前日期和时间，我们可以写:

```
new Date()
```

我们还可以传入参数来创建一个特定的日期。

例如，我们可以写:

```
new Date(2020, 0, 2)
```

这将返回:

```
Thu Jan 02 2020 00:00:00 GMT-0800 (Pacific Standard Time)
```

第二个参数是月份。0 表示一月。十二月份一直到 11 点。

我们可以为小时、分钟、秒和毫秒再传入 4 个参数。

我们可以写:

```
new Date(2020, 0, 2, 1, 1, 1, 500)
```

然后我们得到:

```
Thu Jan 02 2020 01:01:01 GMT-0800 (Pacific Standard Time)
```

我们可以使用`getTime`方法返回 UTC 1970 年 1 月 1 日以来的毫秒数，也就是 UNIX 时间。

我们可以通过书写来使用它:

```
new Date(2020, 0, 2).getTime()
```

并获得 1577952000000。

其他有用的方法还有`getFullYear`、`getMonth`、`getDate`、`getHours`、`getMinutes`、`getSeconds`来得到他们的零件。

我们可以使用单词边界来提取精确的单词。

例如，我们可以写:

```
/\bcat\b/.test("bobcat")
```

它返回`false`。

`\b`是字界图案。

这是反对:

```
/cat/.test("bobcat")
```

返回`true`。

# 选择模式

我们可以使用`|`符号来表示它所分隔的选项之间的匹配。

例如，我们可以写:

```
/dog|cat/.test('dog cat');
```

然后我们得到`true`。

# 替换方法

字符串有一个`replace`方法，让我们在字符串中搜索模式并替换它们。

例如，我们可以写:

```
'foobar'.replace(/[foo]/g, 'a')
```

然后我们得到:

```
"aaabar"
```

它用`a`替换了`foo`实例。

我们也可以使用它和特殊字符来放置匹配的字符串。

例如，我们可以写:

```
'12 456'.replace(/(\d{2}) (\d{3})/g, '$1')
```

然后我们得到`'12'`，因为我们只取了第一个组，并把它放入带有`$1`的替换字符串中。

# 贪婪

我们从一个字符串中串出所有匹配的条目。

例如，我们可以删除字符串中的所有数字:

```
'123 abc'.replace(/\d+/g, '')
```

然后我们得到返回的`' abc'`。

# 动态创建 RegExp 对象

我们可以使用`RegExp`构造函数来动态创建正则表达式。

我们可以通过书写来使用它:

```
const re = new RegExp('/\\d+/', 'g');
```

然后我们有一个 regex 对象来全局搜索数字。

第一个参数是 regex 字符串，第二个是可选标志。

![](img/3a006d1f18fb8b78d5d2e205e879e369.png)

约瑟夫·冈萨雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 搜索方法

我们可以使用`search`方法来搜索模式。

例如，我们可以写:

```
'abc 123'.search(/\d+/g)
```

然后我们返回 4。

无法从 0 以外的索引开始搜索。

# lastIndexOf

我们可以使用`exec`方法获得某个模式出现的最后一个索引。

例如，我们可以写:

```
const re = /(\d+)/g
re.exec('123 456');
console.log(re.lastIndex)
```

我们首先创建了一个正则表达式，然后对它运行`exec`来获得第一个匹配。

然后我们得到 3，因为数字的第二个实例从索引 3 开始。

# 结论

使用 regex 对象，我们可以以多种方式搜索项目。

我们还可以通过使用 regex 对象进行搜索来替换字符串中的项目。