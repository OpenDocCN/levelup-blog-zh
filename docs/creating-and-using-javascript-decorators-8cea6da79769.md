# 创建和使用 JavaScript 装饰器

> 原文：<https://levelup.gitconnected.com/creating-and-using-javascript-decorators-8cea6da79769>

![](img/2a5d5dccae43e8daee821b8991aac6a9.png)

帕特里克·施耐德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

装饰器是修改其他函数返回值的函数。这对于将相同的修改应用于多个函数非常有用。

在本文中，我们将研究如何在 JavaScript 中创建 decorators，并使用它们来修改方法和类。

# JavaScript 装饰器的特征

## 方法装饰者

JavaScript 方法装饰器是带有参数`target`、`property`和`descriptor`的函数。`target`是我们正在修改的方法所在的类，`property`是我们正在修改的方法的属性名称字符串，`descriptor`是属性中概述的属性的描述符。

装饰器只能在类中使用，即使 JavaScript 类只是构造函数之上的语法糖。

此外，这是一个相对较新的构造，可能不被许多浏览器支持，所以我们可能需要 Babel 来使它们工作。

例如，如果我们用下面的`readonly`装饰器将一个类方法设为只读，而将`Person`类设为只读:

```
const readonly = (target, property, descriptor) => {
  descriptor.writable = false
  return descriptor
}class Person {
  constructor(name, age) {
    this.name = name
    this.age = age
  } @readonly
  description() {
    return `${this.name} is ${this.age}`
  }
}
```

然后`target`是`Person`类，它有被称为`Person`的`constructor`和`description`方法。

`property`是我们正在修改的类成员的字符串名称。在上面的代码中，这将引用`Person`类的`description`方法，所以它应该是字符串`'description'`。

`descriptor`有描述的属性描述符。在 JavaScript 中，属性描述符是具有以下属性的对象:

*   `value` —财产的价值
*   `writable` —一个布尔值，指示值是否可以更改
*   `enumerable` —一个布尔值，指示它是否会被`for...in`循环遍历
*   `configurable` —一个布尔值，指示我们是否可以更改给定属性的属性描述符。

在上面的`readonly` decorator 函数中，我们将`descriptor.writable`设置为`false`，因此在`readonly` decorator 被应用到`description`方法后，属性的值不能被更改。

我们可以通过编写以下代码来确认这一点:

```
let person = new Person('Joe', 10);
console.log(person.description);
person.description = 'foo';
console.log(person.description);
```

在我们将`person.description`设置为`foo`之前，我们得到:

```
ƒ description() {
    return `${this.name} is ${this.age}`;
  }
```

在将其设置为`foo`后，我们仍然得到:

```
ƒ description() {
    return `${this.name} is ${this.age}`;
  }
```

所以实际上并没有把`person.description`设置成`foo`。这证实了我们的`readonly`装饰器阻止我们覆盖`description`方法的值。

![](img/469829bd6ab11747aae4fcd1ea7c1a53.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 班级装饰者

类装饰器定义在类的顶部。它用于修改类或构造函数。因此，它需要返回一个构造函数或一个新类。

类装饰器只接受一个参数，即定义它们的类。

例如，我们可以如下使用它:

```
const addGender = (PersonClass) => {
  PersonClass = class Person {
    constructor(name, gender) {
      this.name = name;
      this.gender = gender;
    }
  }
  return PersonClass;
}[@addGender](http://twitter.com/addGender)
class Person {
  constructor(name) {
    this.name = name;
  }
}let person = new Person('Joe', 'male');
console.log(person.gender);
```

性别是我们的班级装饰者。它需要一个`PersonClass`，是一个类。

在函数中，我们只是将`PersonClass`赋给了一个新类，并将其返回。新类在构造函数中接受一个`name`和`gender`。

那么我们可以如下使用它:

```
let person = new Person('Joe', 'male');
console.log(person.gender);
```

我们应该看到`person.gender`就是`'male'`。

这意味着`person`是我们返回的类的一个实例。我们也可以通过记录`person.constructor`来检查这一点。

我们也可以返回一个构造函数而不是一个类:

```
const addGender = (PersonClass) => {
  PersonClass = function Person(name, gender) {
    this.name = name;
    this.gender = gender;
  }
  return PersonClass;
}[@addGender](http://twitter.com/addGender)
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

如果我们像下面这样实例化它，我们应该得到和以前一样的结果:

```
let person = new Person('Joe', 'male');
console.log(person.gender);
```

# 结论

我们可以创建 JavaScript 装饰器来修改方法和类。

一个方法装饰器将`target`、`property`和`descriptor`作为参数。我们可以返回 change，并在我们的方法装饰函数中返回`descriptor`。

在类装饰函数中，我们返回一个新的类或构造函数。它以一个类作为参数。我们不必修改传入的类。我们可以随意返回一个新类。