# 在 Node.js 中创建一个类实例会怎么样？

> 原文：<https://levelup.gitconnected.com/what-happens-when-you-create-an-class-instance-in-node-js-c4a2bb237bcb>

![](img/95c3c3f05024f383ae18a0f471fa775c.png)

从 JavaScript ES6 及更高版本开始，我们能够像在其他编程语言中那样编写类，因此，我们也可以使用 *new* 关键字以同样的方式创建实例。与此同时，我们得到了一些 OOP 特性，比如继承。

也就是说，JavaScript 要成为完全面向对象的语言还有很长的路要走——我们谈论的是普通的 JavaScript，所以 TypeScript 不在讨论范围之内。

现在，为了理解创建新实例时会发生什么。让我们考虑下面的例子:

```
class Animal{
  species;
  constructor(){}
}

class Dog extends Animal{
  name;
  constructor(){
    super();
  }
}

let max = new Dog();
```

让我们仔细看看上面的例子，想一想，当*新狗()*被调用时会发生什么？

首先，声明类属性，然后调用类的构造函数，并将必要的值存储在新创建的实例变量 *max* 中。然后，函数 *super()* 开始在继承线中上升到 *Animal* 类，并运行其构造函数，但不运行属性和方法。这就是*扩展*关键字的工作，确保父类的所有属性和方法在子类中都是可访问的。

这只是它如何工作的概述。让我们尝试一下，以便更深入地了解发生了什么。首先，我们将把值哺乳动物分配给物种，并把它放在子类中:

```
class Animal{
  species;
  constructor(){
    this.species = 'mammal';
  }
}

class Dog extends Animal{
  name;
  constructor(){
    super();
    console.log(this.species);
  }
}

let max = new Dog();
```

如果我们运行这个程序，你会发现它会把‘哺乳动物’这个词打印到控制台上。这正是我们想要的。让我们看看发生了什么:

1.  声明了狗属性名。
2.  Dog 构造函数被初始化，它调用声明父类 *Animal* 的属性物种的 *super()* ，并初始化父构造函数。
3.  父构造函数将值哺乳动物赋给物种。
4.  Dog 构造函数继续并在控制台记录该值。

如果我们在调用 super 之前先控制日志种类，会发生什么情况:

```
constructor(){
    console.log(this.species);
    super();
  }
```

当你运行程序时，你会得到一个引用错误，告诉你在使用*这个*关键字或者从派生构造函数返回之前，你需要调用 *super()* 方法。这是有意义的，因为 *super()* 的行为就好像你正在启动一个实例，但是不管它的父类是什么。超级功能:

1.  启动一个新的父实例，例如， *new Animal()* 。
2.  用保留当前实例的关键字附加这个实例。

好吧，让我们更疯狂一点。我们将在 Dog 类中给属性名赋值“max ”,然后在 Animal 类中给它赋值:

```
class Animal{
  species;
  constructor(){
    this.species = 'mammal';
    console.log("Name", this.name);
  }
}

class Dog extends Animal{
  name = 'max';
  constructor(){
    super();
    console.log("Species", this.species);
  }
}

let max = new Dog();
```

在运行上面的代码之前，您能猜到输出会是什么吗？

当你运行这个程序时，它不会中断。然而，虽然在 Dog 类中分配了 *name* 属性，但输出将是 *Name undefined* 。根据我们之前提到的行为，这将被认为是奇怪的，因为我们在类声明上赋值，这是 Node 中的一个新特性，只在现代浏览器上工作(以防它在您的机器上崩溃，然后您将知道为什么)。

为了理解发生了什么，我们需要使用 *console.dir()* 将*这个*输出到控制台:

```
constructor(){
  this.species = 'mammal';
  console.dir("Object", this); //this will show object details
  console.log("Name", this.name);
}
```

输出:

```
//output
'Object'
Name undefined
Dog { species: undefined, name: 'max' }
```

当您运行这个程序时，您会注意到两件事:

*   虽然您在 name 字段之前输出对象，但是首先它会显示 object，然后 Name 字段会显示，最后实例细节会显示在最后。
*   名称在 *this* 中被初始化，但是当你调用 name 时它是未定义的。

我们从中可以了解到的是 *console.dir* 一直等到继承树构建完成，然后输出它的字段，这意味着在这个阶段对象还没有完全构建好。我们可以通过看到 *this.name* 返回 undefined 来证明这一点。

这里的要点是，您不应该期望子类将值传递给其父类的构造函数。但是，您可以稍后在方法中或在新创建的实例中使用它:

```
let max = new Dog();
max.name;
```

Node.js 的 OOP 很年轻。它仍然缺少许多特性，包括在封装中非常重要的接口和访问修饰符。为了探索所有 Node 的新特性，我构建了一个 [npm 包](https://www.npmjs.com/package/oraios-queries)以达到其当前的极限，本文揭开了其中一个奇怪的部分。我打算一有机会就分享更多。