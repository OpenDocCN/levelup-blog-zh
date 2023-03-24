# JavaScript 最佳实践—对象、函数和数组

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-objects-functions-and-arrays-44ea9cc67096>

![](img/10bbeef68a4a3ab4426dc2d28e506fe2.png)

照片由[拉-Rel 复活节](https://unsplash.com/@lastnameeaster?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了使代码易于阅读和维护，我们应该遵循一些最佳实践。

在这篇文章中，我们将看看我们应该遵循的一些最佳实践，以使每个人的生活更轻松。

# 对象和类中的 Getter 和 Setter 对

我们的对象中应该有 getter 和 setter 对。

没有 getters，属性就无法读取，所以就不用了。

例如，不写:

```
const obj = {
  set a(value) {
    this.val = value;
  }
};
```

我们写道:

```
const obj = {
  set a(value) {
    this.val = value;
  }, get a() {
    return this.val;
  }
};
```

# 在开始后和结束数组括号前换行

我们可以在左括号和右括号前换行。

对于长数组，使用它们会更清楚

例如，不写:

```
const arr= [1, 2, 3, 4, 5];
```

我们写道:

```
const arr = [
  1, 2, 3, 4, 5
];
```

# 括号内的空格

我们可以在括号内不加空格。

所以我们可以写:

```
const arr = ['foo', 'bar'];
```

而且读起来很清楚。

# 数组方法回调中的 Return 语句

我们总是需要在数组方法中返回一些东西。

方法有`reduce`、`Array.from`、`filter`、`map`、`some`、`every`等。在方法中返回东西。

例如，我们不应该这样写:

```
const indexes = arr.reduce((foo, item, index) => {
  foo[item] = index;
}, {});
```

或者:

```
const foo = Array.from(elements, (element) => {
  if (element.tagName === "DIV") {
    return true;
  }
});
```

相反，我们应该写:

```
const sum = arr.reduce((a, b) => a + b, 0);
```

我们用`reduce`返回值的组合结果。

或者我们可以写:

```
const foo = Array.from(elements, (element) => {
  if (element.tagName === "DIV") {
    return true;
  }
  return false;
});
```

像`forEach`这样的一些方法不需要返回一些东西，因为我们不能对返回值做太多事情。

但是我们可以提前返回到下一个迭代:

```
arr.forEach((item) => {
  if (item < 0) {
    return;
  }
  doSomething(item);
});
```

# 数组元素之间的换行符

我们可以在数组中放置长表达式。

例如，我们可以写:

```
const arr = [
  function foo() {
    doWork();
  },
  function bar() {
    doWork();
  }
];
```

我们有两个很长的函数，所以我们把它们放在自己的行中。

# 箭头函数体中的大括号

箭头函数体中的大括号有助于增加清晰度。

例如，我们可以写:

```
const foo = () => {
  return 0;
};
```

而不是写:

```
const foo = () => 0;
```

我们知道我们肯定会用关键字`return`返回一些东西。

# 箭头函数参数中的括号

括号对于分隔参数很有用。

例如，我们可以写:

```
(a) => {}
```

使读取参数更容易。

# 箭头函数的箭头之前或之后的空格

我们应该在箭头函数的箭头之间放置空格。

例如，不写:

```
(a)=>{}
```

或者:

```
a=> a;
```

我们写道:

```
(a) => {}
```

# 将变量视为块范围

如果我们使用`var`来声明变量，它们应该被用作块范围的变量。

例如，我们不应该这样写:

```
function doIf() {
  if (true) {
    var build = true;
  } console.log(build);
}
```

或者:

```
function doIfElse() {
  if (true) {
    var build = true;
  } else {
    var build = false;
  }
}
```

相反，我们写道:

```
const doIf = () => {
  var build; if (true) {
    build = true;
  } console.log(build);
}
```

或者:

```
const doIfElse = () => {
  var build; if (true) {
    build = true;
  } else {
    build = false;
  }
}
```

我们不应该使用`var`的非块范围特性，比如在函数的块外提升或访问`var`变量。

他们只是欺骗了很多人。

# 打开块之后和关闭块之前块内部的空间

最好在块内留有空格，以便于阅读。

例如，不写:

```
function foo() {return false;}
```

我们写道:

```
function foo() { return false; }
```

在开头和结尾留有空格会更清晰。

![](img/ad04e057508f4446c151f2a04d421ae8.png)

由[里克·梅森](https://unsplash.com/@egnaro?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

单个空格和额外的行有利于可读性。

我们应该按照预期的方式使用数组方法。