# 飞舞的飞镖:噢

> 原文：<https://levelup.gitconnected.com/fluttering-dart-oop-8b92cd89a7f0>

## [飘动的飞镖](https://levelup.gitconnected.com/fluttering-dart/home)

## 类、对象、接口等等

![](img/22d79f6dcd1ad9be19edfa5db034538a.png)

[Flutter](https://flutter.dev) 项目可以使用特定于平台和跨平台的代码。后者是用 [Dart](https://dart.dev) 写的，而且，构建 Flutter apps，需要一些 Dart 的基础知识。

在本系列的前几部分中，我们浏览了 Dart[](https://medium.com/@constanting/fluttering-dart-9a3e74b0d9c5)****[**函数**](https://medium.com/@constanting/fluttering-dart-b37110f4d1bf)**[**运算符**](https://medium.com/@constanting/fluttering-dart-ee493f4b0440) 和 [**控制流语句**](https://medium.com/@constanting/fluttering-dart-the-flow-7be2080763ad) 。******

****在这一部分，我们将发现 Dart 是真正的面向对象编程语言。****

****可以使用[](https://dartpad.dev)**试一试一些代码示例。******

# ******类别和对象******

******还记得我们在旅程开始时讨论过的[内置数据类型](https://medium.com/@constanting/fluttering-dart-9a3e74b0d9c5)吗？类允许我们定义自己的数据类型！这样我们就可以对程序中需要使用的对象进行建模。******

********类**是用户定义的数据类型，在示例中，到目前为止我们已经定义了一些类，最令人难忘的可能是 Cat 类。******

****在 Dart 中，每个对象都是一个类的实例，所有的类都是从**对象**派生出来的。Dart 也有一个基于 **mixin 的继承**，这有助于减少多重继承。这种继承类型允许重用多个类体，并且只存在一个超类。****

****类定义**成员**:函数和数据(**方法**和**实例变量**)。**在对象上调用**方法就是调用方法的行为。公共方法可以访问该对象的成员。****

****点运算符`**.**`用于引用变量或方法。****

****我们可以使用一个定义的**类**的**构造函数**来创建一个**对象**。****

****构造函数名可以是类名`ClassName`或`ClassName.identifier`。例如，我们将使用`Cat()`或`Cat.copyCat()`构造函数创建一个`Cat`。****

****构造函数可以有参数来提供初始化新对象所需的值，有几种类型:****

*   ******默认** —当我们不声明构造函数时，提供一个没有参数的默认构造函数，调用超类中的无参数构造函数；请注意，如果您声明了一个构造函数，将不会有默认的构造函数，如果您扩展了一个类，它的构造函数不会被继承；****

```
**class **Cat** {
  DateTime birthday; // default
  // it's here
  // even if
  // you can't see it
}**
```

*   ******命名为**——当我们需要为一个类实现多个构造函数时使用，或者提供额外的清晰性；****

```
**class Cat {
  DateTime birthday; // named
  **Cat.baby()** {
    birthday = DateTime.now();
  }
}**
```

*   ******重定向** —当我们只想重定向同一个类中的某个构造函数时；它的主体是空的，构造函数调用出现在`:`之后；****

```
**class Cat {
  DateTime birthday;

  // main cosntructor
  Cat(this.birthday); // delegating to main constructor
  **Cat.withBirthday(DateTime birthday) : this(birthday);**
}**
```

*   ******常量** —当我们需要永远不变的对象时使用；当调用这样的构造函数时，我们应该使用关键字`const`，否则我们不会创建常量；****

```
**class CatTreat {
  static final CatTreat catTreat = const CatTreat(1);

  final num quantity; // constant
  **const CatTreat(this.quantity);**
}**
```

*   ******工厂** —当实现一个不总是创建其类的新实例的构造函数时；工厂构造函数可能返回一个缓存的实例，进行对象池，或者返回一个子类型的实例；工厂构造者没有访问`this`的权限。****

```
**import 'dart:math';class Cat extends Pet {
  DateTime birthday;
  Cat(this.birthday);
  // delegating to main constructor
  Cat.withBirthday(DateTime birthday) : this(birthday);
}
class Dog extends Pet {
  DateTime birthday;
  Dog(this.birthday);
  // delegating to main constructor
  Dog.withBirthday(DateTime birthday) : this(birthday);
}
// factory
class **Pet** {
  Pet();
  **factory** Pet.withBirthday(DateTime birthday) {
    bool isCat = Random.secure().nextBool();
    return isCat?Cats(birthday):Dog(birthday);
  }
}**
```

****上面的 Pet factory 构造函数会随机返回一只猫或一只狗(Pet 的子类)。****

## ******可调用类******

****Dart 类也可以表现得像函数一样(它们可以被调用、接受参数并返回某些内容)。****

****为了实现这一点，我们必须在类中定义`call()`方法。****

```
**class Cat {
  DateTime birthday; Cat(this.birthday); String call() {
    print('Meow!');
  }
}
void main() {
  var cat = Cat(DateTime.now());
  cat(); 
  // prints
  // Meow!
}**
```

## ****发电机****

****当我们需要延迟产生一系列值时，就会用到生成器。Dart 支持两种类型的生成器函数:****

*   ****返回可迭代对象的同步生成器****
*   ****以及返回流对象的异步生成器****

****为了实现**同步**生成器，我们将函数体标记为`sync*`，并使用`yield`语句返回值:****

```
**Iterable<Cat> kittens(int toSpawn) sync* {
  int kittenIndex = 0;
  while(kittenIndex < n) {
    kittenIndex++;
    yield Cat.baby();
  }
}**
```

****为了实现一个**异步**生成器，我们将函数体标记为`async*`，并使用`yield`语句返回值:****

```
**Stream<Cat> kittens(int toSpawn) async* {
  int kittenIndex = 0;
  while(kittenIndex < n) {
    kittenIndex++;
    yield Cat.baby();
  }
}**
```

****如果我们使用递归调用，可以通过使用`yield*`来提高性能:****

```
**Iterable<Cat> kittens(int toSpawn) sync* {
  if(toSpawn > 0) {
    yield Cat.baby;
    yield* kittens(toSpawn - 1);
  }
}**
```

## ****变量****

****有两种风格:**实例**和**类**变量。****

****默认情况下，所有未初始化的变量都有值`null`。此外，所有非 final 的变量都会生成一个隐式的 getter 和 setter。最后一个不会生成 setter。****

****默认情况下，变量是实例变量。如果在声明时初始化(而不是在构造函数或方法中)，它们的值在创建实例时设置，这是在构造函数及其初始值设定项列表执行之前。****

****为了创建一个类变量，我们将使用`static`关键字。这些对于类范围的状态和常量很有用。它们在被使用之前不会被初始化。****

## ****方法****

****方法是为对象提供行为的函数。****

****与变量的情况一样，这里也有两种风格:**实例**和**类**方法。****

****对象上的实例方法可以访问实例变量和`this`。****

****静态方法(类方法)不对实例进行操作，因此不能访问`this`。它们最好用作编译时常数(例如，作为参数传递给常数构造函数)。对于常见或广泛使用的实用程序和功能，我们应该使用顶级函数，而不是静态方法。****

# ******封装******

****Dart 不包含用于限制访问的关键字，像 Java 中使用的`public`、`protected`或`private`。封装发生在库级，而不是类级。****

****有一个简单的规则:任何以下划线`_`开头的标识符(类、类成员、顶级函数或变量)对其库都是私有的。****

# ****继承和构成****

******继承**允许将一个类扩展到该类的一个特殊版本。如前所述，所有的类都继承自对象类型，只需声明一个类，我们就扩展了对象类型。Dart 允许单个直接继承，并对 **mixins** 有特殊支持，可用于扩展类功能，无需直接继承，模拟多个继承，重用代码。这就是**构图**的实现方式。****

****混合是在多个类层次结构中重用类代码的一种方式。要使用 mixin，使用关键字`with`后跟一个或多个 mixin 名称。要指定只有某些类型可以使用 mixin——例如，因此您的 mixin 可以调用它没有定义的方法——使用`on`来指定所需的超类。****

****没有`final class`，所以一个类总是可以被扩展的。****

# ****抽象****

******抽象**是我们定义一个类及其本质特征的过程，把实现留给它的子类。****

****为了声明一个抽象类，我们使用了`abstract`关键字。这些类不能被实例化，但对定义接口很有用。抽象类可以有抽象方法。****

****没有接口关键字。它的工作方式是每个声明的类定义一个**隐式接口**，包含一个类和它实现的任何接口的所有实例成员。这意味着任何类都可以由其他人实现，而无需扩展它。****

****一个类可以通过使用`implements`关键字实现一个或多个接口。****

# ****多态性****

******多态性**通过继承实现，代表一个对象复制另一个对象行为的能力(int**int**或 double 也是一个 **num** )。****

****我们可以使用`extends`创建一个**子类**和`super`来引用**超类**。****

****子类通常覆盖实例方法、getters 和 setters。我们可以使用`@override`注释来表明我们正在覆盖一个成员。****

****Dart 不允许**过载**。为了克服这一点，我们可以使用灵活的参数定义(**可选**和**位置**)。****

****总的来说，Dart 提供了我们使用 OOP 范例所需的所有功能。****

****在 [**飞舞的 Dart**](https://medium.com/tag/fluttering-dart/archive) 系列的下一部分，我们将深入研究[的未来并隔离](https://medium.com/@constanting/fluttering-dart-futures-and-isolates-6b4bce6d804b)以发现如何克服 Dart 的单线程缺点。****

****[](https://medium.com/@constanting/fluttering-dart-futures-and-isolates-6b4bce6d804b) [## 飞舞的飞镖:未来与孤立

### 如何克服单线程的缺点

medium.com](https://medium.com/@constanting/fluttering-dart-futures-and-isolates-6b4bce6d804b) 

就这些！****