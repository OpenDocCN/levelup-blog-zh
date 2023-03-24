# JavaScript 闭包和生命是如何工作的

> 原文：<https://levelup.gitconnected.com/how-javascript-closures-and-iifes-work-446320b45a8f>

![](img/c7ce724ef824a148621174b4ec116c7c.png)

在 2021 年的前 100 天里，作为#100DaysOfCode 的一部分，我将重新向自己介绍常见的 JavaScript 概念，并写博客介绍它们！在这篇文章中，我将谈谈闭包和闭包*以及它们在 JavaScript 编程中的重要性。*

# 关闭

在 JavaScript 中，函数被认为是“一级”的，因为像其他变量一样，它们可以被赋值，作为参数传递，甚至可以从其他函数返回！因为 JS 函数被认为是这样的，所以每次创建它们时，它们都具有利用其作用域之外的变量的能力。这个能力被称为*。*

*闭包指的是被封闭的函数对其周围状态的访问，即*词法范围*。例如，在这个简短的代码片段中:*

```
*let A = "Hello, World!"
function greeting() {
  console.log(A) 
}

greeting() // --> Hello, World!*
```

*尽管`A`没有被定义在`greeting()`的范围之外，但是它仍然可以在函数中被访问。让我们看看如果我们在`greeting()`函数的定义下改变`A`的值会发生什么:*

```
*let A = "Hello, World!"
function greeting() {
  console.log(A) 
}

A = "Hello, Other World!"

greeting() // --> Hello, Other World!*
```

*通常，这在其他语言中是不可能的。但是在 JavaScript 中，由于闭包，当执行`greeting()`时，其作用域内的`A`变量被绑定到全局`A`变量的最新版本。于是，“你好，另一个世界！”记录到控制台。*

*![](img/af953bbbb6bbcb5ea3bd5f65609c7b67.png)*

*使用 JS 闭包最经典的例子是在一个函数内部调用另一个函数。让我们看另一个例子:*

```
*function FuncA() {
  let name = "Brandon" 
  function FuncB() {
    console.log(name)
  }
  FuncB() 
}

FuncA() // --> Brandon*
```

*同样，闭包通过允许封闭的内部函数访问其周围环境中的所有内容，使代码具有功能性(双关语)。*

*如果你要*返回* `FuncB`而不是仅仅调用它，那么`name`信息仍然会因为闭包而存在。我们可以用附加变量赋值来执行:*

```
*function FuncA() {
  let name = "Brandon" 
  function FuncB() {
    console.log(name)
  }
  return FuncB
}

let newFunc = FuncA()
newFunc() // --> Brandon*
```

*…或双括号:*

```
*function FuncA() {
  let name = "Brandon" 
  function FuncB() {
    console.log(name)
  }
  return FuncB
}

FuncA()() // --> Brandon*
```

*因为我们进入了更多的括号…*

# *生活*

*IIFE(或“iffy”)代表 ***立即调用函数表达式*** 。当您运行 JavaScript 代码时，函数语句会经历一个“创建”阶段，在这个阶段，它们已经准备好执行，但是还没有运行。这发生在“执行”阶段。但是有时候，我们不希望一个函数一直挂着，等着被执行；我们希望它一创建就能正常运行。输入生活。*

```
*(function(name) {
  console.log("Hello, " + name)
}("Brandon")) // --> Brandon*
```

*在上面的例子中，我们将一个匿名函数语句放在一组额外的括号中。如果我们不这样做，就会抛出一个`SyntaxError`,因为函数语句通常应该有一个名字。生活就是这样。IIFEs 将函数*语句*转换成一个被立即调用的*表达式*。*

# *结论*

*如果你用 JavaScript 编码，你已经在使用 closers 了。即使一个`function`被写入一个文件，它仍然被包含在*全局作用域*中。闭包允许您将全局作用域中定义的数据与操作该数据的内部函数链接起来。*

*有许多库和框架将它们的整个代码基础包装在一个函数中，以避免全局范围(IIFEs)中的值冲突。这确保了任何具有不同值的重复变量在执行时属于它们各自的上下文，防止了冲突。*

*闭包和生命一起保护您的 JavaScript 程序及其数据。*

# *资源*

*[闭包(MDN 文档)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)*

*[iife(MDN 文档)](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)*

*[罩下 JavaScript。7:生活](/javascript-under-the-hood-pt-7-iifes-23b70358db73)*

**原载于*[*https://blog.mydevdiary.net*](https://blog.mydevdiary.net/how-javascript-closures-and-iifes-work-ckkskq7ui0a4liis198zr1wcl)*。**