# JavaScript 算法:简单比较

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-simple-comparison-ca408f24b353>

![](img/81afb421218997317de46743694e79e3.png)

对于今天的简短算法，我们将编写一个名为`add`的函数，它将接受两个值作为输入。一个是字符串，一个是整数，`a`和`b`。

给你两个值，一个是整数，另一个是字符串。该函数的目标是查看两个值是否相同(不管它们的数据类型如何)。如果相同则返回`true`，如果不同则返回`false`。为了增加这个挑战，我们将在不使用任何 JavaScript 内置方法的情况下解决这个算法。

这里有一个简单的例子:

```
let a = "1";
let b = 1;
```

即使一个变量是字符串，另一个是整数，两个变量都有`1`作为值。该函数将返回`true`。

```
let a = "123";
let b = 132;
```

对于上面的例子，函数将返回`false`，因为两个变量没有相同的数字。

让我们用代码来解决这个问题。

对于这个问题，我们将在不使用内置方法将一个变量的数据类型转换为另一个变量的情况下比较值。JavaScript 有两种使用比较运算符比较两个数字的方法。

`===`是一个严格的比较运算符，这意味着在比较两个操作数时，它会同时考虑内容和数据类型。如果我们使用严格比较运算符比较`2`和`"2"`，比较将返回`false`。而字符串和整数 2 的内容，数据类型是不一样的。这两种数据类型要么都是字符串，要么都是整数。

另一个比较运算符叫做抽象比较，`==`。这只查看每个操作数的内容。抽象比较运算符的作用是在进行比较之前将操作数转换为相同的类型。

因此，对于我们的函数来说，如果我们想在不考虑数据类型的情况下比较两个输入，我们可以使用抽象比较运算符。

```
return a == b;
```

仅此而已。以下是我们代码的其余部分:

```
function add(a, b){
  return a == b;
}
```

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-first-reverse-85243cccdb3d) [## JavaScript 算法:先反向

### 我们要写一个函数，输出一个反过来写的字符串。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-first-reverse-85243cccdb3d) [](/javascript-algorithm-letter-changes-6182740d490d) [## JavaScript 算法:字母变化

### 我们将把一个字符串中的每一个字母都换成字母表中它后面的那个字母，同时进行另一个改变。

levelup.gitconnected.com](/javascript-algorithm-letter-changes-6182740d490d) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-longest-word-da608ff20244) [## JavaScript 算法:最长的单词

### 我们要写一个函数，输出一个句子中最长的单词。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-longest-word-da608ff20244)