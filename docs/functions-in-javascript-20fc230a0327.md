# JavaScript 中的函数

> 原文：<https://levelup.gitconnected.com/functions-in-javascript-20fc230a0327>

![](img/427f423b2959ac110af95821d924f42f.png)

艾莉·史密斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

函数是一组可重用的代码，可以在程序中的任何地方调用。这消除了反复编写相同代码的需要。它帮助程序员编写模块化代码。函数允许程序员将一个大的程序分成许多小的、易管理的函数。

让我们讨论一下函数的类型以及如何使用:

**函数声明** 函数 A(){ }；

**函数表达式**
var B = Function(){ }；

**带分组运算符的函数表达式**
var C =(Function(){ })；

**命名函数表达式**var D = Function foo(){ }；

**life**
var E =(function(){
return function(){ }
}))；

**函数构造器**
var F = new Function()；

**特例:对象构造器**
var G = new function(){ }；

**ES6 箭头功能**
var H = x =>x * 2；

# 函数声明

函数声明可能是 JavaScript 领域中最熟悉和最古老的方式。这就创建了一个在当前范围内可访问的变量 A。

**提升**
有趣的是，它们被“提升”到了自己作用域的顶端:

```
A();function A() {
  console.log(‘foo’);
};
```

上面的代码执行如下:

```
function A() {
  console.log(‘foo’);
};A();
```

这意味着，您可以在将函数写入代码之前调用它们。这无关紧要，因为整个函数都被提升到了其包含范围的顶部。(这与变量相反，变量只提升声明，而不提升内容)。

**if 语句中没有函数声明(或循环等)**
您不能在表达式中以这种方式定义函数，例如 if 语句，这是常见的，如果我们想为不同的情况定义不同版本的函数，通常是为了解决浏览器的不一致性。嗯，你*在某些实现中可以*，但是代码处理的方式不一致。如果您想使用这种模式，请改用函数表达式。

**函数声明必须有名字**
这个方法不允许你创建匿名函数，这意味着你必须给它一个标识符(在这个例子中我们用了“A”)。

# 函数表达式

函数表达式看起来类似于函数声明，除了函数被赋予一个变量名。虽然函数不是 JavaScript 中的原始值，但这是在这种函数式语言中充分利用它们的方式。

JavaScript 支持将函数作为参数传递给其他函数，将它们作为其他函数的值返回，并将它们赋给变量或存储在数据结构中。

```
var B = function(){};
```

**匿名函数(它们不需要名字)**
函数名在函数表达式中是可选的，我们称这些为匿名。在上面的例子中，我们将变量 B 设置为一个匿名函数。

**变量声明提升**所有变量都会发生这种情况，这意味着我们的函数也会发生这种情况，现在我们把它们赋给变量。

```
var A = function() {...};
var B = function() {...};
var C = function() {...};
```

上述代码将被执行为

```
var A, B, C; // variable declarations are hoistedA = function() {...};
B = function() {...};
C = function() {...};
```

因此，设置和调用此类函数的顺序非常重要:

```
// this works
var B = function() {};
B();// this doesn’t work
B2(); // TypeError (B2 is undefined)
var B2 = function() {};
```

第二个例子给了我们一个错误，因为只有变量 B2 的声明被提升，而不是定义，因此“未定义”的错误。

# **带分组运算符的函数表达式**

这些真的和普通的旧函数表达式没有什么不同，在野外也看不到。让我们看看发生了什么:

```
var B = (function() {});
```

当 JavaScript 引擎遇到这里的左括号时，我们实际上是在说“开始把它和其他东西组合在一起”。我们告诉引擎我们不是在做一个函数声明，而是一个函数表达式。然后将结果赋给一个变量:

```
(function(){});  //resulting function not assignedvar foo = (function(){}); //resulting function assigned to foovar bar = function(){};  //resulting function assigned to bar
```

在这里，我们可以看到 foo 和 bar 实际上是一样的，因为在 foo 中，除了函数本身之外，我们没有将函数组合在一起。

# 命名函数表达式

这里，我们不是将变量赋给匿名函数，而是赋给一个命名函数。

```
var D = function foo() {};
```

**函数名只能在函数** 中访问，我们没有将函数名(foo)暴露给封闭作用域(这里是全局作用域):

```
var D = function foo() {
  console.log(type of foo);
};D();                      // function
console.log(typeof foo);  // undefined
```

**对递归有用** 因为函数名可以在函数本身中访问，所以这对递归函数很有用。

```
var countdown = function a (count) {
  if (count > 0) {
    count--;
    return a(count);  //we can also do this: a(--count)
  }
  console.log(‘End of recursive function’);
}countdown(5);
```

# **立即调用函数表达式(life)**

