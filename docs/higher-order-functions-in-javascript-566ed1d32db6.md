# Javascript 中的高阶函数

> 原文：<https://levelup.gitconnected.com/higher-order-functions-in-javascript-566ed1d32db6>

## 闭包、回调，以及如何使用 Spread 操作符访问高阶函数中数量不确定的参数

![](img/e73167e8e9109c77184a0f8b95ecc784.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

无论您是 Javascript 新手还是相当高级的人，并且只是回顾基本主题，高阶函数都是理解并能够在代码中实现的重要特性。

作为一种编程语言，Javascript 是[多范例](https://codeburst.io/imperative-vs-declarative-javascript-8b5e45a602dd):它支持命令式和声明式编程。命令式编程包括[面向对象编程](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS)(或 OOP)和原型继承，而声明式编程——或函数式编程——建立在第一类函数和闭包以及其他属性之上。这些函数式编程特性用来*声明*一个函数的预期行为是什么，而不特定于任何一个例子。就本文的范围而言，我们不会深入研究这两种范式的细节(更多信息请参见这里的),而是关注 Javascript 函数式编程的一些基本特性，因为它们与高阶函数特别相关。然后，我们将一起构建一个高阶函数的示例。

# 一流的功能

在任何编程语言(不仅仅是 Javascript)中，[一级函数](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function)是一个被视为任何其他变量的函数。这个概念允许 Javascript 中的函数作为另一个函数的参数被接受，由另一个封闭函数返回，甚至作为变量值被赋值。同样，在 Javascript 中，函数被视为*对象*。为了创建高阶函数，这是必要的。

您可以查看每种可能性的代码片段(作为参数的函数、作为返回值的函数或作为变量值的函数)[这里](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function)或[这里](https://medium.com/launch-school/javascript-weekly-an-introduction-to-first-class-functions-9d069e6fb137)。

# 关闭

一个[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)是 Javascript 中的函数如何记住和访问它周围的状态(或词汇环境)。每当一个函数访问一个定义在自己作用域 之外的 ***变量时，就会产生闭包。当考虑闭包以及它们如何操作时，考虑下面的例子可能会有所帮助。让我们一起走过它。***

```
01  const globalVar = "xyz"
02  (function outerFunc(outerArg){
03    const outerVar = 'a'
04    (function innerFunc(innerArg){
05      const innerVar = 'b'
06      console.log(outerArg) // 123
07      console.log(innerArg) // 456
08      console.log(outerVar) // "a"
09      console.log(innerVar) // "b"
10     console.log(globalVar) // "xyz"
11    })(456)
12  })(123)
```
```

在代码片段的全局范围内，有一个全局变量`globalVar`，它是用值`“xyz”`声明和实例化的。`outerFunc`是一个 life——一个[立即调用的函数表达式](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)——接受第 2 行`outerArg`的参数，用第 12 行`123`的实参调用。在`outerFunc`中，声明了一个变量`outerVar`，并用`‘a’`的值实例化。`outerFunc`还包括`innerFunc`。`innerFunc`也是一个生命，接受第 4 行`innerArg`的参数，用第 11 行的参数`456`调用。在`innerFunc`中，它声明并实例化了一个变量`innerVar`，其值为`‘b’`。在`innerFunc`中，它记录了每个变量值。

`innerFunc`在这里充当一个闭包。正如您从日志中看到的(由上面的`//`描述)，`innerFunc`可以访问这个代码片段中每个变量的值。`innerFunc`创建自己的作用域，可以在这个作用域内访问`innerVar`，但是 ***也可以*** 访问其封闭作用域内的变量`outerFunc`，以及任何全局声明的变量`globalVar`。基本上，内部闭包总是可以向外看，但是反过来是*不*真。

回顾了一级函数和闭包之后，高阶函数只是这两个概念的组合。

# 高阶函数

简单来说，一个[高阶函数](https://eloquentjavascript.net/05_higher_order.html)只是一个一级函数，可以接受另一个函数作为自变量 ***和/或*** 返回一个函数。高阶函数的一个常见示例是当函数接受第二个函数(命名的或匿名的)作为参数时。作为参数传入的函数也称为[回调函数](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)。请记住，当回调函数在封闭函数中被调用并需要访问外部变量时，闭包就会被创建。

让我们考虑一个简短的例子:

```
01  function hello() {
02    console.log("hello world")
03  } 04  function start(callback){
05    callback()
06  } 07  start(hello)
// "hello world"
```

在上面的代码片段中，第 1–3 行声明了函数`hello`。在第 4 行的`start`的函数声明中，我们将`callback`作为参数传入。在第 7 行，`start`被调用，函数`hello`作为参数被传入。`start`在被调用时记录`“hello world”`，因为`hello`作为一个回调函数，它本身将在`start`中被调用(见第 2 行)。

# 实现高级高阶函数

我们将一起构建一个高阶函数`logger`，它将接受一个回调函数。该函数应该记录*回调*函数的参数( ***而不是*** `logger`的参数)；评估回调函数的返回值；最后，记录回调函数的返回值。

在下面的代码片段中，我创建了三个函数作为示例性的回调函数用于`logger` : `add`、`square`和`sumArray`。这些函数都基于所提供的参数计算一个数学方程。为了调用高阶函数`logger`，进行了一个变量声明(见下面第 12-14 行)，该值是一个函数表达式(即`logger(add)`)。通过使用我们的高阶函数，函数表达式可以接受一个回调函数。

`logger`满足高阶函数的要求:它接受一个函数作为回调函数，并且**也将**返回一个函数，该函数将评估回调函数。还要记住，`logAdd`、`logSquare`和`logSum`在被调用时都会创建自己的闭包；它们共享对`logger`函数的相同引用，但是会创建不同的词法环境。

## 起始代码:

```
01  function add(x, y){
02    return x + y
03  }

04  function square(x, y){
05    return x * y
06  }

07  function sumArray(arr){
08    return arr.reduce((accumulator, currentValue) => {
09      return accumulator + currentValue
10    })
11  }

12  const logAdd = logger(add)
13  const logSquare = logger(square)
14  const logSum = logger(sumArray)

16  logAdd(1, 2) 
// "these are my arguments:" 1 2
// "this is the result:" 3

17  logSquare(3, 3)
// "these are my arguments:" 3 3
// "this is the result:" 9

18  logSum([1, 2, 3])
// "these are my arguments:" [1, 2, 3]
// "this is the result:" 6
```

在处理这个问题时，让我们首先考虑第 12–14 行的变量声明中发生了什么。我们可以理解第 16 行的`logAdd(1, 2)`会用`logger` : `logger(add(1, 2))`内的`1`和`2`的参数调用`add`。通过将预期的功能放在上下文中，我们可以看到主要的障碍是`logger`中的`add`函数需要访问将传递给`logAdd`的参数。然而，`logger`也需要足够抽象以接受任意数量的参数，这样它也可以成功地评估任何回调。虽然`logAdd`需要两个参数，但是`logSum`需要一个数组。

如果我们再次考虑闭包是如何工作的，我们知道内部函数总是可以向外看它们的封闭范围。

## 访问未知数量的参数

在直接进入`logger`之前，让我们首先尝试弥合差距，构建一个单独的`combined`函数，它可以处理接受未知数量的参数。(稍后我们可以将内部功能抽象成回调。)`combined`的参数将被传递给一个内部函数，该函数需要特定数量的参数。为了简洁起见，代码片段将集中于从上面构建`add`功能；它需要两个数字参数。

在伪代码中:

```
01  function combined(args??){
02    return (math args) => {
03      do some math here
04    } 
05  } 
```

我们先把`add`的功能性作为`combined`的返回值包含进来。

```
01  function combined(/* args? */){
02    return ((x, y) => {
03      return x + y
04    })(/* args? */)
05  } 
```

为了让匿名箭头函数(第 2-4 行)访问`combined`的参数，我们可以在这里使用一个 IIFE。我们在第 4 行传递的参数将与上面代码片段中第 1 行的参数相匹配。

那么，Javascript 中有没有什么运算符可以让我们访问未知数量的参数呢？让我们回顾一下**扩展运算符**。根据 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):

> **“扩展语法**允许在应该有零个或多个参数(用于函数调用)或元素(用于数组文字)的地方扩展可迭代对象，如数组表达式或字符串……”

通过在我们的外部函数的参数*和*中实现 spread 操作符，当我们立即调用匿名函数表达式时，内部函数可以访问这些变量，而外部函数不需要指定预期的数量。

```
01  function combined(...args){
02    return ((x, y) => {
03      return x + y
04    })(...args)
05  }

// without arrow function syntax

06  function combined(...args){
07    return (function adder(x, y){
08      return x + y
09    })(...args)  
10  }
```

## 使用回调进行重构

完成了这些，我们现在可以考虑如何将`combined`函数重构为预期的`logger`函数。

首先，我们不再需要像在上面的`combined`中那样包含`add`的功能。我们传递给`logger`的`callback`将会处理这个问题。其次，请记住，我们可以扩展封闭的外部函数的参数，以便内部函数可以使用 spread 运算符访问零个或多个参数。

```
01  function logger1(callback){
02    return (...args) => {
03      console.log('these are my arguments: ', ...args)
04      const result = callback(...args)
05      console.log('this is the result: ', result)
06    }
07  }

    // without arrow function syntax

08  function logger2(callback){
09    return function(...args){
10      console.log('these are my arguments: ', ...args)
11      const result = callback(...args)
12      console.log('this is the result: ', result)
13    }
14  }

    // without the spread operator or arrow function syntax 
    // (pre-ES6) 

15  function logger3(callback){
16    return function(){
17      console.log('these are my arguments: ', arguments)
18        const result = callback.apply(null, arguments)
19      console.log('this is the result: ', result)
20    }
21  }
```

在上面的代码片段中，`logger1`中的第 2 行和第 9 行传播了`combined`先前显式传递给其内部函数的参数。这里，我们必须记住，`logAdd`将在被调用时为`logger(add)`创建一个闭包，`logAdd`将为`logger`的`callback`传入参数。匿名函数(第 2–6 行)通过将它们作为参数(第 2 行)传递给`callback`来访问`…args`。

## ES6 之前的版本

顺便说一下，上面代码片段中的`logger3`给出了一个 ES6 之前的解决方案。第 17 行和第 18 行访问 Javascript 的`[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)`对象。这是一个类似数组的对象，在函数中只有*和*可以访问，并且包含传递给该函数的参数的**值**。明确地说，您*不能*使用任何内置数组方法(即`map`或`forEach`)，但是您可以在对象上调用`.length`或通过它们的索引号访问任何参数属性:`arguments[0]`。

此外，如果没有 spread 操作符，第 18 行的`apply`方法将调用`callback`函数，就好像它是`arguments`的方法一样(作为`apply`的第二个参数传入)。通过提供`null`作为第一个必需的参数，全局对象被使用，而不是提供`apply`方法一个特定的`this`上下文给`callback`(更多信息[在这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply))。`arguments`都被传递到`callback`函数中，并且可以被成功评估。

如果我们比较`logger1`和`logger3`的日志:

```
01  const log1Add = logger1(add)
02  log1Add(1, 2)
// these are my arguments: 1 2
// this is the result: 3

03  const log3Add = logger3(add)
04  log3Add(1, 2)
// "these are my arguments:" [Arguments] { ‘0’: 1, ‘1’: 2 }
// "this is the result:" 3
```

当调用`logger3`时，第 17 行将记录`these are my arguments:[Arguments] { ‘0’: 1, ‘1’: 2 }`，而不是像`logger1`或`logger2`那样记录`these are my arguments: 1 2`。这是因为我们在`logger3`中专门访问 Javascript `arguments`对象，而不是传递给`logAdd`并在`logger1`或`logger2`中传播的参数。

但是，请注意，如果我们调用`log3Add(1, 2)`，那么`logger3`的第 19 行仍然会记录`this is the result: 3`，就像上面代码片段中的`log1Add(1, 2)`一样。

# 结论

每当您将 Javascript 实现为函数式编程语言时，通常会使用高阶函数。上面的教程回顾了高阶函数建立在什么概念之上(记住:一级函数和闭包)以及如何在自己的程序中实现高阶函数。

祝你好运，并快乐编码！

# 来源

MDN Web Docs: [面向对象的 Javascript 初学者](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS)，[一级函数](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function)，[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)，[回调函数](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)，[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)，[传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)， [arguments object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) ，[Function . prototype . apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

Javascript 说白了:[Javascript 编程范式有哪些？](https://medium.com/javascript-in-plain-english/what-are-javascript-programming-paradigms-3ef0f576dfdb)

代码突发:[命令式 vs 声明式 Javascript](https://codeburst.io/imperative-vs-declarative-javascript-8b5e45a602dd)

推出学校:[一级函数介绍](https://medium.com/launch-school/javascript-weekly-an-introduction-to-first-class-functions-9d069e6fb137)

雄辩的 Javascript: [高阶函数](https://eloquentjavascript.net/05_higher_order.html)

零零碎碎:[理解 Javascript 中的高阶函数](https://blog.bitsrc.io/understanding-higher-order-functions-in-javascript-75461803bad)