# TypeScript 中的 JavaScript 对象特性—继承和类

> 原文：<https://levelup.gitconnected.com/javascript-object-features-in-typescript-inheritance-and-classes-5fff7f1bcdb7>

![](img/ae08fba795912177f9b26eac4ee667da.png)

布鲁克·拉克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究访问被覆盖的原型方法和类语法。

# 访问重写的原型方法

即使我们在从父构造函数继承的构造函数中重写方法，我们仍然可以访问父构造函数对构造函数的实现。

例如，如果我们有:

```
const Animal = function(name) {
  this.name = name;
};
Animal.prototype.toString = function() {
  return `name: ${this.name}`;
};const Dog = function(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
};
Object.setPrototypeOf(Dog.prototype, Animal.prototype);
Dog.prototype.toString = function() {
  return `name: ${this.name}, breed: ${this.breed}`;
};
```

然后我们可以调用父构造函数的方法来替换`Dog`的原型的`toString`方法的一部分，编写如下:

```
const Animal = function(name) {
  this.name = name;
};Animal.prototype.toString = function() {
  return `name: ${this.name}`;
};const Dog = function(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
};Object.setPrototypeOf(Dog.prototype, Animal.prototype);Dog.prototype.toString = function() {
  const name = Animal.prototype.toString.call(this, this.name);
  return `${name}, breed: ${this.breed}`;
};
```

我们重用了`Animal.prototype`的`toString`方法，形成了`Dog.prototype`的`toString`方法的一部分。

这样，我们就不用重复任何代码了。

# 定义静态属性和方法

我们可以将静态方法和属性定义为函数本身的属性。

我们可以这样做，因为函数只是普通的对象。

例如，我们可以写:

```
const Animal = function(name) {
  this.name = name;
};
Animal.type = 'animal';console.log(Animal.type);
```

然后我们在`Animal`上定义了一个静态的`type`属性。

然后我们可以通过引用`Animal.type`来访问它。

# JavaScript 类

JavaScript class 是一种语法，可以简化从其他流行编程语言的转换。

然而，在幕后，它只是构造函数和原型的组合。

JavaScript 类和其他基于类的语言(如 Java)有一些不同。

所有实例变量都是公共的。

同样，我们可以在`constructor`中返回任何我们想要的对象，当我们用`new`调用类时，我们返回那个对象。

像构造函数一样，它是用关键字`new`调用的。

例如，我们可以这样定义一个类:

```
class Animal {
  constructor(name) {
    this.name = name;
  } toString() {
    return `name: ${this.name}`;
  }
}
```

我们有一个`Animal`构造函数，它返回一个`Animal`实例，带有`name`属性和`toString`方法。

然后，如果我们通过编写以下内容来创建一个`Animal`实例:

```
const animal = new Animal('joe');
```

如果我们查看`animal`的内容，我们会看到`animal`中的`name`属性。

在它的`__proto__`属性中，我们看到了`toString`方法。

为了创建一个继承自父类的子类，我们使用了`extends`关键字。

例如，我们可以写:

```
class Animal {
  constructor(name) {
    this.name = name;
  } toString() {
    return `name: ${this.name}`;
  }
}class Dog extends Animal {}
```

我们创建了一个继承自`Animal`父类的`Animal`子类。

然后当我们写下:

```
const dog = new Dog('joe');
```

并调用`toString`:

```
console.log(dog.toString());
```

我们得到:

```
name: joe
```

从控制台日志中。

`toString`方法继承自`Animal`类。

为了调用父构造函数，我们使用了`super`关键字。

如果我们想调用父构造函数的方法，我们使用相同的关键字，后跟一个点和方法名。

例如，如果我们想给`Dog`添加一个构造函数和一个`toString`方法，我们可以写:

```
class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  } toString() {
    const name = super.toString();
    return `${name} breed: ${this.breed}`;
  }
}
```

给定我们之前拥有的`Animal`类，我们可以通过编写调用`super.toString();`来调用`Animal`的`toString`。

我们像在`Dog`的`constructor`中一样，通过使用`super`关键字来调用父节点的`constructor`。

在构造函数体中，`super`调用必须在其他任何东西之前。

关键字`extends`表明我们的`Dog`类继承了`Animal`类的成员。

因此，当我们通过编写以下代码创建一个新的`Dog`实例时:

```
const dog = new Dog('joe', 'lab');
console.log(dog.toString());
```

然后在上面调用`toString`，我们得到:

```
name: joe breed: lab
```

从控制台日志中。

![](img/adc8aea25ca0ef7f1ca7b333dfa6893c.png)

照片由[丹金](https://unsplash.com/@danielcgold?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

类语法使得从其他面向对象语言的转换更加容易。

此外，它通过将构造函数代码放在一个整洁的包中来清理它们。

我们也可以用更简洁的方式调用父构造函数。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)