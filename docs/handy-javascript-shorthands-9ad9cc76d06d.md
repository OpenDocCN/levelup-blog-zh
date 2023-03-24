# 您可能错过的便捷的 JavaScript 速记

> 原文：<https://levelup.gitconnected.com/handy-javascript-shorthands-9ad9cc76d06d>

![](img/bbada2b057c3602f3f367fac866ac34a.png)

照片由[克拉克·杨](https://unsplash.com/@cbyoung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 的最新版本中，这种语言引入了更多的语法糖。在这篇文章中，我们将看看在新版本和旧版本的 JavaScript 中容易阅读的快捷方式。

在本文中，我们将研究三元运算符，声明多个变量、箭头函数、默认参数值等等。

# 三元运算符

我们可以用三元运算符简洁地写出一个`if...else`语句。

而不是写:

```
const x = 20;
let grade;if (x >= 50) {
  grade = "pass";
} else {
  grade = "fail";
}
```

我们可以写:

```
const x = 20;
let grade = (x >= 50) ? "pass" : "fail";
```

它们都检查`x`是否大于或等于 50，如果是，则指定字符串`'pass'`，否则指定`fail`。

我们也可以用三元运算符编写嵌套的`if`语句如下:

```
const x = 20;
let grade = (x >= 50) ? "pass" : (x >= 25) ? "good fail" : 'bad fail';
```

这与以下内容相同:

```
const x = 20;
let grade;
if (x >= 50) {
  grade = "pass";
} else {
  if (x >= 25) {
    grade = "good fail";
  } else {
    grade = "bad fail";
  }
}
```

# 设置默认值

如果变量为 falsy，我们可以通过编写以下内容来设置默认值:

```
let x;
let y = x || 10;
```

这与以下内容相同:

```
let x;
let y;
if (x === undefined || x === null || x === 0 || x === '' || isNaN(x)) {
  y = 10;
}
```

因为`x || 10`意味着如果`x`为 falsy，也就是说`x`为`undefined`、`null`、0、空字符串或者`NaN`，那么我们给`y`赋值 10，和:

```
if (x === undefined || x === null || x === 0 || x === '' || isNaN(x)) {
  y = 10;
}
```

# 声明多个变量的简写

我们可以通过编写以下代码来声明多个变量:

```
let x = y = z = 5;
```

这和写作是一样的:

```
let x = 5;
let y = 5;
let z = 5;
```

它首先将 5 分配给`z`，然后将`z`的值分配给`y`，最后将`y`的值分配给`x`。

# 如果是真实的速记

检查某事是否真实的简写是 JavaScript，它是除了`undefined`、`null`、0、空字符串或`NaN`之外的任何东西，如下所示:

```
if (x){
 console.log('x is truthy')
}
```

上面的代码检查`x`是否正确，如果正确，我们执行`console.log`。

# For…Of 循环速记

从 ES6 开始，我们可以使用`for...of`循环遍历数组或类似数组的对象中的变量，这包括映射、集合、`arguments`对象、生成器、迭代器和任何使用`[Symbol.iterator]`方法的对象。

我们可以写:

```
let fruits = ['apple', 'orange', 'grape'];
for (let fruit of fruits) {
  console.log(fruit);
}
```

这比使用带索引的常规`for`循环更干净，它也适用于其他可迭代对象。例如，我们可以将它用于发电机:

```
let fruits = function*() {
  yield 'apple';
  yield 'orange';
  yield 'fruits';
}for (let fruit of fruits()) {
  console.log(fruit);
}
```

![](img/0867d924b6517212826322aa0747d23c.png)

罗宾·曼茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# Array.forEach

我们可以使用`Array.forEach`方法循环遍历一个数组，尽管它比循环慢。

要使用它，我们可以编写类似下面的代码:

```
let fruits = ['apple', 'orange', 'grape'];fruits.forEach((fruit, index) => console.log(fruit));
```

# 十进制基数指数

我们可以指定指数，而不是写出带有所有尾随零的整数。

例如，如果我们有:

```
1e0
```

对于 1

```
1e1
```

10 元

```
1e2
```

100 美元

```
1e3
```

对于 1000 以此类推。

# 数字分隔符

最新的浏览器允许我们使用下划线来分隔数字，以便于阅读。例如，我们可以写

```
100_000_000
```

一亿美元。下划线可以放在我们选择的任何地方。

# 对象属性速记

而不是写:

```
const foo = 1,
  bar = 2;
const obj = {
  foo: foo,
  bar: bar
};
```

我们可以写:

```
const foo = 1,
  bar = 2;
const obj = {
  foo,
  bar
};
```

这两段代码完全相同。

# 箭头功能

如果一个箭头函数只有一行，那么我们不需要大括号，我们可以从它返回一个值，而不需要使用`return`关键字。

例如:

```
() => 1
```

与以下内容相同:

```
() => {
  return 1
}
```

如果我们不关心`this`的值，我们可以使用箭头函数，因为箭头函数不会改变函数内部的`this`值。

# 默认参数值

我们可以在 ES6 中指定默认参数值。例如，我们可以写:

```
const add = (a = 1, b = 2) => {
  return a + b
}
```

这与以下内容相同:

```
const add = (a, b) => {
  if (typeof a === 'undefined') {
    a = 1;
  } if (typeof b === 'undefined') {
    b = 1;
  }
  return a + b
}
```

上面的短手大多来自 ES6。这个版本的 JavaScript 提供了许多方便的快捷方式，让我们写代码更容易，阅读也同样容易。

`for...of` loop 非常有用，因为它可以遍历数组和类似数组的对象。没有其他循环可以做到这一点。

数字分隔符更新，仅在最新的浏览器中可用。