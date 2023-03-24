# JavaScript 最佳实践—生成器和静止/展开间距

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-generators-and-rest-spread-spacing-c004c6b0f593>

![](img/65027e8daf01bc3baa4c0194cb9511dc.png)

弗农·雷内尔·森松在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将着眼于防止使用无用的生成器和间隔休息和传播操作。

# 总是添加`yield Statements to G`生成器功能

生成器函数的要点在于，当调用生成器来创建返回这些值的生成器时，它会顺序返回一系列值。

为了返回值，我们必须使用`yield`关键字来返回项目。

如果我们只是在不使用`yield`语句的情况下返回项目，那么我们可以使用一个常规函数来完成。

例如，以下不应该是生成器函数:

```
function* foo() {
  return 1;
}
```

因为要从中获得返回值，我们必须编写以下代码:

```
const {
  value: val
} = foo().next();
```

在上面的代码中，我们调用了`foo`生成器来返回一个生成器，然后我们必须调用`next`来获得返回值。

返回的对象有`value`和`done`属性，前者是返回值 1，后者是`true`，因为它所做的只是返回生成器函数中的值。

正如我们所看到的，当我们可以直接用一个常规函数返回值时，这是一个非常迂回的方法。

我们可以写:

```
const foo = () => 1;
```

创建返回 1 的常规函数。

因此，如果我们要定义一个生成器函数，我们应该确保返回一系列带有`yield`关键字的值。

例如，我们可以编写以下代码来实现这一点:

```
function* foo() {
  yield 1;
  yield 2;
}
```

使用`yield`关键字，我们返回值并暂停生成器。然后当我们再次调用`next`时，我们恢复生成器并返回下一个值。

然后我们可以通过调用`foo`来创建一个生成器，并如下循环遍历这些值:

```
for (const f of foo()) {
  console.log(f);
}
```

# Rest 算子和 Spread 算子之间的间距及其表达式

rest 和 spread 运算符都用`...`表示。虽然它们由相同的符号表示，但它们的用法不同。

rest 运算符用于将被调用的参数中尚未赋值的参数放入数组。

它还用于将没有被析构到变量中的数组条目放入数组中。

如果 rest 操作符被用来析构对象，那么没有被析构的对象条目被存储在 rest 操作符所应用的变量中。

spread 操作符用于创建数组和对象的浅表副本，还可以将多个数组和对象合并在一起。

此外，当 spread 运算符用于函数的参数时，它可以用于将数组扩展到参数中。

它们都在操作数之前。通常，当操作符是 rest 或 spread 操作符时，操作符和操作数之间没有任何空格。

例如，我们通常编写如下内容:

```
const a = {
  foo: 1
};
const b = {
  bar: 2
};
const c = {
  baz: 3
};
const merged = {
  ...a,
  ...b,
  ...c
};
```

从上面的代码中我们可以看到，在 spread 操作符和`a`之间没有任何空格。同样，我们与物体`b`和`c`有相同的间距。

对于数组，我们有相同的间距，因此我们编写如下代码:

```
const a = [1, 2];
const b = [3, 4, 5];
const merged = [...a, ...b];
```

在上面的代码中，我们在 spread 操作符和`a`之间也没有任何空格。`b`也是如此。

如果我们在调用一个带有参数数组的函数时使用 spread 运算符，我们写:

```
const add = (a, b) => a + b;
const sum = add(...[1, 2, 3]);
```

我们也没有在操作符和操作数之间添加任何空格。此外，我们没有任何空格在括号内的任何地方。

但是，如果我们把它和其他参数一起使用，那么我们需要在参数之间留一些间距。例如，我们编写以下内容:

```
const add = (a, b) => a + b;
const sum = add(1, ...[2, 3]);
```

在上面的代码中，我们在逗号之后和 spread 运算符之前有一个空格字符。

同样，对于 rest 操作符，我们编写以下代码。如果我们在一个函数上使用它，那么我们写:

```
const add = (...args) => args.reduce((a, b) => a + b, 0);
```

括号里没有空格。这与我们使用 spread 操作符的前一个例子是一致的。

如果在 rest 参数之前有其他参数，那么我们需要一些间距。例如，我们可以定义一个函数如下:

```
const add = (a, ...args) => a + args.reduce((a, b) => a + b, 0);
```

正如我们所看到的，逗号后面有一个空格。

对于析构，我们编写如下代码:

```
const [a, ...b] = [1, 2, 3];
const {
  c,
  ...rest
} = {
  c: 1,
  d: 2,
  e: 3
}
```

间距与其他示例一致，除了对象析构，为了清楚起见，我们将每个变量放在它们自己的行上。

![](img/3611966edf50c13ea270078a19a4391c.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

JavaScript 生成器函数中应该有`yield`语句来暂停和恢复返回的生成器。否则，我们应该只定义一个正则函数。

rest 和 spread 运算符相对于其他实体的间距是标准的，一些参数或变量之间有一个空格。如果没有其他参数或变量，则不需要空格。

rest 和 spread 运算符和它的操作数之间肯定没有空格。