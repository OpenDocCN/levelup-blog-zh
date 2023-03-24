# 编写更健壮代码的 JavaScript 最佳实践—函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-functions-3303584a6d3a>

![](img/33a4a8743464dd6c6fa3033aa5b578be.png)

由[paweczerwiński](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将探讨编写不太可能出错的函数的最佳方式。

# 尽可能使用箭头功能

JavaScript 箭头函数很棒。它们让我们以更短更简洁的方式编写函数。

同样，我们不必担心函数内部`this`的值。考虑`this`的值总是很痛苦，因为它在不同的位置有不同的值。

此外，我们不必担心`arguments`对象的值，因为它没有绑定到它。

所以，要尽量用。例如，我们可以这样写:

```
const arr = [1, 2, 3];
const arr2 = arr.map(x => x * 2);
```

在上面的代码中， `x => x * 2`是我们的箭头函数。正如我们所看到的，它很短，因为它只有一行，所以我们不必显式地写`return`来返回`x * 2`。单行函数要简洁得多。

我们不必一直写出`function`关键字来声明一个函数。

同样，我们也不能用它调用`bind`、`call`或`apply`，这样我们也不用担心那些方法。这是我们应该尽可能使用箭头函数的另一个原因，因为调用这些方法出错的风险更小。

它们也不能用作构造函数，所以我们不能对它们使用`new`关键字。这很好，因为不想在新的类语法中使用构造函数。

另一件好事是它们没有`prototype`属性，因为它们不能用作构造函数。这是另一个我们不能在代码中犯错误的地方。

此外，没有带箭头函数的函数声明，所以它们不能被提升。这意味着它们只能在定义后被引用。这消除了我们在哪里可以调用或引用箭头函数的许多困惑。

它们可以用作顶级函数，对象的方法，或者嵌套在任何地方。

例如，我们可以写:

```
const obj = {
  foo: () => {
    console.log('foo');
  }
}
```

所以我们可以调用`obj.foo`方法来记录`'foo'`。

我们也可以写:

```
const bar = () => {
  const foo = () => {
    console.log('foo');
  }
}
```

将`foo`窝在`bar`里面。

因此，除了构造函数之外，它们还适合用在其他地方。我们其实并不想使用构造函数，因为类语法会主动检查代码中的错误，而不是让错误随着构造函数一起消失。

当我们从另一个构造函数继承时，这一点甚至更加重要。

# 使用 Rest 操作符获取长参数列表

如果我们有一个很长的参数列表传递给我们的函数，rest 操作符应该总是用来代替`arguments`对象。

在函数签名中用`...`表示，它返回额外的参数，这些参数没有被设置为数组形式的指定参数的值。

它们使用箭头函数和传统函数。例如，我们可以使用 rest 运算符，如下所示:

```
function f(a, b, ...args) {
  console.log(args);
}
```

当我们调用`f(1, 2, 3, 4, 5);`时，那么`args`就是`[3, 4, 5]`，因为 1 被设置为`a`而 2 被设置为`b`。

我们也可以将它们与箭头函数一起使用:

```
const f = (a, b, ...args) => {
  console.log(args);
}
```

然后我们得到和以前一样的结果。

这样，我们就不必使用`arguments`对象来获取参数，或者使用`call`或`apply`来调用带有额外参数的函数。

像任何数组一样，我们可以对它使用析构语法。例如，我们可以写:

```
const f = (a, b, ...[d, e]) => {
  console.log(d, e);
}
```

那么当我们这样称呼它的时候:

```
f(1, 2, 3, 4, 5);
```

我们得到`d`是 3，`e`是 4。注意，我们不必析构所有传入的参数。

这比`arguments`对象好得多，因为它是一个数组而不是类似数组的对象，我们可以对它使用析构语法。

![](img/c487c013779b9f51986d877265631d01.png)

[Dro Troll](https://unsplash.com/@ango?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

箭头函数应该在我们的 JavaScript 代码中使用，除非我们需要创建构造函数或者在对象方法中引用`this`。

rest 操作符是获取没有赋给命名参数的函数参数的最佳方式。