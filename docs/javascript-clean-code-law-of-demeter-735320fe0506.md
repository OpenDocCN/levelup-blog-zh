# JavaScript 干净代码——德米特里定律

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-law-of-demeter-735320fe0506>

![](img/77681a094ba2bb2db16b575c80b38a48.png)

卡莱布·伍兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

德米特里定律指出，我们应该尽可能隐藏代码的实现细节。这确保了我们的代码之间的松散耦合。

在本文中，我们将看看如何将德米特法则应用到我们的代码中。

# 为什么我们需要松耦合？

松耦合意味着当一段代码发生变化时，我们不必担心要修改很多代码。

每个单元对其他单元只有有限的了解，因此只引用了其他单元的一小部分。

他们只和直系朋友说话，这样就不会和其他不相关的部分说话了。

所以这意味着如果我们有两个类`A`和`B`，那么`A`只引用必须被引用的`B`的方法。其他成员和其他实现细节都是私有的。

# 我们如何应用得墨忒耳定律？

我们可以将 Demeter 定律应用到我们的 JavaScript 代码中，尽可能地引用一些类和它们的成员。

下面是相互引用过多的类的一个例子:

```
class PostalCode {
  constructor(postalCode) {
    this.postalCode = postalCode;
  } setPostalCode(postalCode) {
    this.postalCode = postalCode;
  }
}class Address {
  constructor(streetName) {
    this.streetName = streetName;
  } getPostalCode() {
    return this.postalCode;
  } setPostalCode(postalCode) {
    this.postalCode = new PostalCode(postalCode);
  }
}class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  } setAddress(address) {
    this.address = new Address(address);
  } getAddress() {
    return this.address;
  }
}
```

我们有引用`PostalCode`的`Address`类和引用`Address`和`Occupation`的`Person`类。

如果`Address`和`PostalCode`中的任何一个发生变化，那么我们必须改变`Person`和`Address`类。

此外，我们必须通过编写以下代码来设置一个`Person`的邮政编码:

```
person.getAddress().getPostalCode().setPostalCode('12345');
```

这需要大量的链接，包括返回不同类的实例。如果这些方法中的任何一个发生变化，那么整个链都必须重写。

我们应该做的是将所有的引用合并到一个方法中，如下所示:

```
class PostalCode {
  constructor(postalCode) {
    this.postalCode = postalCode;
  } setPostalCode(postalCode) {
    this.postalCode = postalCode;
  }
}class Address {
  constructor(streetName) {
    this.streetName = streetName;
  } getPostalCode() {
    return this.postalCode;
  } setPostalCode(postalCode) {
    this.postalCode = new PostalCode(postalCode);
  }
}class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  } setAddress(address) {
    this.address = new Address(address);
  } getAddress() {
    return this.address;
  } getPostalCode() {
    return this.postalCode;
  } setPostalCode(postalCode) {
    this.postalCode = new PostalCode(postalCode);
  }
}
```

然后，如果`PostalCode`类发生变化，我们只需更新`Person`类，而不是在`PostalCode`类发生变化时更新整个调用链来获取和设置邮政编码。

关键是，我们必须了解整个系统，才能有所作为。

`PostalCode`不必连接到`Address`，因为它们可以单独改变。

如果我们将它们耦合在一起，那么我们必须在改变`PostalCode`之前了解`Address`。

上面的例子显示了可以避免并且应该避免的耦合。

![](img/344f00b114728f1670d2ffed0fa8381d.png)

[科里·布特莱特](https://unsplash.com/@coryb?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 立面图案

我们还可以使用 facade 模式来隐藏系统的复杂性，这样我们就不必了解它们。

例如，我们可以将多个类隐藏在 facade 类后面，然后使用 facade 类与隐藏在 facade 类后面的多个类间接交互，如下所示:

```
class ClassA {}class ClassB {}class ClassC {}class Facade {
  constructor() {
    this.a = new ClassA();
    this.b = new ClassB();
    this.c = new ClassC();
  }
}class Foo {
  constructor() {
    this.facade = new Facade();
  }}
```

在上面的例子中，`Foo`类不知道`Facade`类后面有什么。`Facade`类保存了`ClassA`、`ClassB`和`ClassC`的实例。

它为由`ClassA`、`ClassB`和`ClassC`组成的复杂系统提供了一个简单的接口。

当`Facace`后面的任何一个类改变时，我们只需要改变`Facade`类。

它是我们想用这些类做的所有事情的一个切入点。我们用一个统一的界面来处理所有的引用，而不是单独引用它们并创建一堆引用。

这满足了 Demeter 定律，因为我们只访问`Facade`类来处理`ClassA`、`ClassB`和`ClassC`的任何事情。我们不必知道它们的底层实现。

这使得软件易于使用、理解和测试，因为我们只需要使用和测试`Facade`来与所有底层的类进行交互。

它消除了引用复杂系统的多个部分的需要，因为`Facade`类提供了我们所需要的一切。

如果 facade 类下面的代码设计得不好，我们还可以用一个设计良好的 API 来包装它，帮助使用 facade 类的人以一种简单的方式使用它。

最重要的是，消除了紧密耦合，因为除了 facade 类之外，没有任何东西引用它下面的复杂代码。

# 结论

Demeter 法则就是尽可能地将实现隐藏在代码之外，这样他们就不必引用代码的不同部分来完成某件事情。

我们应该只创建与密切相关的类对话的类，而不是与所有东西对话的类。

当我们需要更改代码时，与所有东西交谈会产生一堆很难弄清楚的引用。

实现德米特里定律的一个好方法是使用 Facade 模式。该模式声明我们有一个 facade 类，作为复杂系统中其他类的入口点。

它用来提供一个易于使用的 API，它隐藏了底层的实现。因此，如果我们确实需要更改底层代码，我们只需更新 facade 类，而不是所有引用底层实现代码的内容。