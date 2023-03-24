# JavaScript 最佳实践—基本语法

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-basic-syntax-e4a20c7b69b6>

![](img/afafa4b7273ef8aded3cd6d814bd40a6.png)

照片由[菲尔在](https://unsplash.com/@philhearing?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上听到

像任何种类的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将研究一些在编写 JavaScript 代码时应该遵循的最佳实践。

# 刻痕

我们应该有两个缩进空间。这在保持可见性的同时最大限度地减少了输入。

例如，我们写道:

```
function hello (name) {
  console.log('hi')
}
```

# 字符串的单引号

单引号有利于分隔字符串，因为我们不必像键入双引号那样按 shift 键。

例如，我们可以写:

```
'hello world'
```

为了节省我们打字时的力气。

# 没有未使用的变量

我们不用的变量是没用的，所以不应该有。

# 在关键字后添加一个空格

关键字后面的空格总是有用的，因为它使我们的代码更容易阅读。

所以我们应该加上它们:

```
if (condition) { ... }
```

没有空格会使内容难以阅读。

# 在函数声明的括号前添加一个空格

为了更好的可读性，我们应该在函数声明的括号前加一个空格，

例如，我们应该写:

```
function foo (arg) { ... }
```

# 总是用===而不是==

`===`在比较其操作数之前，不会以复杂的规则进行自动数据类型转换。

因此，这使得`===`比`==`更可取。

通过使用它，它将帮助我们避免许多错误。

所以我们写道:

```
if (name === 'james')
```

而不是:

```
if (name == 'james')
```

同样，出于同样的原因，我们应该使用`!==`而不是`!=`进行不等式比较。

例如，我们写道:

```
if (name !== 'John')
```

而不是:

```
if (name != 'John')
```

但是，我们可以使用`==`在一个表达式中检查`null`和`undefined`。

例如，我们可以写:

```
if (foo == null)
```

为了做到这一点。

# 中缀运算符必须有空格

我们应该把像`=`或算术运算符这样的中缀运算符隔开。

比如 e 我们不应该'；r 写:

```
let x=2;
```

相反，我们写道:

```
let x = 2;
```

同样，我们应该写:

```
let message = 'hello, ' + name + '.'
```

而不是:

```
let message = 'hello, '+name+'.'
```

这样，我们可以更容易地阅读代码，特别是当我们的表达式中有很多操作符时。

# 逗号应该有一个空格字符

逗号后面应该有一个空格。

例如，我们应该写:

```
const list = [1, 2, 3]
```

或者:

```
const greet = (name, options) => { ... }
```

而不是:

```
const list = [1,2,3]
```

或者:

```
const greet = (name,options) => { ... }
```

# 将 else 语句与其花括号放在同一行

我们应该把`else`和他们的花括号保持在一条线上。

例如，我们应该写:

```
if (condition) {
  // ...
} else {
  // ...
}
```

而不是:

```
if (condition) {
  // ...
} 
else {
  // ...
}
```

它在保持可读性的同时节省了一些空间。

# 对多行语句使用花括号

我们应该对`if`语句使用花括号，这样我们就不会混淆。

例如，我们可以写:

```
if (options.foo) console.log('done')
```

或者:

```
if (options.foo) {
  console.log('done')
}
```

然而，我们不应该写:

```
if (options.foo)
  console.log('done')
```

因为`if`块在哪里开始或结束是欺骗性的。

# 总是处理 err 函数参数

如果回调有一个`err`参数，那么我们应该处理它，如果它被定义的话。

比如说。我们写道:

```
run(function (err) {
  if (err) throw err
  console.log('done')
})
```

如果`err`被定义了，那么我们就扔了它。

我们不应该做的，如果忽略它，假装什么都没发生。

# 用全局注释声明浏览器全局

为了确保我们没有定义未声明的全局变量，我们应该添加一个注释或引用全局对象来访问全局变量。

例如，我们写道:

```
/* global alert */alert('hi')
```

我们也可以写:

```
window.alert('hi');
```

`window`是浏览器中的全局对象，所以我们应该显式地写出来。

# 没有多个空行

有多个空行是没用的，只保留一个最大值，去掉其余的。

我们写道:

```
const value = 'hello world';
console.log(value);
```

而不是:

```
const value = 'hello world';console.log(value);
```

![](img/57806990a8378845aee5d388895f33a5.png)

照片由[卢茨·鲍曼](https://unsplash.com/@luba1304?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

如果我们使用全局变量，我们应该指出它们。

此外，在大多数情况下，我们应该使用`===`或`!==`进行比较。

缩进和空格使得阅读代码更加容易。