执行这个函数，它的返回值是另一个函数，并将它赋给变量。这种模式非常强大，并且有很多有用的应用。其中最著名的是模块模式。

```
var E = (function() {
  return function(){
    ...
  }
})();
```

我们已经学习了上面的分组操作符，所以你应该觉得这是等价的。

```
var foo = function() {
  return ‘bar’;
};var output = foo();console.log(output); //bar
```

由于 foo 指向我们的函数表达式，我们知道我们可以简单地避免使用变量“foo ”,并将整个函数作为匿名函数放入(毕竟函数是第一类的！).

# 函数构造函数

这种方法非常古老，不建议使用。您在前面传入无限数量的参数，然后实际的函数体在最后一个参数中显示为一个字符串(因为它是一个字符串，它实际上相当于 eval()，不推荐使用)。

**定义函数** 你可以创建这样一个函数

```
var F = new Function('arg1', 'arg2', 'console.log(arg1 + ", " + arg2)');F('foo', 'bar');  // 'foo, bar'
```

**不需要新的运算符
你可以简单的写 var F = Function()；得到同样的结果。**

**怪癖**

这意味着它们不能访问其封闭范围内的变量，这不是特别有用:

```
function foo() {
  var bar = 'blah';
  var first = new Function('console.log(typeof bar)');

  first();   // undefined

  var second = function(){
    console.log(typeof bar);
  }
  second();  // string
}foo();
```

在函数“first”中，我们使用了函数构造函数，所以它不能访问变量栏。然而，如果我们使用函数“second”，这是一个函数表达式，它实际上可以访问其封闭范围内定义的变量(通过闭包)。

**换句话说，*不要使用函数构造器*。**

# 特例—对象构造器

在这种情况下，我们并没有真正定义一个函数，尽管我们使用了 function 关键字。

```
var G = new function() { ... };
```

***新功能(){ }；*** 创建一个新对象，并调用匿名函数作为其构造函数。如果从函数中返回一个对象，则该对象成为结果对象，否则将从头创建一个新对象，并在该新函数的上下文中执行函数。

以这种形式来看，有点不寻常。让我们用正确的方式来做:

```
var Person = function(){
  console.log(this);  // Person
}
var joe = new Person();
```

所以实际上对于新的操作符，我们给它一个新的“this”上下文，然后用这个新的上下文执行给定的函数。与我们之前讨论的函数定义有很大不同。

# 箭头功能

随着 ES6 的加入，出现了这些所谓的“胖箭头”功能，或者如果你喜欢精简的版本，就简单地称为“箭头功能”。

箭头函数没有带来全新的功能，而是提供了一些语法上的好处，这意味着最终我们开发人员将不得不输入更少的代码来达到同样的结果。

箭头函数在以前有单行函数的情况下特别方便，比如 ES5 JavaScript:

让我们看一个例子:

```
[1, 2, 3].map(function(x) {return x * 2});
// [2, 4, 6]
```

上述情况，现在我们可以写成如下:

```
[1, 2, 3].map(x => x * 2);
// [2, 4, 6]
```

箭头函数的第二个好处是它们保留了上下文，这非常方便。通常，试图在 JavaScript 中保持作用域是一项乏味的任务，通常是通过使用 bind()。但这让我们可以绕过它，少写点。

我们再举一个例子:

```
function multiply(array) {
  this.multiplier = 2;
  return array.map(function(x) {return x * this.multiplier});
};

var fakeContext = {};

multiply.call(fakeContext, [1, 2, 3]);
//[NaN, NaN, NaN]
```

这不起作用是因为乘法函数的 ***this*** 上下文没有保存在 ***map()*** 函数中，迫使我们稍微重写一些东西，通过在***【multiple()***(如 ***var self = this*** )中定义一个变量或者通过使用***【bind()***来保存函数上下文。

相反，使用一个箭头函数，我们知道我们的上下文将被保留，允许我们用一个小的调整来写:

```
function multiply(valuesArray) {
  this.multiplier = 2;
  return valuesArray.map(x => x * this.multiplier);
};

var fakeContext = {};

multiply.call(fakeContext, [1, 2, 3]);
// [2, 4, 6]
```

这篇 [ES6 箭头功能](https://medium.com/@kanth.vallampati/learning-javascript-es6-arrow-functions-a3bf9caaecee)的文章有详细的解释。

# 摘要

函数是脚本的主要组成部分。

*   函数定义被提升了——表达式没有。
*   函数在被调用时被执行。这就是所谓的 ***调用*** 一个函数。
*   值可以通过*传入函数并在函数内使用。该值的名称称为参数。实际值本身称为自变量。*
*   *功能 ***总是*** `return`的一个值。在 JavaScript 中，如果没有指定`return`值，函数将默认返回`undefined`。*
*   *功能有 ***对象*** 。*