# JavaScript 调试:函数语法

> 原文：<https://levelup.gitconnected.com/javascript-debugging-function-syntax-364d4d90a4c7>

![](img/ca939b8c28c393e55cb43384449b93f7.png)

今天我们要调试一个程序。我们正试图写一个函数，但我们一直得到一个错误。这是我们的节目:

```
function main [verb, noun]
  return verb + noun
}
```

当我们试图运行这个程序时，我们得到了`SyntaxError: Unexpected token '['`。错误指向第一个左括号。如果我们观察 JavaScript 函数的结构，那些括号应该是圆括号。

```
function main (verb, noun)
  return verb + noun
}
```

如果我们再次运行这个程序，我们会得到另一个语法错误，但是这次是`SyntaxError: Unexpected token 'return'`。

问题是函数体没有被两个花括号完全包围。它缺少一个左大括号。现在，从函数名和括号中的参数看不出函数体是什么。另外，底部有一个随机的花括号。

一旦我们添加了花括号并运行程序。没有错误。

```
function main (verb, noun){
  return verb + noun
}
```

让我们通过调用带有必要参数的函数来测试这个函数。这个函数的目的是输出一个由动词和名词组成的字符串。

```
main("drink", " wine")
// outputs: "drink wine"
```

这就是我们程序调试的结论。我们发现有一个错误，我们一步一步地修复可能的错误，直到程序正常工作。当然，我们可以一次完成所有的修复，但是在调试时，你并不总是知道一次修复所有代码的解决方案。通常，您将一次解决一个问题，直到程序按预期运行。

如果您觉得这篇调试文章很有帮助，并且您想了解一些 JavaScript 算法及其解决方案，请查看我最近的其他文章:

[](/javascript-algorithm-marcs-cakewalk-98ad0c699348) [## JavaScript 算法:Marc 的 Cakewalk

### 对于今天的算法，我们将编写一个名为 marcsCakewalk 的函数，它将接受一个数组，卡路里，作为…

levelup.gitconnected.com](/javascript-algorithm-marcs-cakewalk-98ad0c699348) [](https://codeburst.io/javascript-algorithm-camelcase-4df119b6216e) [## JavaScript 算法:CamelCase

### 对于今天的简短算法，我们将创建一个名为 camelcase 的函数，它将接受一个字符串输入 s。

codeburst.io](https://codeburst.io/javascript-algorithm-camelcase-4df119b6216e) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-viral-advertising-168a872cb557) [## JavaScript 算法:病毒式广告

### 对于今天的算法，我们要写一个叫做 viralAdvertising 的函数，它接受一个整数 n 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-viral-advertising-168a872cb557)