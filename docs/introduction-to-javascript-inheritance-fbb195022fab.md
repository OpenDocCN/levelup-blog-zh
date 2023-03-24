# JavaScript 继承简介

> 原文：<https://levelup.gitconnected.com/introduction-to-javascript-inheritance-fbb195022fab>

![](img/105fadb43e0b746182e50c45cb796301.png)

照片由 [Tra Nguyen](https://unsplash.com/@thutra0803?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种面向对象的语言。然而，它与许多其他面向对象语言的不同之处在于，它使用基于原型的继承，而不是基于类的继承。

基于原型的继承意味着对象从它的原型继承项目。原型只是另一个对象，可以被其他对象继承。

这与基于类的继承不同，因为类是创建新对象的模板。类可以从其他类继承，以重用它所继承的类的代码。

# 继承的旧语法

## 构造函数

在 ES6 之前，我们只有构造函数作为创建新对象的模板，这些对象是构造函数的实例。

例如，我们可以如下定义一个构造函数:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}
```

然后我们可以通过编写以下代码来创建一个新的`Person`实例:

```
let person = new Person('Joe', 10);
```

要从构造函数中的其他构造函数继承项目，我们必须用`call`方法调用我们想要继承的父构造函数，然后将我们的构造函数原型的`constructor`属性设置为我们想要继承的父构造函数。

例如，如果我们想让一个`Employee`构造函数继承`Person`构造函数的属性，我们可以写:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}function Employee(name, age, title) {
  this.title = title;
  Person.call(this, name, age);
  this.__proto__.constructor = Person;
}let employee = new Employee('Joe', 20, 'waiter');
console.log(employee);
```

`call`方法获取我们想要设置的`this`的值，其余的是我们传递给调用`call`方法的函数的参数。

如果我们查看有原型的`employee`对象的`__proto__`属性，我们应该得到它的`__proto__.constructor`应该是我们设置的`Person`构造函数。

`employee`对象的属性和值应该是我们调用`Employee`构造函数时传递给它的内容。

## Object.create()

`Object.create()`方法是我们创建对象时从原型继承的另一种方式。

它采用的参数是我们希望从它返回的对象继承的原型对象。

例如，我们可以使用`Object.create`创建一个带有原型的对象，如下所示:

```
const person = {
  name: 'Joe',
  age: 20
}let employee = Object.create(person);
employee.title = 'waiter';console.log(employee);
```

如果我们看一下`employee`对象，我们将会看到`__proto__`属性将会为`age`和`name`属性设置值。

## 直接设置 __proto__ 属性

从 ES6 开始，官方就支持直接设置`__proto__`属性，这是一种在 Firefox 之前在各种浏览器中设置对象原型的非正式方式。

我们可以直接将一个对象设置为`__proto__`属性，编写如下代码:

```
const person = {
  name: 'Joe',
  age: 20
}let employee = {
  title: 'waiter'
};employee.__proto__ = person;
console.log(employee);
```

我们应该得到属性和值的确切结构，就像我们用`Object.create()`方法创建一个对象时所做的那样。

我们必须小心的一件事是，如果我们不想改变一个对象的原型，我们不想意外地设置它。如果我们使用 JavaScript 对象作为地图，这可能会发生。在 ES6 中，我们可以使用`Map`对象来实现这个目的。

## 对象.定义属性

我们也可以使用`defineProperty`方法来设置一个对象的原型。例如，我们可以写:

```
const person = {
  name: 'Joe',
  age: 20
}let employee = {
  title: 'waiter'
};Object.defineProperty(employee, '__proto__', {
  value: person
});
console.log(employee.__proto__);
```

当我们记录`employee.__proto__`的值时，我们得到了`person`对象。

请注意，原型位于`defineProperty`方法调用的第三个参数的`value`属性中。

![](img/c3b2ef4bf5ec6693b36b7b2f1e493d1b.png)

Chiara Daneluzzi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 新的类语法

随着 ES6 的发布，引入了新的类语法。表面上，看起来我们有基于类的继承，但在表面下，它和以前完全一样。

类语法与构造函数相同。举个例子，

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}
```

与以下内容相同:

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
```

我们可以通过编写以下代码来实例化这两者:

```
const person = new Person('Joe', 10);
```

当我们检查它的属性时，我们得到相同的对象。

类语法还创建了一种清晰方便的继承方式，看起来像传统的基于类的继承。

我们可以创建一个超类，子类可以用`extends`关键字继承它。例如，我们可以写:

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}class Employee extends Person {
  constructor(name, age, title) {
    super(name, age);
    this.title = title;
  }
}const employee = new Employee('Joe', 20, 'waiter');
```

在上面的代码中，我们用`extends`关键字来表示`Employee`继承自哪个类。我们只能继承一个阶级。

调用`super`方法来调用父构造函数并设置其属性。在这种情况下，调用`super`将调用`Person`类中的`constructor`方法。

`this`指它在各个阶层里面的阶层。

这和我们之前做的完全一样:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}function Employee(name, age, title) {
  this.title = title;
  Person.call(this, name, age);
  this.__proto__.constructor = Person;
}let employee = new Employee('Joe', 20, 'waiter');
console.log(employee);
```

唯一的问题是，当我们检查`employee`对象时，我们得到的是`__proto__.constructor`属性显示的是`class`而不是`function`。

类语法使得继承比以前更加清晰。从一开始，JavaScript 中的原型继承模型就非常需要语法糖。

同样，有了类语法，我们不必再调用父构造函数对象上的`call`方法并设置`this.__proto__.constructor`。

这比使用`Object.create()`或者直接设置`__proto__`属性要好。设置`__proto__`属性有它的问题，比如不小心设置了错误的原型。