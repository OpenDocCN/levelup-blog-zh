# 停止使用 Setters

> 原文：<https://levelup.gitconnected.com/stop-using-setters-785670f4bf8>

Setters-pattern 是众多编程语言中最流行的一种。工程师们已经用了这么长时间，这种方法已经变得很明显了。甚至 IDE 现在只需点击几个热键就能为你生成 setters。我认为在任何情况下都应该避免使用 setters。该条目包含 Java 上的代码片段，但是这些规则可以应用于任何其他语言。

![](img/6c6ccb7a5d54c97084f963b19d8f7780.png)

图片来自 [PublicDomainPictures](https://pixabay.com/users/PublicDomainPictures-14/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=73326) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=73326)

我打赌每个人都见过类似的东西。

[Java 中 Setters 用法示例](https://gist.github.com/SimonHarmonicMinor/26fcadcc0670f1c8efe9639d8922ba46)

这种方法看起来很优雅，但是让我们考虑一下。首先，我们实例化一个空对象，里面什么都没有。之后，我们一步一步地分配所需的值。如果我们忘记指定名字或年龄会怎样？这意味着未来的执行将处理一个不完整的对象。不仅如此，setters 的存在意味着对象是可变的，可以被任何人在任何时候修改。让我们看另一个例子。

[将一个可变实体传递给另一个方法](https://gist.github.com/SimonHarmonicMinor/0984d13e9569a8cad788125885970077)

我们通过调用`findAllSubordinates`并不期望`manager`上有任何突变，但是我们怎么知道呢？在这种情况下，`humanResources`可以只是一个接口。我们不知道它到底是如何工作的。所以，我们不知道`manager`的未来状态，因为它的公共 API 包含可以被调用的变异方法。

也许这一论点还不够有说服力。让我们看另一个例子。假设我们有一张员工及其支付率的地图。

[意外的地图损坏](https://gist.github.com/SimonHarmonicMinor/3fe35f06c4468b3c0e92df84c49b16ab)

输出是

```
Map: {[name=Jack, age=23]=35, [name=Julia, age=35]=65}
Corrupted map: {[name=Jack, age=55]=35, [name=Julia, age=35]=65}
Jack's rate: null
Julia's rate: 65
```

发生这种情况是因为新杰克的散列码不等于地图上包含的散列码。因此，这些键被认为是不同的。有一个黑客可以修复这种情况，但它值得另一个故事。

> 编辑:`Person`必须实现`equals`和`hashCode`方法。否则，Jack 的 rate 应该是 35，因为默认的`hashCode`实现返回相同的值，即使您更改了一些字段值。感谢 [tw-abhi](https://github.com/tw-abhi) 指点！

顺便说一下，我也见过有人在 Spring 这样的框架中使用带有依赖注入的 setters。看看这个。

[带 setter 的 Spring 依赖注入](https://gist.github.com/SimonHarmonicMinor/1e93c249e70ea7202c81ea59899eb3a1)

没有人预料到`ParentService`的依赖性会突然消失。但是正如你所看到的，即使没有[反射 API](https://www.baeldung.com/java-reflection) ，这也很容易做到。我的观点是，如果你的类有可以改变其状态的方法，这可能会导致不愉快的后果。

不管怎样，我们能做些什么呢？嗯，最简单的事情就是将所有需要的值传递给构造函数。

[用构造函数实例化不可变对象](https://gist.github.com/SimonHarmonicMinor/fea86121a61e8050bfefb74a26b17857)

你看到现在所有属性都是`final`了吗？这很重要。这意味着值只能在构造函数范围内赋值一次。不变性向我们保证对象的恒定状态。我们可以把它传递给任何方法。我们甚至可以从多个线程安全地访问它。

"但是如果需要缺省值呢？"。好吧，Java 不支持默认参数值，但是我们可以定义多个构造函数，省略一些参数。或者我们可以使用[构建器模式](https://refactoring.guru/design-patterns/builder)。我们可以自己实现，但是我更喜欢使用 [Lombok](https://projectlombok.org/) 库。

[使用构建器以替换默认参数值](https://gist.github.com/SimonHarmonicMinor/267897a0044cd6e75e7f28fa593057e6)

现在很明显哪个参数被赋值了。这种方法可能看起来类似于 setters 的用法，但是有很大的不同。在`build`方法调用之前，我们不会实例化一个对象。但是一旦它被创造了，它是不可改变的。

有时候开发人员用构造器来代替大的构造器。这似乎很有用，因为它使代码更干净、更直观，但是您应该注意这种情况。让我们看一看。

[用 builder 代替大型构造器](https://gist.github.com/SimonHarmonicMinor/f61680f4f5222d7ab1609234e255f14d)

问题是，第二种方法明显更容易理解，但也很容易忘记一些论点。这种情况经常发生，尤其是当一个类用新参数增强时。有时候可以接受，有时候不行。不要忘记 Builder-pattern 的最初目的是实现默认参数值，而不是替换大型构造函数。如果你的类有很多依赖项，并且所有的依赖项都是必需的，那么最好把类分成几个。

# 结论

我希望我使您相信 setters 的使用不是一个好的实践。有些情况下我们必须使用 setters。例如，如果一个库为我们提供了这样的 API。但是不要在你的项目中使用它们。不值得。感谢您的阅读！

# 资源

*   [反射 API](https://www.baeldung.com/java-reflection)
*   [建造者模式](https://refactoring.guru/design-patterns/builder)
*   [龙目岛](https://projectlombok.org/)