# 算法:斐波那契数列

> 原文：<https://levelup.gitconnected.com/algorithms-fibonacci-sequence-2fb1dd3d671f>

![](img/e2775d646f07ba301add7804a351b73c.png)

图片来自[仿植物怪兽佐拉创意](https://www.zoracreative.com/the-fibonacci-sequence/)

什么是斐波那契数列？斐波纳契数列是指一个数字序列，其中每个后续数字是前两个数字的总和，同时保持黄金比例。数学中的黄金比例是指两个数字之间的比例与另一组数字的比例相同。你可能以前在数学课上听说过。牢记在心；我将解释如何创建斐波那契数列作为一种算法。这是它的样子。

```
Fibonacci Sequence:1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 1441 + 1 = 2
2 + 3 = 5
3 + 5 = 8
5 + 8 = 13
8 + 13 = 21
13 + 21 = 34
21 + 34 = 55
34 + 55 = 89
55 + 89 = 144
... and so on to infinity
```

计算下一个数字就像 Fn = Fn-1+Fn-2 一样简单。

现在让我们开始考虑如何创建算法。假设一位采访者要求我们创建一个斐波纳契数列算法，该算法接受一个数字作为参数。该算法的目的是使用该数字(表示索引)并返回所述索引数字的值。

```
function fibonacciSequence(index) {}
```

现在我们有了一个简单的空函数。现在让我们问问自己，我们对斐波纳契数列了解多少。我们知道当前值是用过去的两个值确定的，前两个值是 1。这意味着在这两个值之间添加了 0。现在让我们创建变量来表示这些值，包括一个变量来表示没有设置值的 Fn。

```
function fibonacciSequence(index) {
 let a = 1
 let b = 0
 let currentValue
}
```

太好了！现在让我们进入下一步。我们知道索引值不能小于 0，所以我们需要围绕它设置一个条件。因为我们知道序列的前两个值是 1，所以我们暂时将它设置为 currentValue 等于值为 1 的变量 a。我们现在必须将前面的两个值相加，以获得序列值中的下一个数字。之后，我们必须将 currentValue 重新赋值给 b，然后我们必须递增地改变索引，以便遍历从插入的数字到索引值 0 的每个索引值。那么我们必须返回 b。

```
function fibonacciSequence(index) {
 let a = 1
 let b = 0
 let currentValue
 while (index >= 0) {
  currentValue = a
  a = a + b
  b = currentValue
  index --
 }
 return b
}
```

似乎有点太容易了，对吧？你是一名专业软件工程师，想要向面试官展示你的技能。我们可以做得更好，我们会的。我们将使用 ***递归*** 来创建斐波那契数列算法！如果你需要一个快速的提醒，看看我写的[博客文章](https://medium.com/dev-genius/algorithms-what-is-recursion-ddaac64e2e03)！

根据我们对序列的了解，你可能会尝试创建这样的算法作为解决方案。

```
function fibonacciSequence(index) {
 if (index <= 1) return 1;
 return fibonacciSequence(index - 1) + fibonacciSequence(index - 2);
}
```

尽管这个解决方案确实可行，但是考虑到时间复杂性，它不是最优的。如果你需要时间复杂性的复习课程，请参考我写的这个[帖子](https://medium.com/swlh/big-o-notation-what-is-it-and-why-is-it-important-a520eb33bf56)。该解决方案的时间复杂度为 O (2n ),这是第二慢的时间复杂度。让我们优化这个解决方案。

```
function fibonacciSequence(index, memo) {
 memo = memo || {};
 if (memo[index]) return memo[index];
 if (index <= 1) return 1;
 return memo[index] = fibonacciSequence(index - 1, memo) + fibonacciSequence(index - 2, memo);
}
```

在这个解决方案中，我们使用了一种叫做**记忆**的东西，它指的是通过存储耗时的函数调用的结果来优化计算机程序。为什么这很重要，为什么我们要使用它？在这个解决方案中，我们将索引的值存储在一个 hash 中，这样可以避免额外的计算时间。这个解的时间复杂度为 O(n)。

尝试在每个解决方案中输入值 30 并运行算法。运行时间有很大的不同。运行时间只会随着输入值的增加而显著增加。

**结论**

总而言之，编写代码**不仅仅是编写功能代码**。它是关于**优化**所说的功能代码以获得可能的最佳性能，其中之一是时间复杂度。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)