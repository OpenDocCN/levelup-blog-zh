# JavaScript 里没有 Spread 运算符这种东西！

> 原文：<https://levelup.gitconnected.com/there-is-no-such-thing-as-the-spread-operator-in-javascript-9c4e4dbd8a02>

![](img/1a297fde18fbbce558c32be77001bd58.png)

放松，深呼吸……——瓦莱丽娅·布加约娃在 Unsplash 上的照片

听说过[传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)吗？在 ES2015 中推出，由于其简单的语义和无处不在的用例，我们很喜欢它。那么[传播算子](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Spread_operator)呢？没错，就是 Spread 语法用的[三个点](https://guide.freecodecamp.org/javascript/es6/spread-operator/) ( `...`)！

…通过说这样的话，我们开始挖掘一只虫子生活的可怕世界

# Spread 语法快速回顾

我们可以利用 Spread 语法的一个简单用例是当我们想要连接多个数组时。查看以下代码片段:

```
const clientErrors = ['err1', 'err2', 'err3'];
const serverErrors = ['err4', 'err5']; function numberOfErrors(clientErrors, serverErrors) {
  // Assuming that both inputs are arrays to prevent TypeErrors.
  return [...clientErrors, ...serverErrors].length;
}numberOfErrors(clientErrors, serverErrors); // => 5
```

函数`numberOfErrors`连接两个数组并返回新数组的长度。但是如果参数是假值，比如`null`或`undefined`，那该怎么办呢？

```
const clientErrors = ['err1', 'err2', 'err3'];
const serverErrors = null;function numberOfErrors(clientErrors, serverErrors) {
  return [...clientErrors, ...serverErrors].length;
}numberOfErrors(clientErrors, serverErrors);// => TypeError
```

我们知道，如果我们试图传播一个`null`或`undefined`变量，这将使解释器变得唠叨。在现实世界的场景中，我们希望保护自己免受这种边缘情况的影响。稍加修改，我们最终会写出这样的代码:

```
const clientErrors = ['err1', 'err2', 'err3'];
const serverErrors = nullfunction numberOfErrors(clientErrors, serverErrors) {
  return [...(clientErrors || []), ...(serverErrors || [])].length;
}numberOfErrors(clientErrors, serverErrors) // => 3
```

因为`serverErrors`是 falsy，所以逻辑 OR 操作符将返回一个空数组，然后该数组将被优雅地展开。调用`numberOfErrors`的最终结果等于`clientErrors`数组的长度，也就是`3`。

# 扩展运算符优先级

现在我们已经讨论了一个基本的例子，让我们来看一些更有趣的东西。针对以下每个问题，标记正确答案。解决方案将在之后立即呈现。 *(* ***提示*** *:可以自己运行代码片段看看结果！)*

## 问题 A

```
const a1 = null;
const b1 = [1, 2];const c1 = [...a1 || b1];
```

`c1`的值是多少？

1.  `c1`没有价值。表情`...a1`会丢`TypeError`，因为`a1`是`null`。
2.  `c1`就是`[1, 2]`。表达式`a1 || b1`将首先被求值，然后返回`[1, 2]`，它将被展开。

## 问题 B

```
const a2 = [1, 2];
const b2 = null;const c2 = [...a2 || b2];
```

1.  `c2`就是`[1, 2]`。先对表达式`a2 || b2`求值，会展开。
2.  `c2`就是`[1, 2]`。先对表达式`…a2`求值，会展开。

## 问题 C

```
const a3 = null;
const b3 = null;const c3 = [...a || b];
```

1.  `c3`没有价值。表达式`...a3`会抛出`TypeError`，因为`a3`就是`null`。
2.  `c3`没有价值。表达式`a3 || b3`将首先求值，返回`null`，然后展开语法将抛出`TypeError`。

## 答案

```
A. 2
B. 1 
C. 2
```

如果碰巧你对上述问题中的至少一个回答不正确，那么你可能陷入了运算符优先级的陷阱。点标点符号`…`比逻辑 OR `||`优先级高，还是反过来？传播运算符的优先级是什么？正确答案是:**没关系，因为 JavaScript 中没有 Spread 运算符这种东西！**

# 跨页符不存在！

当我们试图评估像`[…array || []]`这样的表达式时，检查操作符的优先级是合乎逻辑的。关于 Spread 语法，Web 中存在一个常见的误解，它被表示为一个操作符。

一个[很棒的回答](https://stackoverflow.com/a/44934830)由[李国能](https://stackoverflow.com/users/5647260)发布在 Stack Overflow，值得一提，总结了 Spread 语法的本质。

ECMAScript 2015 规范本身可以直接检索到最令人难忘的论点之一:

> 完整的运算符列表在 [ECMAScript 2015 语言规范](http://www.ecma-international.org/ecma-262/6.0/)的第 12.5 至 12.15 条中列出，该规范中引入了``…`，但未提及``...`。— [李国能回答](https://stackoverflow.com/a/44934830)

另外值得一提的一点是*“操作符是一个内置函数[..]表示* ***的计算结果正好是一个值。"*** 。如果我们试图在 Web 控制台中运行类似于`const a = …b`的语句，其中`b`是一个数组，那么我们将`SyntaxError`。

Spread 语法的工作方式是首先评估其参数，然后传播结果。因此，`[…a || b]`的行为与`[…(a || b)]`完全相同。在`a || b`表达式周围放一组括号有助于消除歧义。

> 作为一个实用的参考，首先评估 Spread Syntax 的参数，然后进行扩展。