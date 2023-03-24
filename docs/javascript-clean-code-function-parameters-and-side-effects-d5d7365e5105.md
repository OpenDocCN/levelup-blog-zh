# JavaScript 干净代码—函数参数和副作用

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-function-parameters-and-side-effects-d5d7365e5105>

![](img/d5e46d3bf5f7efe6343304ea3592071d.png)

[疾控中心](https://unsplash.com/@cdc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

函数是 JavaScript 程序的重要组成部分。它们用于将代码分成可重用的块。因此，为了有清晰的 JavaScript 代码，我们需要有易于理解的函数。

在本文中，我们将看到好函数的更多属性，包括标志参数、二元和三元函数以及副作用。

# 标志参数

布尔参数应该尽量少用。它使得函数签名更加复杂，并且它告诉我们函数不止做一件事(有多条路径)。

# 二元函数

二元函数比参数较少的函数更难理解。然而，有时它们是有意义的。例如，如果我们有一个包含笛卡尔坐标的对象，那么它应该有两个参数。

例如，我们可以有一个带有两个参数的构造函数的类，如下所示:

```
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}const point = new Point(1, 2);
```

几乎不可能用其他方式来定义它。

然而，我们必须意识到，与参数较少的函数相比，它需要更多的时间和脑力。

# 三元函数

有三个参数的函数需要花费大量的时间和脑力来理解有两个参数的函数。

如果有两个或更少的论点，就要考虑更多的论点组合。

# 将参数组合成对象

如果一个函数有许多参数，我们应该考虑将它们组合成对象。

如果他们有血缘关系，那就更是如此。例如，以下函数有许多参数:

```
const describeFruit = (color, name, size, price, numSeeds, type) => {
  return `${fruitName} is ${fruitColor}. It's ${fruitSize}. It costs ${price}. It has ${numSeeds}. The type if ${type}`;
}
```

6 个参数可能太多了。我们可以通过传入一个对象来解决这个问题:

```
const describeFruit = (fruit) => {
  return `${fruit.name} is ${fruit.color}. It's ${fruit.size}. It costs ${fruit.price}. It has ${fruit.numSeeds}. The type if ${fruit.type}`;
}
```

如我们所见，它干净多了。我们不必担心传入许多参数。

它也更适合屏幕，因为它更短。

5 个参数可能是函数中应该有的最大值。

# 动词和关键词

在我们的函数名中包含动词和关键字是一个好主意，因为它们做一些事情，这意味着名称中的动作词是合理的。

此外，我们需要知道我们要对哪些事物应用动作。这意味着我们必须添加一些关键字来做到这一点。

例如，符合这条规则的好的函数定义应该是这样的:

```
const copyArray = (array) => [...array];
```

名称让我们知道我们的函数复制了一个数组。

它也让我们知道我们传递给函数的是什么，这显然是一个数组。

![](img/1708b8b9563f88673bba867b9948aee0.png)

[Autri Taheri](https://unsplash.com/@ataheri?utm_source=medium&utm_medium=referral) 在[Unplash](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄的照片

# 无副作用

副作用是函数中的代码，它对函数之外的东西进行更改。

这不是很好，因为它对函数之外的东西进行了隐藏的更改。

我们应该尽可能地避免这种情况，因为它会做一些意想不到的事情，而且会让测试变得更加困难，因为除了接受参数、做事情和返回结果之外，它还会对我们必须考虑的函数之外的东西进行更改。

这意味着我们必须在函数返回的内容之外进行测试。

例如，如果我们有:

```
let numFruits = 1;
const addFruit = () => {
  numFruits++;
}const removeFruit = () => {
  numFruits--;
}
```

然后我们有两个有副作用的函数，因为它们都改变了每个函数之外的`numFruits`变量。

写这些函数的更好方法是把它们写成纯函数。纯函数是在传入相同参数的情况下返回相同结果的函数。而且，它没有副作用。

正因为如此，纯函数更容易测试，它们的行为也是可以预测的。

我们可以通过编写如下代码来重写上面的代码:

```
let numFruits = 1;
const addFruit = (numberOfFruits) => numberOfFruits + 1;
const removeFruit = (numberOfFruits) => numberOfFruits - 1;numFruits = addFruit(numFruits);
numFruits = removeFruit(numFruits);
```

我们现在有两个函数，分别接受一个`numFruits`参数并返回一个更大或更小的数字。

然后，我们可以使用它们来改变函数之外的`numFruits`变量。

正如我们所看到的，它们对`numFruits`没有任何作用，而是分别返回`numberOfFruits`参数加 1 或减 1。

如果我们为它们编写测试，那么我们可以通过传入输入并检查输出是否是我们想要的来轻松测试它们。这比将副作用提交给测试代码可能使用的变量要好得多。

# 结论

标志参数应该最小化。他们告诉我们，函数可以做不止一件事，它是函数签名中的另一个参数。

接受较少参数的函数比接受较多参数的函数更好。如果需要很多参数，考虑将它们组合成一个对象。

最后，如果可能的话，应该避免副作用。有副作用的函数会做一些隐藏的事情，并且很难测试。纯函数更具可测试性和可预测性，因为它们不会产生副作用。