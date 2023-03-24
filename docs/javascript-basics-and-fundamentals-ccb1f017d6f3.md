# Javascript 基础和基本原理

> 原文：<https://levelup.gitconnected.com/javascript-basics-and-fundamentals-ccb1f017d6f3>

![](img/8c586319fea009b23e91c2c7e431d491.png)

卡斯帕·卡米尔·鲁宾的照片

将讨论以下 Javascript 基础知识:

*   函数声明/表达式
*   ES6 箭头函数语法
*   `let` / `const` / `var`的区别
*   函数中的作用域链
*   迭代器方法
*   通过值传递，通过引用传递(尽可能简单)

# 函数声明/表达式

一个基本的 JavaScript 方法利用关键字`function`，后跟代表代码块的名称、括号和花括号。这被称为**函数声明**。

《出埃及记》

```
function returnThree(){ return 3;}
```

与另一种编程语言 ruby 不同，当使用`function`关键字时，return 关键字必须被用来显式地从代码块中返回一些东西。函数也可以在没有名字的情况下声明。这就是所谓的**匿名函数**。匿名函数可以作为**回调函数**使用。

《出埃及记》

```
function() { return 3;}
```

若要单独调用匿名函数，请将该函数括在括号中，并在其后添加另一组括号。这就变成了一个立即调用函数表达式(IIFE)。

```
(function(){ return 3;})()
```

匿名函数可以存储在变量中。这就是所谓的**函数表达式**。

```
let returnThree = function(){ return 3;}
```

使用函数表达式有一个致命的缺点。如果函数表达式在其代码块之前被调用，它将引发引用错误。

《出埃及记》

```
//Function ExpressionreturnThree() // undefined var returnThree = function(){ return 3;}
```

然而，JavaScript 将函数声明提升到代码的顶部，这意味着函数可以在其代码块之前被调用。

《出埃及记》

```
//Function DeclarationreturnThree() // 3 function returnThree(){ return 3;}
```

有关在 JavaScript 中提升的更多信息，请参考:

[](https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-what-is-hoisting-in-javascript-a63c1b2267a1) [## JavaScript 中的提升是什么？

### 了解 JavaScript 中的提升意味着什么，并提供代码示例来帮助解释这一切。

medium.com](https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-what-is-hoisting-in-javascript-a63c1b2267a1) 

# ES6 箭头函数语法

ES5 函数语法:

```
function returnThree(){ return 3;}
```

ES6 函数语法(只能做函数表达式):

```
const returnThree = () => { return 3;}
```

它实际上可以缩短，不需要花括号来表示隐式返回:

```
const returnThree = () => 3;
```

恰好接受一个参数的 arrow 函数不需要括号:

```
const addThree = param => param + 3addThree(3) // 6
```

除了更短的语法，箭头函数还有另一个与`*this*` *概念相关的好处。*有关更多信息，请参考:

