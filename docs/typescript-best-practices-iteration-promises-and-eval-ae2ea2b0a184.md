# TypeScript 最佳实践—迭代、承诺和评估

> 原文：<https://levelup.gitconnected.com/typescript-best-practices-iteration-promises-and-eval-ae2ea2b0a184>

![](img/12e826434889f561076bcf83a72f97be.png)

照片由[丹金](https://unsplash.com/@danielcgold?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是一个简单易学的 JavaScript 扩展。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的类型脚本代码。

在本文中，我们将研究用 TypeScript 编写代码时要遵循的最佳实践，包括用更好的替代方法替换 for-in 循环。

承诺码应该也有用。

类似 eval 的方法也不应该使用。

# 没有使用 for-in 循环对数组进行迭代

现在有了 for-of 循环和获取对象键的方法，for-in 循环就不那么有用了。

此外，它迭代我们对象的原型，这可能是我们不想要的。

迭代的顺序也不能保证。

因此，我们不应该在代码中使用它。

而不是写:

```
for (const x in [1, 2, 3]) {
  console.log(x);
}
```

我们写道:

```
for (const x of [1, 2, 3]) {
  console.log(x);
}
```

for-in 循环遍历索引，而 for-of 循环遍历条目。

# 不要使用类似 eval 的方法

在 JavaScript 和扩展的 TypeScript 中，都有接受字符串并将其作为代码运行的方法。

有一种从字符串运行代码的`eval`方法。

`Function`构造函数也从字符串中返回一个函数。

另外，`setTimeout`和`setInterval`函数都可以运行字符串中的代码。

这阻止了任何优化的完成，因为代码在一个字符串中。

此外，它造成了一个很大的安全缺陷，因为任何人都可以输入带有代码的字符串并潜在地运行它。

因此，我们不应该运行任何允许我们从字符串运行代码的函数。

如果我们使用`setTimeout`或`setInterval`，我们应该传入一个回调而不是一个字符串。

例如，不写:

```
setTimeout('alert(`foo`);', 100);
```

或者:

```
const add = new Function('a', 'b', 'return a + b');
```

我们写道:

```
setTimeout(() => {
  alert(`foo`);
}, 100);
```

或者:

```
setInterval(() => {
  alert(`foo`);
}, 10000);
```

并且不要使用其他功能。

# 没有变量或参数的显式类型声明初始化为数字、字符串或布尔值

我们不需要为任何被明确赋值为数字、字符串或布尔值的东西指定类型。

这是因为从代码中可以明显看出它们是什么。

因此，与其写:

```
const a: number = 10;
```

我们写道:

```
const a = 10;
```

这适用于任何其他原始值，如字符串或布尔值。

如果返回类型很明显，那么我们也可以跳过类型注释。

所以，不要写:

```
const a: number = Number('1');
```

我们写道:

```
const a = Number('1');
```

这也适用于正则表达式。如果我们有一个正则表达式，那么我们不需要注释。

而不是写:

```
const a: RegExp = /foo/;
```

我们写道:

```
const a = /foo/;
```

# 以有效的方式使用 new 和构造函数

我们应该以有效的方式使用`new`和`constructor`。

因此，我们不应该分别使用它们来创建新的类实例或作为构造函数。

例如，不写:

```
class C {
  new(): C;
}
```

我们写道:

```
class C {
  constructor(){
    //...
  }
}const c = new C();
```

构造函数应该只在类中。

我们还可以将`new`指定为接口中的签名:

```
interface I {
  new (): C;
}
```

![](img/333f41eb9b2d65973451401f60f12d86.png)

照片由[约瑟夫·冈萨雷斯](https://unsplash.com/@miracletwentyone?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 不要在不是用来处理承诺的地方使用承诺

我们不应该在不是为处理承诺而设计的地方使用承诺。

例如，我们不应该把它们放在`if`语句或循环中。

所以与其写:

```
const promise = Promise.resolve('foo');

if (promise) {
  // Do something
}
```

或者:

```
const promise = Promise.resolve('foo');

while (promise) {
  // Do something
}
```

或者:

```
[1, 2, 3].forEach(async value => {
  await foo(value);
});
```

或者:

```
new Promise(async (resolve, reject) => {
  await doSomething();
  resolve();
});
```

我们应该写:

```
const promise = Promise.resolve('foo');

if (await promise) {
  // Do something
}
```

或者:

```
const promise = Promise.resolve('foo');while (await promise) {
  // Do something
}
```

或者:

```
for (const value of [1, 2, 3]) {
  await foo(value);
}
```

我们需要将`await`放在正确的位置，这样我们才能正确地获得解析后的值。

此外，以下内容无效:

```
new Promise(async (resolve, reject) => {
  await doSomething();
  resolve();
});
```

因为承诺里面有承诺是多余的。

我们可以将`await doSomething()`移到我们的承诺回调之外。

# 结论

承诺应该以有用的方式使用，否则就不应该使用。

不应该使用类似 eval 的函数，因为它们很危险。

for-in 循环应该用更好的替代方法来代替。