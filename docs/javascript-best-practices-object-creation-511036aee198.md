# JavaScript 最佳实践—对象创建

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-object-creation-511036aee198>

![](img/817cdcf8b796085fa90b7d2b8f0c5044.png)

照片由 [russn_fckr](https://unsplash.com/@russn_fckr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

使用默认参数和属性缩写，清理我们的 JavaScript 代码很容易。

在本文中，我们将研究在 JavaScript 代码中创建对象的最佳实践。

我们将研究对象文字、工厂函数、`Object.create`和构造函数/类的使用。

# 对象文字

我们可以用对象文字符号创建对象。它不需要创建类、构造函数或原型。

我们只是将所有内容放在花括号内，如下所示:

```
const obj = {
  a: 1,
  b: 2
};
```

这对于只创建一次的对象很有好处。

如果我们需要创建多个具有相同结构的对象，那么我们必须找到另一种方法。

# 工厂功能

工厂函数是返回新对象的函数。

例如，我们可以写:

```
const create = () => ({
  a: 1,
  b(){}
});const obj = create();
```

我们调用`create`对象来返回并分配对象:

```
{
  a: 1,
  b(){}
}
```

去`obj`。

工厂函数会导致内存膨胀，因为每个对象都有自己唯一的`b`方法副本。

# 原型链

我们可以通过使用`Object.create`方法创建继承另一个对象属性的对象。

这种继承被称为原型链。

例如，我们可以使用如下方法:

```
const create = () => {
  const parent = {
    a: 1,
    b() {}
  }
  return Object.create(parent);
};const obj = create();
```

现在用`create`创建的每个对象都继承了`parent`对象的属性。

创建的对象总是具有带有`parent`属性的`__proto__`属性。

# ES5 构造函数

构造函数是充当对象模板的函数。

每个对象都是作为构造函数的字段和方法创建的。

它的所有实例都引用这些字段和方法。

例如，我们可以定义如下:

```
function Person(name) {
  this.name = name;
}Person.prototype.greet = function() {
  return `hi ${this.name}`;
}
```

然后我们可以使用`new`关键字通过编写以下代码来创建一个`Person`构造函数的实例:

```
function Person(name) {
  this.name = name;
}Person.prototype.greet = function() {
  return `hi ${this.name}`;
}const joe = new Person('joe');
const greeting = joe.greet();
```

`greet`方法是`Person`构造函数的实例方法，所以我们可以在从`new`关键字创建的对象上调用它。

如果我们将一个方法附加到构造函数的`prototype`属性，那么它就成为构造函数的方法。

我们不想再用它了，因为它又长又丑，而且容易出错。

# ES6 类

更好的方法是使用 ES6 类，它是 ES5 构造函数之上的语法糖。

它们是相同的东西，只是语法更好。

因此，我们可以将上面的例子改写为:

```
class Person {
  constructor(name) {
    this.name = name;
  } greet() {
    return `hi ${this.name}`;
  }
}const joe = new Person('joe');
const greeting = joe.greet();
console.log(greeting);
```

在上面的代码中，下列内容:

```
class Person {
  constructor(name) {
    this.name = name;
  } greet() {
    return `hi ${this.name}`;
  }
}
```

与以下内容相同:

```
function Person(name) {
  this.name = name;
}Person.prototype.greet = function() {
  return `hi ${this.name}`;
}
```

这两个示例的其余代码是相同的。

![](img/182f780b076769fde246ce637f18d50e.png)

[胡伊森](https://unsplash.com/@ethanhjy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 表演

类语法比返回对象文字的工厂函数大约快 3 倍。

这就是使用 ES6 类语法的更多原因。对开发者和浏览器来说都更快。

# 私人数据

我们可以用闭包和弱映射封装私有数据。

我们只需要用`let`和`const`给类和函数的 let 添加私有变量。那么它们在函数之外是不可用的。

只有当对键的引用仍然存在时，才能检索弱映射。一旦它被破坏，那么给定键的值就没有了。

它们都允许我们将不同的属性混合到一个构造函数中，或者将对象混合到一个实体中。

# 结论

对象文字和工厂函数提供了创建一次性对象的简单方法。

如果我们想创建多个具有相同结构的对象，那么我们必须使用类或`Object.create`方法。

类语法创建对象的速度比工厂函数快，所以这可能是使用类的一个优势。