[](https://codeburst.io/javascript-arrow-functions-for-beginners-926947fc0cdc) [## JavaScript:面向初学者的箭头函数

### 上周，我发表了这篇文章，主题是为初学者准备的。其中没有涉及的一个主题是…

codeburst.io](https://codeburst.io/javascript-arrow-functions-for-beginners-926947fc0cdc) 

# let/const/var 之间的差异

使用`const`，变量不能被重新分配。但是，如果该变量是一个对象，它的属性仍然可以更改。这是因为对象引用本身不会改变。

《出埃及记》

```
const obj = {greet: "Hey"}
obj         //{greet: "Hey"}const obj = {} //Uncaught SyntaxError: Identifier 'obj' has already  
               //been declaredobj.greet = "Hello"
obj         // {greet: "Hello"}
```

`let` 和`var` 的相似之处在于它们可以被重新分配。然而，用`var`，作用域是函数而不是括号。

《出埃及记》

```
const arr = [1,2,3,4]for(var i = 0; i<arr.length; i++){ console.log(arr[i]) //1,2,3,4}i // 6
```

使用`var`，变量`i`在`for`循环代码块之外可用。`let`工作方式稍有不同。

```
const arr = [1,2,3,4]for(var i = 0; i<arr.length; i++){ console.log(arr[i]) //1,2,3,4}i //Uncaught ReferenceError: i is not defined
```

乍一看，这似乎是堕落，但这实际上是好的。变量`i`不应该出现在代码块之外。例如，同一个变量可以用于另一个 For 循环代码块，而不会发生冲突。有关`let` / `const` / `var`的更多信息，请参考:

[](https://medium.com/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75) [## JavaScript ES6+: var，let，还是 const？

### 也许要成为一名更好的程序员，你能学到的最重要的事情就是保持事情简单。在…的背景下

medium.com](https://medium.com/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75) 

# 范围链

与 Ruby 类似，Javascript 中的函数可以访问一个*作用域链*。函数将试图在当前范围内寻找变量值。如果不能，它将超出它的作用域，直到找到它所访问的变量，如果变量不存在，它将抛出一个引用错误。

《出埃及记》

```
let greeting = "Hello"function sayGreeting(){ console.log(greeting)}sayGreeting()
```

sayGreeting 函数中没有定义 greeting 变量，但它超出了确定值的范围(如果定义了的话)。

《出埃及记》

```
let greeting = "Hello"function sayGreeting(){ return function(){ console.log(greeting) //logs out "Hello" return function(){ let greeting = "Hey" console.log(greeting) //logs out Hey } }}sayGreeting()()()
```

具有嵌套函数的函数会产生不同的输出。函数有不同的作用域链。最里面的函数可以访问其作用域中值为“Hey”的 greeting 变量。outer 函数不这样做，它超出了查找 greeting 变量及其值的范围。有关 Javascript 中作用域和作用域链的更多信息，请参考:

[](https://blog.bitsrc.io/understanding-scope-and-scope-chain-in-javascript-f6637978cf53) [## 理解 JavaScript 中的范围和范围链

### 了解 JavaScript 引擎如何执行变量查找

blog.bitsrc.io](https://blog.bitsrc.io/understanding-scope-and-scope-chain-in-javascript-f6637978cf53) 

# 迭代器方法

在 Ruby 中，当处理数组时，有四个重要的可枚举方法:。每个，。地图，。下面是 Javascript 中每个迭代方法及其语法的快速概述。

## 。each => forEach()

它遍历数组中的元素。根据回调函数，它可以改变原始数组。

```
let array = [1,2,3,4]array.forEach((el) => { console.log(array)}//1
//2
//3
//4
```

## 。map => map()

它转换数组的元素，但不改变原始数组。

```
let array = [1,2,3,4]let newArray = array.map(el => el * 3)//array [1,2,3,4]//newArray [3,6,9,12]
```

## 。选择= >过滤器()

它选择数组中满足特定条件的元素，但用所选元素创建一个新数组。原阵没有变异。

```
let array = [1,2,3,4]let newArray = array.filter(el => el >2)//array [1,2,3,4]//newArray [3,4]
```

## 。find => find()

与 select 类似，它遍历数组并返回满足所提供条件的第一个元素。

```
let array = [1,2,3,4]let found= array.find(el => el === 3)//array [1,2,3,4]//found 3
```

还有许多其他的 Javascript 方法。有关数组方法的更多信息，请参考:

[](https://medium.com/@timhancodes/javascript-array-methods-cheatsheet-633f761ac250) [## JavaScript:数组方法 Cheatsheet

### 让我们来看看 JavaScript 中一些常见的数组方法。

medium.com](https://medium.com/@timhancodes/javascript-array-methods-cheatsheet-633f761ac250) 

## 通过值传递，通过引用传递(非常基础)

## 按值传递 Ex。

```
let x = 12
let y = xx = x * 2console.log('x', x) // x 24
console.log('y', y)// y 12
```

虽然变量 Y 应该等于 X，但是当 X 被操作时，Y 的值不会改变。第二行中的 y 值已经设置为第一行中的 X 值。改变 X 的值不会改变 y 的值，但是，当一个变量赋给一个对象或数组时，情况就不一样了。

## 通过引用传递。

```
let a = {x: 12}
let b = aa.x = 14console.log('a', a) // a {x:14}
console.log('b', b) // b {x:14}
```

采用了完全相同的方法，但是对于一个对象，结果是不同的。即使对变量 A 进行了更改，变量 A 和 B 的输出也是一样的。为什么？

对于对象，它们在计算机中拥有特定的内存空间。在第 1 行中，当对象被赋值时，变量 A 指向计算机中的一个特定空间。在第 2 行，变量 B 指向同一个空间。对该空间所做的任何更改，两个变量都会显示出来，因为它们指向同一个空间。

## 奖金

如果目标是将变量 B 指向一个与变量 A 具有相同内容的对象，但看不到变化，则必须创建该对象的克隆。要将对象克隆到变量 B，可以使用 ES6 的扩展运算符。

《出埃及记》

```
let a = {x: 12}
let b = {...a}a.x = 14console.log('a', a) // a {x:14}
console.log('b', b) // b {x:12}
```

在第 2 行中，创建了一个新的对象，但是它的内容与当前使用的 spread 操作符相同。变量 A 和 B 现在指向两个不同的对象。对变量 A 的任何更改只能由 A 访问。有关 PBV 和 PBR 的更多信息和示例，请参考:

[](https://medium.com/nodesimplified/javascript-pass-by-value-and-pass-by-reference-in-javascript-fcf10305aa9c) [## [Javascript]Javascript 中的按值传递和按引用传递

### 在本文中，我们将研究 Javascript 中的按值传递和按引用传递。

medium.com](https://medium.com/nodesimplified/javascript-pass-by-value-and-pass-by-reference-in-javascript-fcf10305aa9c) 

本文介绍了 Javascript 的一些基础知识，同时参考了更深入的文章。感谢您的阅读！

[](https://codeburst.io/javascript-functions-understanding-the-basics-207dbf42ed99) [## JavaScript 函数—了解基础知识

### 探索 JavaScript 中的函数——声明、表达式、调用等等。

codeburst.io](https://codeburst.io/javascript-functions-understanding-the-basics-207dbf42ed99) [](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### JavaScript 是世界上最流行的编程语言之一——它随处可见。JavaScript 是一种…

gitconnected.com](https://gitconnected.com/learn/javascript)