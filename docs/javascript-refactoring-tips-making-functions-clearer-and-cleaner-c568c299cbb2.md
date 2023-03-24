# JavaScript 重构技巧——让功能更加清晰明了

> 原文：<https://levelup.gitconnected.com/javascript-refactoring-tips-making-functions-clearer-and-cleaner-c568c299cbb2>

![](img/3c33110cdb78e82cdc1c75555e7651a2.png)

罗克珊娜·巴休在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难写出一段干净的 JavaScript 代码。

在这篇文章中，我们将看看如何使我们的功能更加清晰。

# 对对象参数使用析构

如果我们希望函数有很多参数，那么我们应该有一个大的对象参数。

为此，我们应该使用析构语法来析构我们作为变量作为参数传递的对象的属性。

例如，不写:

```
const greet = (obj) => {
  return `${obj.greeting}, ${obj.firstName}${obj.lastName}`;
}
```

我们应该写:

```
const greet = ({
  greeting,
  firstName,
  lastName
}) => {
  return `${greeting}, ${firstName}${lastName}`;
}
```

这样，我们就少了很多重复，也让每个人都清楚，对象在参数中有什么属性。

这就像有多个参数，而实际上不必传入多个参数。

这要灵活得多，因为对象中属性的顺序与多个参数无关，但是我们仍然可以将每个属性作为变量单独访问。

# 命名我们的回调函数

命名事物使得阅读代码更容易。回调函数也是如此。例如，不写:

```
const arr = [1, 2, 3].map(a => a * 2);
```

我们应该改为写:

```
const double = a => a * 2;
const arr = [1, 2, 3].map(double);
```

现在我们知道我们的回调函数实际上是用来加倍原始数组的每个条目的。

# 让我们的条件句具有描述性

通过将条件表达式写在自己函数的条件语句中，可以使条件更具描述性。

例如，不写:

```
if (score === 100 ||
  remainingPlayers === 1 ||
  remainingPlayers === 0) {
  quitGame();
}
```

我们应该写:

```
const winnerExists = () => {
  return score === 100 ||
    remainingPlayers === 1 ||
    remainingPlayers === 0
}if (winnerExists()) {
  quitGame();
}
```

这样，我们知道这些条件是检查游戏代码中是否存在赢家的条件。

在第一个例子中，我们在`if`的括号中有一个长表达式，大多数人可能不知道它正在检查。

但是在第二个例子中，一旦我们把它放在一个命名函数中，我们知道条件实际上是检查。

在我们的条件中拥有一个命名函数比仅仅拥有一堆布尔表达式要清晰得多。

# 用地图或对象替换 Switch 语句

`switch`语句因其长度而冗长且容易出错。因此，如果可能的话，我们应该用更短的代码来代替它们。

许多`switch`语句可以用地图或实物代替。例如，如果我们有下面的 switch 语句:

```
const getValue = (prop) => {
  switch (prop) {
    case 'a': {
      return 1;
    }
    case 'b': {
      return 2;
    }
    case 'c': {
      return 3;
    }
  }
}
const val = getValue('a');
```

我们可以用一个物体或地图来代替它，如下所示:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
}
const val = obj['a'];
```

正如我们所见，`switch`很长。我们需要用多个`return`语句嵌套多个块，以便在给定一个`prop`值的情况下得到一个返回值。

有了对象，我们就有了对象`obj`:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
}
```

它有 3 个带有字符串键的属性，如果我们像上面那样用括号符号访问它，就可以从这些属性返回值。使用括号符号，键不必是有效的标识符。相反，它们可以是任何字符串。

我们也可以用一张地图来代替这个物体，如下所示:

```
const map = new Map([['a', 1], ['b', 2], ['c', 3]])
const val = map.get('a')
```

正如我们所见，有了地图，代码也短了很多。我们用一行代码定义了`Map`,方法是传入一个包含条目的数组，这些条目依次由键和值的数组组成。

然后我们使用`Map`实例的`get`方法从键中获取值。

与对象相比，映射的一个好处是我们可以将数字、布尔值或对象等其他值作为键。而对象只能有字符串或符号作为键。

此外，我们不必担心地图的继承属性。

![](img/e5627ebbf2cb9ce52e4d257072c8ed6d.png)

理查德·伯顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

使用析构语法可以使对象参数更清晰、更简短。这样，可以有选择地将属性作为变量来访问。

通过将条件表达式放在它自己的命名函数中，可以使条件更具描述性。同样，我们应该命名我们的回调函数，以便于阅读代码。

最后，`switch`的陈述应该尽可能用地图和实物来代替。