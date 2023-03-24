# JavaScript 中需要注意的模糊语法

> 原文：<https://levelup.gitconnected.com/ambiguous-syntax-to-look-out-for-in-javascript-3bebe1aa1253>

![](img/5b5e7b125dc2e7b7dd2498b0846beabc.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=medium&utm_medium=referral)上的 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

就语法而言，JavaScript 是一种非常宽容的语言。因此，我们可能会写很多代码，给阅读代码的人造成混乱。一段相似的代码可能会做不同的事情。

在本文中，我们来看看一些需要注意和避免的模糊语法。

# 函数声明和函数表达式

函数声明是:

```
function foo(x) {
  return x;
}
```

一个函数表达式是:

```
const foo = function id(x) {
  return x;
};
```

上面的代码都创建了一个名为`foo`的函数，但是它们非常不同。JavaScript 解释器将函数声明放在代码的顶部，这样就可以在代码文件的任何地方调用它们。

另一方面，函数表达式只能在定义后被调用。

为了避免这种情况，我们应该对不需要绑定到`this`的函数使用箭头函数，或者对构造函数使用类。

此外，JavaScript 解释器从不将以`function`或`{`开头的语句解释为表达式。如果我们想将函数转换成表达式 we，那么必须将它们括在括号中，以便将它们转换成立即调用的函数表达式(IIFEs ),如下所示:

```
(function (x) { console.log(x) })('foo');
```

在上面的代码中，我们创建了匿名函数`function (x) { console.log(x) }`,然后用括号将它括起来，转换成一个生命。然后我们在用`('foo')`创建它之后马上调用它。

# 对象文字和块

对象文字和块都以花括号结尾。以下是一个对象文字:

```
const obj = {};
```

而下面是一块:

```
{}
```

然而，当我们填充它们时，我们会看到不同之处。例如，对象文字用下面的方法写属性:

```
const obj = {
  foo: 1
}
```

块按以下方式编写:

```
{
  let foo = 1;
}
```

我们可以运行任何可以在块中任何地方运行的代码。只有在块中声明的那些变量都在同一范围内。

对象文本只能包含属性、值，不能包含其他内容。

![](img/e412cb2872d1c95515e52fa8d4e72cd5.png)

照片由 [Alvan Nee](https://unsplash.com/@alvannee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 自动分号插入(ASI)

自动插入分号是 JavaScript 解释表达式的方式。它会自动在行结束符后添加分号，后跟一个非法标记。也就是说，它会自动在换行符后插入分号。

例如，当一个`return`语句的‘操作数’在新的一行时，我们会遇到 ASI 的问题:

```
return
{
  foo: 1
};
```

上面的代码将被解释为:

```
return;
{
  foo: 1
}
;
```

因此，该对象没有被解释为`return`的操作数。相反，它只是带着`undefined`回来了。

为了纠正这一点，我们应该写:

```
return {
  foo: 1
};
```

这样，`return`语句就不会有歧义。当我们把开始的花括号和`return`放在同一行时，这个对象将被返回，我们把分号放在末尾，这样 JavaScript 解释器就不会自己插入它。

JavaScript 解释器这样做的原因是为了防止在`return`之后意外返回值。

也有这样的情况，当我们期望触发 ASI 时，它却没有被触发。例如，如果我们运行:

```
const b = 2,
  c = 3,
  d = 4,
  e = 5;
a = b + c
(d + e).toString()
```

然后我们会得到一个错误，说“c 如果不是一个函数”。这是因为最后两行被解释为:

```
a = b + c(d + e).toString()
```

另一个模糊语法的例子是:

```
const b = 2,
  c = 3,
  d = 4,
  e = 5,
  g = 7;
a = b
/e/g.exec(c)
```

`g`被解释为一个对象，而不是 regex 文本的`g`标志。

同样，我们应该自己加上分号来避免这些歧义。

# 结论

JavaScript 代码中有一些模糊的语法，我们应该避免使用。

首先是函数表达式和函数声明。我们应该对不绑定到`this`的函数使用箭头函数，对构造函数使用类。无论是箭头函数还是类都没有提升，所以我们知道它们只能在定义后使用。

此外，我们应该避免空块和对象文字，这会造成混淆。

最后，我们应该注意分号的自动插入。我们应该自己插入它，而不是让 JavaScript 解释器为我们添加它，弄乱我们的代码。