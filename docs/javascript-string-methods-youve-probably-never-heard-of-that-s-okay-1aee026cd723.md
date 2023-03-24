# 你可能没听说过的 JavaScript 字符串方法

> 原文：<https://levelup.gitconnected.com/javascript-string-methods-youve-probably-never-heard-of-that-s-okay-1aee026cd723>

最后更新于 2019 年 6 月 1 日

![](img/5ae3c4b759d19014e0047cec3b30f551.png)

[OCTOPAL](https://allencapoferri.wordpress.com/tag/underwater-life-sketches/)

字符串是 JavaScript 的数据类型，用于表示可以包含*字母*、*数字*、*符号*、*标点、*甚至*表情符号*的*文本*。它们由零个或多个(16 位值)字符组成，用单引号`'`或双引号`"`括起来。

之前的介绍没什么特别的，但是刷新一下总是好的。有许多方法可以在字符串上调用，但我们中的许多人选择了通用语法。例如，几乎我们所有人都知道，要得到一个字符串的第一个字符，我们可以使用`*str*[0]`，这完全没问题，但是你猜怎么着！—这有一个内置的方法。

在这篇文章中，我将通过例子介绍你可能听说过或者没有听说过的 5 种 JavaScript 方法。

# 字符串方法和属性

因为字符串是原语之一(原语值，原语数据类型)，例如`'Mask Off'`不能有属性或方法(它本身不是一个对象)。幸运的是，JavaScript 定义了几个在字符串上调用的属性和方法，通过一种叫做[装箱](https://en.wikipedia.org/wiki/Object_type_(object-oriented_programming)#Boxing)的技术将它包装成一个对象。这些属性和方法是使用点符号来访问的。

> *📌*字符串在 JavaScript 中是不可变的；并且可以像只读数组一样对待。所有字符串方法都返回一个新值，并且它们不会修改调用它们的字符串。

# 示例设置

在我们深入研究细节之前，让我们首先设置一个用于所有示例的块:

```
const **content** = "*Forem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronics typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem IpsumL*";const { **length** }  = content;    // 500 characters
const **firstChar**   = 0;          // corresponds to F
const **lastChar**    = length - 1; // corresponds to L
const **midContent**  = length / 2; // corresponds to e
const **outOfRange**  = length * 2; // ""
```

# **charAt()方法**

`charAt()`方法返回指定索引处的字符，如果索引超出范围，则返回一个空字符串。如果未提供 index-param，则默认值为 0。

```
/**
 * @param  {number} ranges from 0 to the length of the string -1
 * @return {string}
 */*string*.charAt(*index*)
```

## charAt()示例

```
content.charAt() // "F"
content.charAt(**firstChar**)  // "F"
content.charAt(**lastChar**)   // "L"
content.charAt(**midContent**) // "e"
content.charAt(**outOfRange**) // ""
```

# startsWith()方法

`startsWith()`方法确定一个字符串是否以指定字符串的字符开始。

```
/**
 * @param  {string} string to search for
 * @param  {number} optional - index, defaults to 0
 * @return {boolean}
 */ *string*.startsWith(*string*, *position*)
```

> 📌`*startsWith()*`方法是**区分大小写的**。

## startsWith()示例

```
content.startsWith('F**'**) // true
content.startsWith('f**'**) // false
content.startsWith('e**', midContent**) // true
```

# endsWith()方法

方法确定一个字符串是否以一个指定字符串的字符结束。否则返回`false`

## endsWith()示例

```
content.endsWith('L', **lastChar**) // true
content.endsWith('l', **lastChar**) // false
```

# includes()方法

`includes()`方法让您确定一个字符串是否包含另一个字符串，返回`Boolean`

```
/**
 * @param  {string} string to search for
 * @param  {number} optional - index, defaults to 0
 * @return {boolean}
 */ *string*.includes(*string*, *position*)
```

## includes()示例

```
content.includes('Ipsum') // true
content.includes('1960s') // true
content.includes('Hello from outside') // false
```

# repeat()方法

`repeat()`方法构造并返回一个新的字符串，该字符串带有被调用字符串的指定数量的副本，*将*连接在一起。

```
/**
 * @param  {number} - indicating the number of times to repeat
 * @return {string}
 */ *string*.repeat(*count*)
```

> 📌重复计数必须为:非负、小于无穷大且不溢出最大字符串大小。

## 重复()示例

```
"aa".repeat(3)  // "aaaaaa"
"😺".repeat(3)  // "😺😺😺"
```

**总结一下:**上面提到的方法可以用不同的方法实现，它们可能对性能有害，也可能是最快的选择！但最终，结果将取决于您的需求。

我强烈推荐阅读完整的 [JavaScript 字符串参考](https://www.w3schools.com/jsref/jsref_obj_string.asp)以了解更多细节。