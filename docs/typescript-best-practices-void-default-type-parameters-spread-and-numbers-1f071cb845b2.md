# TypeScript 最佳实践—void、默认类型参数、跨页和数字

> 原文：<https://levelup.gitconnected.com/typescript-best-practices-void-default-type-parameters-spread-and-numbers-1f071cb845b2>

![](img/339a9734ba1a47d7ca4ce420ff7ce3c9.png)

Vincent van Zalinge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 没有空表达式

我们不应该在返回类型为`void`的函数中返回表达式。

例如，如果我们有`doWork`函数:

```
const doWork = (): void => {
  doFirst();
  doSecond();
};
```

我们应该调用`doWork`而不使用它的返回值。

所以与其写:

```
console.log(doWork());
```

我们写道:

```
doWork()
```

# 没有条件表达式

我们应该使用条件表达式，而不是给语句的每个分支分配相同的内容。

例如，不写:

```
let foo;
if (cond) [
  foo = 1;
}
else {
  foo = 2;
}
```

我们写道:

```
let foo = cond ? 1: 2;
```

它要短得多，也容易阅读。

# 使用对象扩散

对象扩散从 ES2018 开始就是一个特性，所以我们应该用它来代替`Object.assign`。

例如，不写:

```
const baz = Object.assign({}, foo, bar);
```

我们写道:

```
const baz = {...foo, ...bar};
```

# 包括 parseInt 中的 Radix 参数

我们应该在调用`parseInt`时包含基数参数，这样它就可以把值解析成我们想要的基数。

它也不会假定我们基于值解析的数字的基数。

例如，不写:

```
const x: string = '12';
const dec: number = parseInt(x);
```

我们写道:

```
const x: string = '12';
const dec: number = parseInt(x, 10);
```

# 使用加号运算符时，操作数应为字符串或数字类型

当我们使用`+`操作符时，我们应该确保它们都是字符串的数字。

这样，我们知道我们肯定是在添加或连接。

例如，不写:

```
const foo = 'foo' + 1;
```

我们写道:

```
const foo = 'foo' + 'bar';
```

或者:

```
const sum = 1 + 2;
```

# 在静态方法中不使用它

我们不应该在静态方法中使用`this`。

它们不应该引用`this`，因为它们不会引用我们期望的值，也就是类实例。

例如，代替书写；

```
class Foo {
  static foo() {
    return 'foo';
  } static bar() {
    return `bar${this.foo()}`;
  }
}
```

我们写道:

```
class Foo {
  foo() {
    return 'foo';
  } bar() {
    return `bar${this.foo()}`;
  }
}
```

# 严格比较

我们应该确保使用`===`和`!==`进行等式比较。

他们检查数据的类型和值。

所以与其写:

```
x == 1;
```

我们写道:

```
x === 1;
```

在比较之前避免任何数据类型强制。

# 严格字符串表达式

我们应该使用模板文字插值，而不是使用字符串连接。

例如，不写:

```
'foo' + bar
```

我们写道:

```
`foo ${bar}`
```

# 添加带有 Switch 语句的 Default 子句

我们应该添加一个带有`switch`语句的`default`子句，这样当没有一个`case`与值匹配时，我们可以做些什么。

例如，代替书写:

```
let foo: number = 1;
switch (foo) {
  case 1:
    doSomething();
    break;
  case 2:
    doMore();
}
```

我们写道:

```
let foo: number = 1;
switch (foo) {
  case 1:
    doSomething();
    break;
  case 2:
    doMore();
    break;
  default:
    console.log('default');
}
```

# 将 typeof 与正确的字符串值进行比较

我们应该将`typeof`与正确的字符串值进行比较。

`typeof`只能为类型返回几个字符串值。

所以我们应该与他们进行比较，确保我们没有任何错别字。

例如，不写:

```
typeof foo === 'undefimed';
```

我们写道:

```
typeof foo === 'undefined';
```

# 没有不必要的构造函数

我们不应该有多余的构造函数。

JavaScript 会在没有它的情况下为我们添加它们。

例如，不写:

```
class A {
  constructor(){}
}
```

或者

```
class A extends B {
  constructor(){
    super();
  }
}
```

我们写道:

```
class A {
  constructor(name){
    this.name = name;
  }
}
```

或者

```
class A extends B {
  constructor(bar){
    super(bar);
  }
}
```

# 默认类型参数

我们可以给泛型类型参数添加一个默认的类型值。

例如，不写:

```
function foo<N, S>() {}
```

我们可以写:

```
function foo<N = number, S = string>() {}
```

这样，泛型类型参数将总是用实参来设置。

![](img/dac03bfa8767129cff196293e7723c69.png)

图为[克洛维斯伍德](https://unsplash.com/@clo_shooting?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 结论

我们可以向泛型类型参数添加默认类型。

`void`表达式不应该作为值使用。

条件表达式、对象扩展、`parseInt`和基数都是我们在代码中应该有的东西。