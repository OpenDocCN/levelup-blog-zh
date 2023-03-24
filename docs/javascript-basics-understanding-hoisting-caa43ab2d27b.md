# JavaScript 基础系列:理解提升

> 原文：<https://levelup.gitconnected.com/javascript-basics-understanding-hoisting-caa43ab2d27b>

## 用简单明了的例子解释吊装！

![](img/dbf0912da3b49fcc94681418a60bba90.png)

变量和函数提升就像起重机提升的板条箱。

> JavaScript 基础是一个探索每个前端软件工程师都应该理解的一些核心概念的系列。这些概念不仅对工作面试的成功很重要，对开发人员的职业生涯也很重要。

JavaScript 中最重要和最基本的概念之一是提升的概念。提升不仅让我们深入了解 JavaScript 如何运行，还让我们了解它的引擎如何解释和执行代码。让我们开始吧！

> **提升是一种允许在声明变量之前使用变量的机制！**

提升是在执行之前，将变量和函数移动到它们各自的全局或局部作用域的顶部的过程。这种机制允许在它们作用域内的任何地方声明它们，并且仍然按顺序执行。简单来说，提升意味着*变量和函数可以在你的代码中声明*之前使用。

## 提升变量

```
hoistMe = 4; // <-- Assigned here although declared belowvar sRoot = Math.sqrt(hoistMe)console.log(sRoot)var hoistMe; // <-- Declared after hoistMe is used
```

结果:`2`

从上面可以看到，变量 *hoistMe* 在声明之前就被赋值甚至使用了，这与大多数语言中发现的变量声明顺序有很大不同。这是可能的，因为 JS 引擎*提升了*变量，即在代码中使用之前，通过将变量提升到顶层范围来解释变量。

> **当你试图访问一个未声明的变量时，会出现** `***ReferenceError***` **。**

然而，也有例外:`let`和`const`。ES6 引入了这两个保留字来取消`var`的使用。需要注意的是，提升并不适用于这两个关键字。如果我们尝试用上面的代码来代替，我们会得到下面的错误:

用`let`:

```
ReferenceError: Cannot access 'hoistMe' before initialization
```

带`const`:

```
const hoistMe; // <-- Declared after hoistMe is used
```

当你试图访问一个未声明的变量时，一个`ReferenceError`发生*。于是，下面抛出一个`ReferenceError`:*

```
v = 4let v;
```

`ReferenceError: Cannot access ‘v’ before initialization`

> ***JavaScript 只吊声明不初始化！***

需要指出的另一点是提升只适用于声明，不适用于初始化。例如:

```
var hoistMe = 2; //Initializationconsole.log (hoistMe * hoistMe2);var hoistMe2 = 2;  //**Doesn't get hoisted because of initialization**
```

结果:`NaN`

输出是 NaN，因为 *hoistMe2* 是**未定义的**，然而我们正在一个数(2)和一个未定义的变量之间执行运算(乘法、除法等)。为什么没有定义？*JavaScript 中任何未声明的变量默认被赋予* `undefined`类型。假设 hoistMe2 由于初始化而没有被提升，当您将它注销到控制台时，它没有任何值，因为它还没有被声明。只有当解释器到达最后一行时，它才读取声明和赋值，即初始化语句。

**提升功能**

JavaScript 中的函数也是如此。可以在同一范围内调用它们之前定义它们。

```
let str = "This string"console.log(hoistMe())function hoistMe(){ // <-- Defined AFTER being called return str + " will be hoisted!"}
```

结果:`This string will be hoisted!`

由于提升，虽然函数在定义之前被调用，但是 JavaScript 引擎不会抛出错误，因为函数已经被解释并提升(提升)到顶部范围，并且在执行之前可以使用。

## 恶作剧问题

**以下代码的输出是什么？**

```
var num = 1;function f(){ 
    console.log("The number is: " + num);
    var num = 2;
}
```

你可能认为结果是 2，或者 1，但事实上是`undefined`。原因是，使用 var 时，只有声明会被提升，从而产生一个值尚不存在的变量。上面的代码类似于:

```
var num = 1;function f(){
    var num;
    console.log("The number is: " + num);
    num = 2;
}
```

输出:`The number is: undefined`

根据我们所学，如果我们用`let`而不是`var`会发生什么？

**外卖**

我们可以将上述总结如下:

*   提升是在声明或定义变量或函数之前使用它。
*   提升只适用于声明，不适用于初始化。
*   `Let`和`const`不能吊装(ES6+)。
*   如上所述，使用`let`时会出现参考误差。
*   当**访问**未声明的变量时发生引用错误。
*   在 JavaScript 中，未声明的变量默认被赋予类型`undefined`。

尽管 JavaScript 允许变量提升，但使用`let`和`const`而不是`var`被认为是最佳实践，以避免错误，并养成在使用变量之前声明变量的习惯。同样的礼仪也适用于功能。

编码快乐！:)