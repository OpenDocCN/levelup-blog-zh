# JavaScript 代码闻起来像函数

> 原文：<https://levelup.gitconnected.com/javascript-code-smells-functions-22920481564b>

![](img/6d2cfe7c5294f613957e5e92b7f02bd9.png)

照片由[亚伦·康克林](https://unsplash.com/@conklincreates?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在编程中，代码气味是一段代码的特征，表明可能有更深层次的问题。这是一种主观特征，用于通过观察代码来判断代码是否质量良好。

在本文中，我们将看到 JavaScript 函数的一些代码味道，包括过多的参数、过长的方法、标识符长度、返回过多的数据以及过长的代码行。

# 参数太多

函数可以有太多的参数。如果它们有太多，在读取和调用函数时，会使函数变得更加复杂。这可能意味着我们可以以某种方式清理代码，使其更易于阅读。

例如，以下函数有许多参数:

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

# 过长的标识符

太长的标识符很难阅读。如果它们可以更短而不丢失任何信息，那么就让它们更短。

例如，我们可以缩短下面的变量声明:

```
let colorOfAFruit = 'red';
```

变成:

```
let fruitColor = 'red';
```

通过移除`Of`和`A`使其变短。它不会改变意义或删除任何信息。然而，它更短，所以我们输入更少，并得到相同的结果。

# 标识符过短

标识符太短也是一个问题。这是因为太短的标识符不能捕捉我们定义的实体的所有含义。

例如，以下变量名太短:

```
let x = 'red';
```

在上面的代码中，`x`太短了，因为我们不知道查看变量名是什么意思。

相反，我们应该写:

```
let fruitColor = 'red';
```

所以我们知道变量是水果的颜色。

# *数据返回过多*

返回我们不需要的数据的函数不好。一个函数应该只返回外部代码需要的东西，这样我们就不会暴露多余的不需要的东西。

例如，如果我们有以下函数:

```
const getFruitColor = (fruit) => {
  return {
    ...fruit,
    size: 'big'
  };
}
```

我们有带有`size`属性的`getFruitColor`函数，它与水果的颜色无关。因此，它不需要也不应该随对象一起返回。

相反，我们应该返回一个带有水果颜色的字符串，如下所示:

```
const getFruitColor = (fruit) => fruit.color;
```

上面的代码要干净得多，只返回函数名所暗示的水果颜色。

![](img/a445c8a182327d33dddb8b7c1c73b538.png)

威尔·马洛特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# *过长的代码行*

过长的代码行使得代码库难以阅读、理解和调试。

代码太长以至于不适合在屏幕上显示，可能应该分成多行。

例如，不要写:

```
$("p").html("This is a paragraph").css("color", "red").css("text-align", "center").slideUp(1000).slideDown(1000);
```

我们应该改为写:

```
$("p")
  .html("This is a paragraph")
  .css("color", "red")
  .css("text-align", "center")
  .slideUp(1000)
  .slideDown(1000);
```

这样干净多了，不会溢出屏幕。

每行代码不应该超过 100 个字符，这样就可以不用在任何屏幕上滚动就可以阅读。

在大多数情况下，代码格式化程序可以重新排列代码行，使它们更短。

# 结论

在一个方法中有太多的参数会使传递数据变得困难，因为很容易遗漏一些项目。这也使得方法签名过长。

标识符的长度应该足以识别我们需要的信息。此外，它不应该太短，以至于我们无法从标识符中获得足够的信息。

一个函数应该只返回我们需要的项目，而不是更多。这使得使用该函数变得容易，因为需要处理的数据更少，并且不会暴露我们不想暴露的额外信息。

最后，长代码行应该分成多行，这样更容易阅读和修改。代码格式化程序可以自动将代码分成多行。