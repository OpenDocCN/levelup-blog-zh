# TypeScript 中的 JavaScript 对象特性—继承

> 原文：<https://levelup.gitconnected.com/javascript-object-features-in-typescript-inheritance-a53a46efe43>

![](img/d8c059daedeeb29096e2b7444fd3d5a4.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究应该在 TypeScript 项目中使用的 JavaScript 的一些特性，包括对象继承。

# JavaScript 对象继承

JavaScript 对象可以链接到另一个对象。

这个物体被称为原型。

对象从原型继承属性和方法。

这允许我们实现复杂的特性，只需定义一次，就可以一致地使用。

当我们使用字面语法创建一个对象时，那么它的原型就是`Object`。

`Object`提供了一些它们继承的基本特性，比如返回对象的字符串表示的`toString`方法。

例如，如果我们有以下对象:

```
const obj = {
  foo: 1,
  bar: "baz"
};console.log(obj.toString());
```

如果我们定义`obj`并运行:

```
console.log(obj.toString());
```

我们让`[object Object]`登录。

这就是默认的`toString`方法在`Object`原型中的作用。

# 对象的原型

`Object`是大多数对象的原型。

我们也可以直接用一些方法。

有`getPrototypeOf`、`setPrototypeOf`和`getOwnPropertyNames`方法。

`getPrototypeOf`返回对象的原型。

例如，我们可以写:

```
const proto = {};
const obj = Object.create(proto);let objPrototype = Object.getPrototypeOf(obj);
```

然后我们得到`objPrototype`就是`Object`。

`proto`是我们传入`Object.create`以来`obj`的原型。

# 创建定制原型

同样，我们可以使用`setPrototypeOf`方法来设置现有对象的原型。

例如，我们可以写:

```
const proto = {};
const obj = {
  foo: 1
};Object.setPrototypeOf(obj, proto);
const objProto = Object.getPrototypeOf(obj);
console.log(proto === objProto);
```

上面的代码有两个对象，`obj`和`proto`。

然后我们用`obj`和`proto`调用`setPrototype`，将`proto`设置为`obj`的原型。

在最后一行，我们检查`proto`是否引用了与从`getPrototypeOf`返回的原型对象相同的对象。

控制台日志返回`true`所以我们知道`obj`的原型其实是`proto`。

# 构造函数

构造函数用于创建一个新对象，配置其属性，并分配其原型。

例如，我们可以通过编写以下内容来创建一个:

```
let Dog = function(name, breed) {
  this.name = name;
  this.breed = breed;
};Dog.prototype.toString = function() {
  return `name: ${this.name}, breed: ${this.breed}`;
};
```

我们有一个带有`name`和`breed`字段的`Dog`构造函数。

然后我们创建了一个名为`toString`的实例方法来返回一个`Dog`实例的字符串表示。

然后我们可以创建一个`Dog`实例并如下调用`toString`:

```
console.log(new Dog('joe', 'labrador').toString());
```

我们得到了:

```
'name: joe, breed: labrador'
```

在控制台日志输出中。

我们用关键字`new`调用了我们的构造函数。

参数被传入并在构造函数中设置为`this`的属性。

构造函数使用设置为新对象的`this`配置对象自身的属性。

构造函数返回的对象原型的`__proto__`设置为`Dog.prototype`。

所以`toString`来自`Dog`实例的`prototype`属性。

![](img/a532214fbe3deaf375d43eb657eefab7.png)

由 [Peter Wendt](https://unsplash.com/@peterwendt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 链接构造函数

我们可以通过编写以下代码来链接多个函数的压缩函数:

```
const Animal = function(name) {
  this.name = name;
};Animal.prototype.toString = function() {
  return `toString: name: ${this.name}`;
};const Dog = function(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
};Object.setPrototypeOf(Dog.prototype, Animal.prototype);
Dog.prototype.bark = function() {
  return "woof";
};const dog = new Dog("joe", "labrador");
console.log(dog.toString());
console.log(dog.bark());
```

我们有一个父`Animal`构造函数，它的原型中有一个`toString`方法。

然后我们添加了一个`Dog`构造函数，它继承了`Animal`构造函数。

在`Dog`构造函数中，我们调用`Animal`上的`call`方法来调用父`Animal`构造函数，但是将`this`设置为`Dog`构造函数。

`Dog`通过调用`setPrototypeOf`继承`Animal`，子构造函数在第一个参数中，父构造函数在第二个参数中。

然后我们向原型添加另一个方法，叫做`bark`到`Dog`的原型，所以我们只能在`Dog`实例上被调用。

`toString`对`Dog`和`Animal`都可用。

# 结论

JavaScript 的继承系统不同于其他面向对象语言的继承系统。

JavaScript 有构造函数和原型，而不是类。

对象可以直接从其他对象继承。构造函数可以从其他构造函数继承。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)