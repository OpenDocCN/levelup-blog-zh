# JavaScript 设计模式——责任链、单例和轻量级模式

> 原文：<https://levelup.gitconnected.com/javascript-design-patterns-chain-of-responsibility-singleton-and-flyweight-patterns-42e1304668da>

![](img/8b7da8b907116726ee9337ffc648e2bb.png)

Sven Scheuermeier 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

设计模式是任何好软件的基础。JavaScript 程序也不例外。

在本文中，我们将研究责任链、单例模式和轻量级模式。

# 责任链模式

责任链类似于观察者模式，只是它将通知发送给一个对象，然后该对象将通知发送给另一个对象，依此类推。

在观察者模式中，通知同时发送给所有的观察者。

例如，如果我们向一个对象发送一些东西，然后这个对象获取这些东西，做一些事情，然后发送给另一个对象，等等，那么就实现了责任链模式。

我们可以按如下方式实现:

```
const backend = {
  receive(data) {
    // do something with data
  }
}const middleLayer = {
  notify(data) {
    backend.receive(data);
  }
}const frontEnd = {
  notify(data) {
    middleLayer.notify(data);
  }
}
```

在上面的代码中，我们有`frontEnd`，它调用了`middleLayer`对象的`notify`方法，后者又调用了`backend`的`receive`方法。

这样，我们可以用一种方法无缝地在它们之间传递数据。

只要我们不公开任何其他在两个对象之间进行通信的方法，我们就有一种干净的方式在`frontEnd`和`middleLayer`和`backEnd`之间发送数据。

# 一个

在单例模式中，我们只实例化一个对象的实例。

在 JavaScript 中，我们可以通过创建一个对象文字来创建带有一个实例的对象。

我们可以这样写:

```
const obj = {
  foo: 1,
  bar: 'baz'
}
```

一个对象文字只是一组键值对，其中的值也可以是其他对象。

如果我们使用类，我们也可以写:

```
class Foo {
  constructor() {
    if (!Foo.instance) {
      Foo.instance = {
        foo: 1,
        bar: 2
      }
    }
    return Foo.instance;
  }
}
```

我们将创建的实例分配给`Foo`的`instance`属性。

然后，当我们创建构造函数时，我们检查`instance`属性，看看是否为它创建了任何东西。

如果没有给它赋值，我们就给它赋值一个对象。

然后我们返回`Foo.instance`以便获得实例。

现在，如果我们创建 2 个`Foo`实例:

```
const foo = new Foo();
const bar = new Foo();
```

然后，我们可以通过编写以下代码来查看它们是否是同一个实例:

```
console.log(foo === bar);
```

我们应该得到`true`,因为它们都引用同一个实例。

单件可以方便地创建由多段代码用来在一个中心位置存储数据的对象。

如果我们不希望在数据访问方式上有冲突，那么我们可以创建一个类或对象文字的单一实例来确保没有冲突问题。

![](img/7b514cd0b7718bed432aba925cbbe396.png)

[艾伦·柯](https://unsplash.com/@iyunmai?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 轻量级模式

flyweight 模式是我们限制对象创建的地方，

我们创建了一组小对象，每个小对象消耗更少的资源。

这些对象位于一个 flyweight 对象的后面，该对象用于与这些对象进行交互，

这些对象也与我们的代码交互。

这样，我们获得了大对象的好处，同时与更易于管理的小对象进行交互。

例如，我们可以如下实现它:

```
class Student {
 //..
}const studentIdentity = {
 getIdentity(){
   const student = new Student();
    //..
    return {
    //...
    }
  }
}const studentScore = {
 getScore(){
   const student = new Student();
    //..
    return {
    //...
    }
  }
}
```

我们有一个代表学生数据的`Student`类。

然后在`studentIdentity`和`studentScore`对象中，我们有方法分别得到他或她的身份或分数。

这样我们就不用把所有的方法都放到`Student`类里了。

相反，我们在它之外有一些更小的方法，可以用来处理特定种类的学生数据。

# 结论

我们可以使用单例模式来创建一次性对象。

我们既可以创建只有一个实例的类实例，也可以创建有对象文字的类实例。

在 flyweight 模式中，我们创建较小的对象来处理较大的对象，从而降低复杂性。

责任链模式让我们以连续的方式将数据从一个对象发送到另一个对象。