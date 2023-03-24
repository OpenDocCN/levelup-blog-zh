# 在 JavaScript 中使用类

> 原文：<https://levelup.gitconnected.com/using-classes-in-javascript-e677d248bb6e>

![](img/3ffe0e1eaaf50374cff207b22c22c65a.png)

[沙哈达特·谢穆尔](https://unsplash.com/@shemul?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 中的类是其原型继承模型的特殊语法，它是基于类的面向对象语言中的类似继承。类只是添加到 ES6 中的特殊函数，用来模仿其他语言中的关键字`class`。在 JavaScript 中，我们可以有`class`声明和`class`表达式，因为它们只是函数。所以像所有其他函数一样，有函数声明和函数表达式。

类充当创建新对象的模板。

**需要记住的最重要的事情:**类只是普通的 JavaScript 函数，不使用`class`语法也可以完全复制。它是 ES6 中添加的特殊语法糖，使声明和继承复杂对象变得更容易。

# 定义类别

要声明一个类，我们使用`class`关键字。例如，要声明一个简单的类，我们可以写:

```
class Person{
  constructor(firstName, lastName) {
    this.firstName= firstName;
    this.lastName = lastName;
  }
}
```

类声明没有被提升，所以在代码中定义之前不能使用，因为 JavaScript 解释器不会自动将它们提升到顶部。因此，在代码中定义之前，上面的类不会工作，如下所示:

```
const person = new Person('John', 'Smith');class Person{
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}
```

如果我们运行上面的代码，我们将得到一个`ReferenceError`。

我们也可以通过一个类表达式来定义一个类，这是另一种语法。它们可以是命名的，也可以是未命名的。我们也可以像处理函数一样，给变量分配一个类。如果我们这样做了，我们可以通过它的名字来引用这个类。例如，我们可以定义:

```
let Person = class {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}
```

要获得上面未命名的类的名称，我们可以用`name`属性获得名称，如下所示:

```
console.log(Person.name);
```

我们还可以定义一个命名类，如下所示:

```
let Person = class Person2 {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}
```

然后为了获得类名，我们可以再次使用`name`属性。所以我们如果我们写:

```
console.log(Person.name)
```

我们得到`Person2`日志。

类体是用花括号定义的。我们在括号内定义类成员。类的主体是在严格模式下执行的，所以在严格模式下定义的所有东西都适用于类的定义，所以我们不能像`var`、`let`或`const`那样定义前面没有关键字的变量，还有很多其他规则在你定义类的时候也适用。

JavaScript 中的类也有一个`constructor`方法，让我们在用`class`实例化对象时设置字段。每个类中只能有一个`constructor`方法。如果不止一个，那么就会抛出`SyntaxError`。如果类扩展了父类，那么`constructor`也可以调用`super`方法来调用超类的`constructor`。

未声明的方法`static`构成了该类的原型方法。在使用`new`关键字创建一个对象后，它们被调用。例如，下面的类只有原型方法:

```
class Person{
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  } get fullName(){
    return `${this.firstName} ${this.lastName}`  
  } sayHi(){
    return `Hi, ${this.firstName} ${this.lastName}`
  }
}
```

在上面的`Person`类中，`fullName`和`sayHi`是原型方法。它们被这样称呼:

```
const person = new Person('Jane', 'Smith');
person.fullName() // 'Jane Smith'
```

静态方法是不用使用`new`关键字从类中创建对象就可以调用的方法。例如，我们可以有如下内容:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
  get fullName() {
    return `${this.firstName} ${this.lastName}`
  }
  sayHi() {
    return `Hi, ${this.firstName} ${this.lastName}`
  }
  static personCount() {
    return 3;
  }
}
```

我们可以调用`personCount`函数而不使用`new`关键字来创建类的实例。所以如果我们写:

```
Person.personCount
```

我们得到 3 个返回。

原型方法中的`this`值将是对象的值。对于静态方法，`this`的值具有静态方法所在的类作为值。

![](img/ce4abb6d10f879a8fd1c9e7ca80a4c3b.png)

照片由[托马斯·凯利](https://unsplash.com/@thkelley?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Getters 和 Setters

JavaScript 类可以有 getter 和 setter 函数。顾名思义，Getters 是一种让我们从类中获取一些数据的方法。Setters 是让我们能够设置类的一些字段的方法。我们用关键字`get`表示 getter 函数，用关键字`set`表示 setter 函数。例如，我们可以编写一个具有 getters 和 setters 的类，如下所示:

```
class Person {
  constructor(firstName, lastName) {
    this._firstName = firstName;
    this._lastName = lastName;
  }
  get fullName() {
    return `${this.firstName} ${this.lastName}`
  }
  get firstName() {
    return this._firstName
  }
  get lastName() {
    return this._lastName
  }
  sayHi() {
    return `Hi, ${this.firstName} ${this.lastName}`
  }
  set firstName(firstName) {
    this._firstName = firstName;
  }
  set lastName(lastName) {
    this._lastName = lastName;
  }
}
```

那么当我们使用`new`关键字来构造一个`Person`对象时，我们可以按照以下方式使用它们:

```
const person = new Person('Jane', 'Smith');
person.firstName = 'John';
person.lastName = 'Doe';
console.log(person.firstName, person.lastName)
```

因为我们有 getter 和 setter 函数，我们可以用它们直接设置数据来设置`Person`类的`firstName`和`lastName`的数据。在以关键字`set`开始的 setter 函数中，当我们给它们赋值时，它传递给参数并在类的成员中设置。在由`get`表示的 getter 函数中，我们返回成员值，该值触发相关的`get`函数。

# JavaScript 继承

在 JavaScript 中，我们可以创建一些类，这些类的属性可以包含在子类的属性中。

因此，我们可以有一个包含所有子类共有的属性的高级类，并且子类可以有自己的特殊属性，这些属性不在任何其他类中。

例如，如果我们有一个具有公共属性和方法的`Animal`类，比如`name`和`eat`方法，那么`Bird`类可以继承`Animal`类中的公共属性。它们不必在`Bird`类中再次定义。

我们可以用 JavaScript 编写以下代码来实现继承:

```
class Animal {
  constructor(name) {
    this.name = name;
  }
  eat() {
    console.log('eat');
  }
}class Bird extends Animal {
  constructor(name, numWings) {
    super(name);
    this.numWings = numWings;
  }
}const bird = new Bird('Joe', 2);
console.log(bird.name)
bird.eat();
```

在上面的例子中，我们有父类`Animal`，它有`eat`方法，所有来自`Animal`的`extends`类都会有，所以它们不必再定义`eat`。

我们有扩展了`Animal`类的`Bird`类。注意，在`Bird`类的`constructor`中，我们有`super()`函数调用来调用父类的构造函数，以填充父类的属性和子类的属性。

类不能扩展常规对象，常规对象不能用关键字`new`构造。如果我们想从一个常规对象继承，我们必须使用`Object.setPrototypeOf`函数来设置一个从常规对象继承的类。例如:

```
const Animal = {
  eat() {
    console.log(`${this.name} eats`);
  }
};class Cat{
  constructor(name) {
    this.name = name;
  }
}class Chicken{
  constructor(name) {
    this.name = name;
  }
}Object.setPrototypeOf(Cat.prototype, Animal);
Object.setPrototypeOf(Chicken.prototype, Animal);let cat = new Cat('Bob');
let chicken = new Chicken('Joe');
cat.eat();
chicken.eat();
```

如果我们运行上面的示例代码，我们可以看到记录的`Bob eats`和`Joe eats`，因为我们从`Animal`对象继承了`eat`函数。

# `this`关键字

关键字`this`允许我们在对象内部访问当前对象的属性，除非你使用了箭头函数。

从上面的例子中我们可以看到，我们可以在对象中获得子类和父类的实例的属性。

# 混合蛋白

我们可以使用 mixins 在 JavaScript 中进行多重继承。Mixins 是创建类的模板。我们需要 mixins 来做多重继承，因为 JavaScript 类只能从一个超类继承，所以多重继承是不可能的。

例如，如果我们有一个基类，我们可以定义 mixin，通过调用一个 mixin 来组合多个类中的成员，然后将返回的结果作为参数传递给下一个 mixin，以此类推，如下所示:

```
class Base {
  baseFn() {
    console.log('baseFn called');
  }
}let classAMixin = Base => class extends Base {
  a() {
    console.log('classAMixin called');
  }
};let classBMixin = Base => class extends Base {
  b() {
    console.log('classBMixin called');
  }
};class Bar extends classAMixin(classBMixin(Base)) {}
const bar = new Bar();
bar.baseFn()
bar.a()
bar.b()
```

在上面的代码中，我们将`Base`类传递给`classBMixin`以将`b`函数传递给`Base`类，然后我们通过将`classBMixin(Base)`的结果传递给`classAMixin`的参数来调用`classAMixin`，以将`classAMixin`中的`a`函数返回给`Base`类，然后返回整个类，并将所有类中的所有函数合并为一个。

如果我们像创建一个`Bar`对象的实例那样调用上面的所有函数，然后调用`baseFn`、`a`和`b`函数，我们会得到:

```
baseFn called
classAMixin called
classBMixin called
```

这意味着我们已经将 mixins 中的所有函数合并到了新的`Bar`类中。

在 JavaScript 中，类只是语法糖，通过让我们以一种更像基于类的面向对象继承模式中的典型继承的方式来构造代码，使 JavaScript 的原型继承更加清晰。这意味着我们编写类来使用`new`关键字从类中创建对象，但是在语法糖的下面，我们仍然使用原型继承来扩展对象。我们可以从对象扩展类，也可以使用 mixins 在 JavaScript 类中进行多重继承。