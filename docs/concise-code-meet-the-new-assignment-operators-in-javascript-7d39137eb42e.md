# 简明代码:了解 JavaScript 中新的逻辑赋值操作符

> 原文：<https://levelup.gitconnected.com/concise-code-meet-the-new-assignment-operators-in-javascript-7d39137eb42e>

现在有了三个新的逻辑运算符:nullish、AND 和 OR

![](img/7fda77d9538b902fae9f8c5df3989769.png)

选择，选择:幸福的无知||令人不快的真相

想少写代码？

让我们来看看 JavaScript 中可用的新赋值操作符，这些操作符在 [Firefox 79](https://hacks.mozilla.org/2020/07/firefox-79/) 和[Chrome 85](https://blog.chromium.org/2020/07/chrome-85-upload-streaming-human.html)(node . js 中尚未提供)中随时可用。

[逻辑赋值操作符建议](https://github.com/tc39/proposal-logical-assignment)指定了新的逻辑操作符来帮助快速编写更清晰的赋值 JavaScript 代码。

有 QQ equals(逻辑零赋值)、And And Equals(逻辑 And 赋值)和 Or Or Equals(逻辑 Or 赋值)，每一种都提供了一种更好的方法来使用简化的便利运算符更新和赋值。

**新操作符与已经实现的现有通用逻辑操作**具有相同的短路行为，例如加号等于(`+=`也称为加法赋值)和 JavaScript 必须提供的所有其他有用的复合赋值操作符。

我认为这些新的操作符非常有用，因为只有在满足某些逻辑条件时，它们才提供了改变赋值的简便方法。

## 我们没有不必要的副作用，只是通过快速、简洁的操作将值合并成变量。

让我们看看三个新的操作符。

# 逻辑零赋值(？？=)

第一个是[逻辑无效赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_nullish_assignment)。逻辑 nullish 赋值只在左边的变量是 nullish 时赋值，在 JavaScript 中这意味着它要么是`null`要么是`undefined`。另见[无效合并操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)。

通过写这个声明:

```
x ??= y
```

等效的逻辑解释如下:

```
x ?? (x = y);
```

# 例子

```
const book = { title: 'Dogs' };
​
// book.title is NOT nullish
book.title ??= 'Cats';
console.log(book.title);
// expected output: 'Dogs'
​
// book.author is nullish
book.author ??= 'Cameron Manavian';
console.log(book.author);
// expected output: 'Cameron Manavian'
```

所以如果`x`是 nullish，这个操作符将更新这个值，但是如果`x`不是 nullish，它将保持现有值不变。

# 逻辑 AND 赋值(&&=)

下一个运算符是[逻辑 and 赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND_assignment)。只有当左边的变量是[真值](https://developer.mozilla.org/en-US/docs/Glossary/truthy)时，逻辑 AND 赋值才会赋值，在 JavaScript 中，这意味着它是任何不是[假值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)的东西——任何不是`false`、`0`、`""`、`null`、`undefined`和`NaN`以及其他一些值的东西。

这就像你如何使用[逻辑 AND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) 一样，除非左边的表达式为真，否则`&&`的右边不会被求值。

通过写这个声明:

```
x &&= y
```

等效的逻辑解释如下:

```
x && (x = y);
```

# 例子

```
const book = { title: 'Dogs', description: '' };
​
// book.title is truthy
book.title &&= 'Cats and Dogs';
console.log(book.title);
// expected output: 'Cats and Dogs'
​
// book.author is falsy
book.author &&= 'Cameron Manavian';
console.log(book.author);
// expected output: undefined
​
// book.description is falsy
book.description &&= 'This is a short book!';
console.log(book.description);
// expected output: ''
```

因此，如果`x`为 true，这个操作符将更新值，但是如果`x`为 falsy，它将保持现有值不变。

# 逻辑 OR 赋值(||=)

最后，我们有[逻辑 or 赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR_assignment)。逻辑 OR 赋值只在左边的变量是 [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 时进行赋值——同样，任何等于`false`、`0`、`""`、`null`、`undefined`和`NaN`的值以及更多的值。

这一个是与前一个操作符的完全相反的*，因此，也与布尔表达式中 JavaScript 逻辑大量使用的[逻辑或](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)类似。*

通过写这个声明:

```
x ||= y
```

等效的逻辑解释如下:

```
x || (x = y);
```

# 例子

```
const book = { title: 'Birds', author: '' };
​
// book.title is truthy
book.title ||= 'Cats and Dogs';
console.log(book.title);
// expected output: 'Birds'
​
// book.author is falsy
book.author ||= 'Cameron Manavian';
console.log(book.author);
// expected output: 'Cameron Manavian'
​
```

尽情享受吧！