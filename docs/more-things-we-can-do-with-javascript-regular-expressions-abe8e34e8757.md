# 带有 JavaScript 正则表达式的高级特殊字符和 RegeEx 方法

> 原文：<https://levelup.gitconnected.com/more-things-we-can-do-with-javascript-regular-expressions-abe8e34e8757>

![](img/fb33a5ffbce32e44c950d16d777f09c3.png)

帕特里克·布林克斯马在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

正则表达式让我们可以轻松地操作字符串。它们是让我们以我们想象的任何方式匹配文本的模式。没有它，我们将很难搜索具有复杂模式的文本。根据正则表达式检查输入对于表单验证也很有用。在本文中，我们将研究正则表达式和 JavaScript 正则表达式方法中的高级特殊字符。

我们可以用如下文字定义 JavaScript 正则表达式:

```
const re = /a/
```

或者，我们可以使用正则表达式构造函数，通过向`RegExp`构造函数传递一个字符串来定义正则表达式对象，如下所示:

```
const re = new RegExp('a');
```

# 更多特殊字符

我们可以使用更多的特殊字符来组成正则表达式:

## `[^xyz]`

匹配不在括号中的任何字符，并首先显示。例如，如果我们有字符串`xylophone`和模式`[^xyz]`，那么它匹配`l`，因为它是`xylohphone`中第一个不是`x`、`y`或`x`的字符。

## [\b]

此模式匹配一个退格字符。

## \b

这个模式匹配一个单词边界。单词边界是字符串在单词字符和非单词字符之间的位置。

例如，如果我们有模式`\babc`和字符串`abc`，那么我们得到`abc`作为匹配，因为我们在字符串的开头有`\b`。

如果我们有`abc\b`，那么我们得到`abc`作为`abc 1`的匹配，因为`\b`匹配字符串末尾的边界，因为我们在`abc`之后有空格。

`abc\babc`不会匹配任何内容，因为在`\b`之前和之后都有单词字符，所以没有单词边界，

## \B

匹配非单词边界。它匹配字符串的第一个字符之前、最后一个字符之后、两个单词字符之间、两个非单词字符之间或一个空字符串。

例如，如果我们有模式`ab\B.`和字符串`abc 1`，那么`abc`将是匹配的。

## \cX

这个模式匹配一个字符串的控制字符，其中`X`是 A 到 z。

## \d

匹配任何数字。和`[0-9]`一样比如`\d`匹配 123 中的 1。

## \D

该模式匹配任何非数字字符。和`[^0-9]`一样。例如，`\D`匹配字符串`abc123`中的`a`。

## \f

匹配换页符。

## `\n`

匹配换行符。

## \r

匹配回车符。

## \s

匹配一个空白字符。

## \S

匹配任何非空白字符。

## \t

匹配制表符。

## \v

匹配垂直制表符。

## \W

匹配任何非单词字符。和`[^A-Za-z0-9_]`一样。例如，如果我们有一个字符串`/.`，那么我们得到`/`作为匹配。

## \n

如果`n`是一个正整数，那么它是与`n`捕获组匹配的最后一个子串的反向引用。

例如，如果我们有一个正则表达式`a(_)b\1`和字符串`a_b_c`，那么我们得到匹配`a_b_`和`_`，因为我们在两个子字符串中都有`_`。

## `\0`

匹配空字符。

## \xhh

匹配代码为`hh` 的字符，其中`hh`为两位十进制数字。例如，由于`©`的十六进制代码是`A9`，我们可以将其与模式`\xA9`进行匹配。

## \呃

用代码`hhhh`匹配字符，其中`hhhh`是 4 个十六进制数字。

## `\u{hhhh}`

用 Unicode 值`hhhh`匹配字符，其中`hhhh`是 4 个十六进制数字。

![](img/c721acf75e6a396fe71d3b704a35c092.png)

