# (功能性)JavaScript 中反模式的 OOP 最佳实践

> 原文：<https://levelup.gitconnected.com/oop-best-practices-that-are-anti-patterns-in-functional-javascript-61ee1af35452>

在面向对象编程(OOP)和设计模式中，什么被认为是好的实践，但在 JavaScript 中被认为是反模式(至少是 JS 的函数式编程(FP)风格)？

以下是要点:

*   优点:工厂模式
*   坏处:构造函数
*   `class Foo extends Bar`丑陋的

![](img/1164beee87f7be01e575145e174d9946.png)

看着这个湖有助于我理清思绪。

对于经典的 OOP 语言(Java、C#、PHP ),事情看起来非常相似。C#可能更好，但是我对它没有什么经验，所以我将使用 Java 作为参考点。然而，所有语言中的(反)模式都是一样的。

免责声明:以上并不意味着遵循 OOP 范例和使用类的面向对象编程或语言是不好的。更多的是 JavaScript 使用了一种不同的范式，提供了更强的表现力，所以利用它是有好处的。

# 优点:工厂模式

事实上，在 OOP 中有两种工厂模式:

*   [工厂方法模式](https://en.wikipedia.org/wiki/Factory_method_pattern)
*   [抽象工厂模式](https://en.wikipedia.org/wiki/Abstract_factory_pattern)

两者都有意义，因为它们是用一种特定的语言来解决问题的。这里最大的一个问题是:你想编码对象的创建，但还不知道它们的`class`。看看提供的 Java 代码示例，有多复杂，需要多少样板文件。

在 JavaScript 中，这些限制都不存在:没有对象类(稍后会详细介绍`class`关键字)，不需要检查接口等等。任何函数都可以返回一个对象，因此您可以:

```
const createUser = ({ name }) => ({
  name,
  setName(name) {
    this.name = name;
    return this;
  }
});

const joe = createUser({ name: 'Joe Doe' });
```

上面的代码是一个简单返回对象的工厂函数。不需要类、静态方法和多态。阅读带有 ES2015 语法的[工厂函数，了解如何处理默认参数、类型推断和其他内容。](https://medium.com/javascript-scene/javascript-factory-functions-with-es6-4d224591a8b1)

换句话说:工厂模式在 OOP 语言中的广泛使用是有意义的，但在 JavaScript 中它只是一个返回对象文字的函数。太简单而不能称为设计模式；)

# 坏处:构造函数

所以 JavaScript 有一个构造函数，应该使用`new`操作符调用来调用*:*

```
function User(name) {
  this.name = name;
}

const joe = new User('Joe Doe');
console.log(joe instanceof User);
```

就像 Java 一样，差不多。如果你忘记使用`new`，构造函数将被调用为常规函数，在这种情况下`this`将被绑定到全局对象(即浏览器中的`window`)。像名字这样的变量被添加到全局范围，整个结构就分崩离析了(双关语)。这是一个主要的陷阱，也是许多错误的原因。

你可以探索构造函数的更多[特征，但是我同意](https://css-tricks.com/understanding-javascript-constructors/) [Kyle Simpson](https://github.com/getify) 有比构造函数、原型黑客和类更好的解决方案，比如 [OLOO 模式](https://stackoverflow.com/questions/29788181/kyle-simpsons-oloo-pattern-vs-prototype-design-pattern)。如果你真的需要有方法的对象，当然；)

长话短说:避免使用`new`关键字和构造函数。因此，`instanceof`可能也没有必要。使用上面描述的更强大的工厂功能。正如道格拉斯·克洛克福特所说:

> 如果一个特性有时是危险的，并且有更好的选择，那么总是使用更好的选择

在 OOP 语言中也不鼓励使用`new`操作符，以避免与特定的类及其实现耦合。工厂模式更受青睐，它支持依赖注入，但这是另一篇文章的内容。

# `class Foo extends Bar`丑陋的

ES2015 在 JavaScript 中引入了`class`和`extends`关键字。不，这并不意味着 JavaScript 已经有了类——它仍然在使用原型链。基本上，它是一个语法糖在构造器模式上，在幕后做了一些魔术。

事实上，这里最坏的冒犯者是关键字`extends`，因为它*鼓励*为代码共享扩展类。这是错误的动机。在设计中应该用`extends`来表达“*是一个“*的关系，但现实中很少出现这样的情况。这是 OOP 中最高形式的耦合。

广泛使用`extends`会导致深度嵌套的类继承层次结构。起初看起来并不坏，但是相信我，我曾经在一个 7 层的系统中工作过，我们一直在这个树中修复代码。固定在一个级别会导致更高或更低几个级别的回归，导致螺旋式下降。那只是[第七层地狱](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3)一类的问题。

这种反模式的另一个常用名称是乔·阿姆斯特朗创造的“大猩猩/香蕉问题”:

另一方面，`class`可能是有用的。很长一段时间，使用类是创建 React 组件的建议。注意你总是有`extends React.Component`——在代码中没有很深的继承层次，对吗？对吗？！我觉得 JS 里的[类关键字没有坏了](https://medium.com/javascript-scene/how-to-fix-the-es6-class-keyword-2d42bb3f4caf)，但是它自带问题，就像`new`关键字一样。正如一个类比所暗示的那样——最好同时避开这两者。

# 对象和类不是软件

OOP 爱好者通常倾向于关注类、对象、UML、图表——所有这些都很好，但不是核心。最重要的是:软件做什么。考虑到这一点，没有方法的对象是非常无用的，即使 Java(过去)强迫你[创建类来保存你的函数](http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html)——这是一个可怕的想法。

对于下一个 JavaScript 特性或项目，开始考虑如何用最简单的方式解决问题。尽量避免所有面向对象的仪式，把注意力集中在软件的功能上。