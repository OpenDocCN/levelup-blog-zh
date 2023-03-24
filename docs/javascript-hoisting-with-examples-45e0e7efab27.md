# JavaScript 提升示例

> 原文：<https://levelup.gitconnected.com/javascript-hoisting-with-examples-45e0e7efab27>

![](img/048afdb3fe614b5068e5bae9d8d35d32.png)

Artem Sapegin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将讨论 JavaScript 中的提升。

**提升**是在创建执行上下文时发生的一个过程，它将变量和函数的声明移动到上下文的顶部。让我们从一个例子开始:

```
hello();function hello(){
    return "Hello World";
}
```

如果您熟悉 JavaScript，您知道这不会抛出错误。在创建全局执行上下文的过程中，当引擎看到“function”关键字时，它会将变量声明(在本例中为函数“hello”)移到上下文中。这也意味着，在执行代码之前，它看起来会像这样:

```
function hello(){
    return "Hello World";
}
hello();
```

它将该声明上移。因此，在代码执行之前，会分配内存来存储这个函数声明。不是函数声明的变量呢？

```
console.log(text)var text = "Hello World!"
```

假设我有这样一段代码，它试图在变量“text”被声明和定义之前打印出来。它会打印出什么？

```
undefined
```

未定义。引擎看到“var”会知道给这个变量留一块内存，但不知道这个变量已经赋了什么值。因此，当变量的声明被提升时，默认情况下会将“undefined”赋给该变量。这就是为什么没有抛出错误，而是打印出“未定义”的原因。

您还看到了定义函数的这种方式:

```
hello();var hello = function(){
    return "Hello World";
}
```

这是一个函数表达式。如果我们这样做呢？我们能正确地打印出“Hello World”吗？

```
VM592:1 Uncaught TypeError: hello is not a function
    at <anonymous>:1:1
```

嗯……好的，所以引擎不知道这是一个函数。记得吗？当引擎看到“var”时，它会将“hello”的声明上移，并给出一个未定义的默认值。如果你试图调用它，它会告诉你这不是一个函数。让我们先打印出 hello 是什么，然后再给它分配这个功能。

```
console.log(hello);var hello = function(){
    return "Hello World";
}
```

```
undefined
```

刚才我们提到，当执行上下文被创建时，变量声明被提升。让我们试试这个:

```
function hello(){
    console.log("1: " + text)
    var text = "Hello World"
    console.log("2: " + text)
}hello()
```

我们在这里能期待什么？

```
1: undefined
2: Hello World
```

好的，这与我们刚才讨论的一致。让我们在全局范围内给“文本”赋值。

```
var text = "Am I printed?"
function hello(){
    console.log("1: " + text)
    var text = "Hello World"
    console.log("2: " + text)
}hello()
```

同样，你希望它打印出什么？

```
1: undefined
2: Hello World
```

尽管如此，1:仍未定义。当调用 hello()时，会创建一个新的执行上下文，并将“text”变量提升到 hello()上下文的顶部，因此，它不知道来自全局执行上下文的赋值。

让我们再尝试一件事，我们删除了 hello()中的变量声明:

```
var text = "Am I printed?"
function hello(){
    console.log("1: " + text)
}hello()
```

```
1: Am I printed?
```

是的，你被打印了。范围链，对吗？( [JavaScript 作用域](/how-does-javascript-scoping-work-f0f6b79ae896))

你觉得这些在我们写 JavaScript 代码的时候会不会造成混乱？

为了使我们的代码更加可预测，我们可以在 ES6 中使用“let”和“const”。他们不会被吊起来。

```
hello();const hello = function(){
    return "Hello World";
}
```

```
Uncaught ReferenceError: hello is not defined
    at <anonymous>:1:1
```

是的，这感觉好多了。刚开始学习 JavaScript 时，可能会出现一些意想不到的行为(比如类型强制)。然而，理解这些概念肯定会让你成为更好的 JavaScript 开发人员。

既然我们提到了“let”和“const”，我还想再补充一个关于块作用域和函数作用域的。当我们使用其他语言时，这是我们预期会抛出错误的东西(这里我们使用 groovy 作为例子):

```
if(someCondition)
{
    def someVar = "Can you see me? "
}
def newVar = someVar + "I cannot see you."
```

这是因为变量是在“块范围”内定义的。如果我们用 JavaScript 来做呢？

```
var someCondition = trueif(someCondition)
{
    var hello = "Hello "
}var helloWorld = hello + "World"console.log(helloWorld)
```

```
Hello World
```

对于 JavaScript，在这种情况下不会抛出错误。这是因为“var”仅由函数和模块限定范围。如果你在函数中这样做，它会抛出一个错误。

```
function someFunction()
{
    var hello = "Hello "
}var helloWorld = hello + "World"console.log(helloWorld)
```

```
VM1525:6 Uncaught ReferenceError: hello is not defined
    at <anonymous>:6:18
```

如果我们也想阻止{}，该怎么办？

**用“let”和“const”。**

```
let someCondition = trueif(someCondition)
{
    let hello = "Hello "
}let helloWorld = hello + "World"console.log(helloWorld)
```

```
VM1566:8 Uncaught ReferenceError: hello is not defined
    at <anonymous>:8:18
```

这就是这篇文章，希望你喜欢它！

如果你想看更多的网络开发或软件工程相关的内容，请关注我。干杯！

# 分级编码

感谢您成为我们社区的一员！更多内容请参见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在转型的理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)