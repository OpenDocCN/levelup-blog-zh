# 停止使用“var”在 JavaScript 中声明变量

> 原文：<https://levelup.gitconnected.com/stop-using-var-to-declare-variables-in-javascript-6c0caec16f43>

## “const”和“let”的介绍

![](img/0c0fec49fe85b31915bda606fd8874d4.png)

我们都从某处开始。我学习了用`var`关键字声明变量的 JavaScript。这很简单也很有效，但我已经改变了。

如果你写类似于`var x = 5`的代码，你需要停下来。嗯，我就实话实说吧，你**没有**要，但是你**应该**要。

我们通常认为编程语言是一套刻在石头上的规则。现实情况是，编程语言——像任何口语一样——是不断发展的。

我现在使用关键字`const`和`let`在 JavaScript 中声明我的所有变量，你也应该这样做。

让我们学习*如何*和*为什么*。

# 声明真常数

从名字上看，变量意味着一个变化的值。虽然声明和不接触变量并不一定有什么错，但是如果我们试图编写有语义意义的代码，那么我们应该区分变量和常量。

常数与变量相反，是一个不变的声明值。历史上，我们用所有的大写字母来定义常数，就像有毒动物身上的亮色。

不依赖于惯例，`const`关键字的引入给了我们一个明确的选项来声明不变的内容。

要使用`const`关键字，只需用`var`替换它，现在这个值就不能修改了。

```
// the old way
// var sales_tax = 0.06;// the new way
const sales_tax = 0.06;sales_tax = 0.08; // "TypeError: Assignment to constant variable.
```

实现很简单，原理也很简单。使用`const`是显而易见的。但是什么时候合适呢？

税率或单位之间的转换等数字比率很容易获取。另一个你会看到使用`const`的地方是使用[箭头符号](https://medium.com/better-programming/learning-javascript-arrow-functions-36cce13351c2)声明函数。

```
const myFunction = (x,y) => {
  // Do stuff
}myFunction(1,2)
```

现在你的功能不能被覆盖。

# 修复 JavaScript 古怪的范围

JavaScript 缺乏范围清晰度，这常常导致开发和故障排除的失败。以下是 JS 范围奇怪之处的总结:

*   一个变量可以使用`var`两次(重新声明)
*   默认情况下，顶级变量是全局的(全局对象)
*   变量可以在声明之前使用(提升)
*   循环中的变量重复使用相同的引用(闭包)

使用`let`澄清并解决了这些奇怪的事情。让我们仔细看看。

## 重新申报

这个很简单，你不能用`let`重新声明一个变量。

```
var x = 0;
let y = 0;var x = 1;
let y = 1; // "SyntaxError: Identifier 'y' has already been declared
```

## 全局对象

在顶层，用`var`声明的变量作为属性添加到全局对象中，而`let`变量不是。

```
var x = 0;
let y = 0;console.log(window.x); // 0
console.log(window.y); // undefined
```

## 提升

用`let`定义的变量只能在它们的块范围内访问，而`var`变量被提升到函数级。

```
// Using var
console.log(i);  // undefined
for(var i=0; i<4; i++) {
  console.log(i);
}
console.log(i); // 4// Using let
console.log(j); // "ReferenceError: j is not defined
for(let j=0; j<4; j++) {
  console.log(j);
}
console.log(j); // "ReferenceError: j is not defined
```

## 关闭

如果这是一个新概念，那么很难理解，但是闭包是一个引用自由变量的[函数。](https://www.freecodecamp.org/news/lets-learn-javascript-closures-66feb44f6a44/)

当我们有一个带有`var`变量的闭包时，引用被记住了，这在变量改变的循环中会很麻烦。

```
var functions = [];for (var i = 0; i < 3; i++) {
  functions[i] = () => { console.log(i); };
}
for (var j = 0; j < 3; j++) {
  functions[j]();
}// 3 3 3
```

使用`let`，每次都会创建一个新的参考。

```
var functions = [];
for (let i = 0; i < 3; i++) {
  functions[i] = () => { console.log(i); };
}
for (var j = 0; j < 3; j++) {
  functions[j]();
}// 0 1 2
```

最终，即使你不是 100%了解这些原因以及它们为什么以这种方式工作，切换到`let`将会产生更明确的代码，这些代码行为一致，并且更容易排除故障/维护。

# 提醒一句

尽管`let`和`const`应该有效地取代`var`关键词，但生活并不总是如此简单。由于这些关键字是在 ECMAScript 2015 (ES6)中引入的，如果您工作的平台不允许，那么您就不走运了。此外，您还需要确保采取措施来确保您的代码能够跨目标受众的浏览器运行。

*你还发现自己在用* `*var*` *吗？是什么阻止你切换到使用* `*const*` *和* `*let*` *？下面分享一下你的经历吧！*