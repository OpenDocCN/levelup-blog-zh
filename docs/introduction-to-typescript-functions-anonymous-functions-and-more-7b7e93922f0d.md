# TypeScript 函数简介:匿名函数等等

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-functions-anonymous-functions-and-more-7b7e93922f0d>

![](img/948f0d8c70a8c639c43579f5ba6aad75.png)

Benoit Gauzere 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

函数是一些小的代码块，接受一些输入，可能返回一些输出或者有副作用。副作用意味着它修改了函数之外的一些变量。

我们需要函数将代码组织成可重用的小块。

如果没有函数，如果我们想重新运行一段代码，我们必须在不同的地方复制它。函数对于任何类型脚本程序都是至关重要的。

在本文中，我们继续研究 TypeScript 函数的不同部分，包括传入可变数量的参数、递归、函数嵌套以及在对象中定义函数。

# 调用参数多于参数的函数

在 TypeScript 中，我们可以调用实参多于形参的函数。如果我们只是传递它们，而不从`argument`对象访问它们，它们将被忽略。您可以用`argument`对象获得并使用参数中没有的额外参数。argument 对象有带数字键的参数，就像数组的索引一样。访问额外参数的另一种方式是通过 rest 参数。

例如，如果我们调用带有额外参数的`add`函数:

```
function add(a: number, b: number, ...rest: any){
  console.log(arguments);
  return a + b;
}
add(1, 2, 3);
```

签名的`...rest`部分捕获了我们不希望传入的参数。我们使用 rest 运算符，它由单词`rest`前的 3 个句点表示，表示在`b`后的末尾可能会有更多的参数。我们在 TypeScript 中需要这样做，这样我们就不会得到传入的参数数量和实参数量之间的不匹配。在普通的 JavaScript 中，`...rest`是可选的。

在`console.log`调用中，我们应该得到:

```
0: 1
1: 2
2: 3
```

# 函数中的可变范围

除非是全局变量，否则函数内部的函数不应该在函数外部被访问。我们应该尽可能避免定义全局变量，以防止错误和难以追踪的错误，因为它们可以在程序的任何地方被访问。为了防止定义全局变量，我们应该用`let`定义变量，用`const`定义常量。例如，我们应该这样定义函数:

```
function add(a: number, b: number){
  let sum = a + b;
  return sum;
}
```

在这种情况下，我们有`sum`，它只能在函数中访问，因为它是用`let`关键字定义的。

# 匿名函数

匿名是没有名字的函数。因为它们没有名字，所以在任何地方都不能被引用。它们通常作为回调函数传递给其他函数，当函数被传递给参数时会调用回调函数。但是，您可以将匿名函数赋给一个变量，这样它就成为一个命名函数。

它们也可以自动执行。这意味着你可以定义函数并让它立即运行。例如，如果我们写:

```
const sum = (function(a: number, b: number){
  return a + b;
})(1, 2);console.log(sum) // log 3
```

我们之所以记录 3，是因为我们定义了一个函数来将 2 个数字相加，然后将 1 和 2 作为参数直接传入，方法是将函数放在括号中，然后将参数传递给它。

# 递归

您可以在 TypeScript 中从函数内部调用同一个函数。这叫做递归。所有的递归函数都必须有一个终止条件，这个终止条件被称为基例，这样它就知道什么时候停止执行。否则，你会得到一个被无限次调用的函数，这会使浏览器崩溃。

要编写递归函数，我们可以写:

```
function sumOfSquares(num: number): number {
  let sum: number = Math.pow(num, 2);
  if (num == 1) {
    return 1
  } else {
    return sum + sumOfSquares(num - 1)
  }  
}
```

在这个例子中，我们编写了一个函数来计算给定数字的平方和。我们计算`num`的平方，然后如果`num`等于 1，那么我们返回 1。否则，我们返回`sum`加上在`num — 1`上调用`sumOfSquares`的结果之和。我们不断减少`num`，这样我们就可以达到我们的基本情况 1，在这样做的同时将结果相加。

![](img/bdf42b82027db4d661693379ca836f8a.png)

[Roi Dimor](https://unsplash.com/@roi_dimor?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 嵌套函数

函数可以相互嵌套。这意味着我们可以在一个函数中定义另一个函数。例如，我们可以写:

```
function convertToChicken(name: string){
  function getChickenName(name: string){
    return `Chicken ${name}`;
  }
  return getChickenName(name)
}
```

在本例中，我们在`convertToChicken`调用中调用了`getChickeName`。所以如果我们写`convertToChicken('chicken')`，那么我们得到`'Chicken chicken'`，因为我们调用了 get `getChickeName`并返回了结果。变量的作用域就是名字。`let`和`const`是块范围的，所以它们不能在定义的原始函数之外被访问，但是它们在嵌套函数中是可用的，所以如果我们有:

```
function convertToChicken(name: string) {
  let originalName = name;  function getChickenName(newName: string) {
    console.log(originalName)
    return `Chicken ${newName}`;
  }
  return getChickenName(name)
}
```

那么`originalName`仍将在`console.log`中定义。

# 在对象中定义函数

我们可以用几种方法在对象中定义一个函数。我们可以像往常一样使用`function`关键字或 arrow 函数，但是我们也可以用`function`关键字的简写来写。例如，如果我们有一个`bird`对象，我们想定义`chirp`函数，我们可以写:

```
const bird = {
 chirp: function(){
   console.log('chirp', this)
  }
}
```

或者使用下面的简写:

```
const bird = {
 chirp(){
   console.log('chirp', this)
  }
}
```

这两个是相同的，因为`chirp`函数将把`bird`对象作为`this`的值。

另一方面，如果使用箭头函数:

```
const bird = {
 chirp: () => {
   console.log('chirp', this)
  }
}
```

我们将从 TypeScript 编译器得到一个错误，因为`this`的值是`globalThis`的值，这是 Typescript 编译器不允许的。我们得到错误“包含的箭头函数捕获了‘this’的全局值。(7041)“当我们试图编译上面的代码时。

TypeScript 函数允许我们将代码组织成可以重用的小部分。有很多方法来定义一个函数，但是坚持通常推荐的方法，比如使用箭头函数，不要过多地使用`arguments`。