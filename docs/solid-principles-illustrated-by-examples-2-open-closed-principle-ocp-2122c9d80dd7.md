# 举例说明坚实的原则:2。开闭原则(OCP)

> 原文：<https://levelup.gitconnected.com/solid-principles-illustrated-by-examples-2-open-closed-principle-ocp-2122c9d80dd7>

结账单责任原则[这里](https://medium.com/@manojchemate/solid-principles-illustrated-by-examples-1-single-responsibility-principle-srp-9eeb2849ceec)如果你还没有，

**开合原理:**

> 模块应该对扩展开放，对修改关闭。
> 
> 非正式地说，现有的代码不应该因为新的需求而有任何改变。

对于任何系统，根据业务需求，总会有更多的变化需要集成到现有的代码库中。如果一个模块不遵循 OCP，那么一个模块中的变化可能导致其他模块中的级联故障，为了避免这种情况

以这样一种方式设计你的类、接口和方法，

> 开放扩展:未来的需求应该通过添加新的类、接口或函数来满足，这些新的类、接口或函数可以扩展或实现现有的类、接口或函数。
> 
> 对修改关闭:不修改现有的类、接口或函数。

![](img/1593e0d121c474697ab261e0a8dd9f30.png)

照片由[蒂姆·莫斯霍尔德](https://unsplash.com/@timmossholder?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**怎么做？**

> 抽象是关键。

首先，让我们看一个违反 OCP 教的流行例子

有一个 *ShapeDrawer* 类可以绘制所有支持的形状类型。

现在，在未来，如果我们想要支持三角形形状，我们必须添加一个*三角形*类，但是我们需要修改现有的*形状类型*枚举和*形状抽屉*类的 *drawAllShapes()* 方法，这违反了 OCP 原则。 *ShapeDrawer* 没有关闭进行修改。

让我们看看如何使用抽象来避免它，

1.  在上面的代码中，为了支持三角形，我们只需要用 *draw()* 方法添加*三角形*类，而不需要修改 *ShapeDrawer* 类，因此它符合 OCP。
2.  *形状*界面打开进行扩展，*形状抽屉*关闭进行修改绘制新的形状。
3.  这并不意味着它对所有未来的需求都是封闭的，但是如果你能找出系统的动态组件，并对它们进行适当的抽象，那么这个系统将是僵化的。

OCP，

1.  确保未来的变化不会导致意外的副作用。
2.  确保代码的灵活性、可重用性和可维护性。

但是我们应该如何使用继承和多态这样的抽象结构来符合 OCP 呢？有什么原则吗？

是的，接下来里斯科夫替代原理有助于它

[](https://medium.com/@manojchemate/solid-principles-illustrated-by-examples-3-liskov-substitution-principle-lsp-b182c2848a9c) [## 举例说明坚实的原则:3。利斯科夫替代原理

### 通过实例了解利斯科夫替代原理(LSP)！

medium.com](https://medium.com/@manojchemate/solid-principles-illustrated-by-examples-3-liskov-substitution-principle-lsp-b182c2848a9c)