照片由 [Etty Fidele](https://unsplash.com/@fideletty?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 正则表达式方法

JavaScript 正则表达式对象有几个方法，可以让我们用正则表达式做各种事情，比如搜索字符串、测试字符串是否匹配某个模式、替换字符串等等。

方法如下。

## 高级管理人员

`exec`方法在字符串中搜索正则表达式的匹配项。如果没有匹配，它返回结果数组或`null`。

例如，如果我们写:

```
/[a-z0-9.]+@[a-z0-9.]+.[a-z]/.exec('[abc@abc.com](mailto:abc@abc.com)')
```

然后我们回来了:

```
["[abc@abc.com](mailto:abc@abc.com)", index: 0, input: "[abc@abc.com](mailto:abc@abc.com)", groups: undefined]
```

我们也可以使用`g`标志来搜索匹配的整个字符串。例如，我们可以写:

```
/\d+/ig.exec('123')
```

然后我们得到 123 作为匹配。

## 试验

`test`方法搜索正则表达式和指定字符串之间的匹配。如果匹配，它返回`true`，否则返回`false`。

比如 `/foo/.test(‘foo’)`返回`true`，而`/foo/.test(‘bar’)`返回`false`。

## 比赛

`match`方法返回一个字符串与一个正则表达式的所有匹配。

例如，如果我们写:

```
'table tennis'.match(/[abc]/g)
```

然后我们回来了:

```
["a", "b"]
```

因为我们有了`g`标志来搜索整个字符串进行匹配。如果我们移除`g`标志，那么我们只得到第一个匹配。例如，如果我们写:

```
'table tennis'.match(/[abc]/)
```

然后我们回来了:

```
["a", index: 1, input: "table tennis", groups: undefined]
```

## `**matchAll**`

`matchAll`方法返回一个字符串与迭代器中正则表达式的所有匹配，这让我们可以用 spread 操作符或`for...of`循环得到结果。

例如，如果我们写:

```
[...'table tennis'.matchAll(/[abc]/g)]
```

然后我们回来了:

```
0: ["a", index: 1, input: "table tennis", groups: undefined]
1: ["b", index: 2, input: "table tennis", groups: undefined]
```

因为我们有了`g`标志来搜索整个字符串进行匹配。如果我们移除`g`标志，那么我们只得到第一个匹配。例如，如果我们写:

```
[...'table tennis'.matchAll(/[abc]/)]
```

然后我们回来了:

```
0: ["a", index: 1, input: "table tennis", groups: undefined]
```

## 搜索

`search`方法获取字符串匹配的索引。如果没有找到匹配，则返回-1。例如，我们可以写:

```
'table tennis'.search(/[abc]/)
```

然后我们得到 1，因为我们有了作为第二个字符的`'table tennis'`。

然而，如果我们有:

```
'table tennis'.search(/[xyz]/)
```

然后我们返回-1，因为所有 3 个字母在`'table tennis'`中都不存在。

## 替换

方法在一个字符串中寻找匹配，然后用我们指定的字符串替换匹配。

例如，如果我们有:

```
'table tennis'.replace(/[abc]/, 'z')
```

然后我们得到:

```
"tzble tennis"
```

## 使分离

我们可以使用`split`方法根据我们作为分隔符输入的模式来分割一个字符串。它返回一个字符串数组，这些字符串是根据正则表达式的匹配项从原始字符串中拆分出来的。

例如，如果我们有:

```
'a 1 b 22 c 3'.split(/\d+/)
```

然后我们回来了:

```
["a ", " b ", " c ", ""]
```

正如我们所看到的，有很多字符和它们的组合，我们可以用 JavaScript 正则表达式来搜索。它让我们非常容易地进行字符串验证和操作，因为我们不必拆分它们并检查它们。我们要做的就是用正则表达式对象来搜索它们。

同样，通过复杂的模式来拆分和替换字符串也可以通过正则表达式对象以及它们各自的`split`和`replace`方法来简化。