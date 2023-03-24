# 类型脚本 Getters 和 Setters 在 Angular

> 原文：<https://levelup.gitconnected.com/advantages-of-typescript-getters-and-setters-in-angular-668f4639ad0c>

![](img/c57242ab50de5dad85612bf9e1d98ca1.png)

我用 Angular 开发 web 应用程序已经有很长一段时间了，在这段时间里，我发现了很多东西，我觉得很少有人会去探索，或者很多人会陷入同样的困境。在本文中，我将讨论 TypeScript 和 Angular 提供的一个这样的功能，通过使用它，我们可以进行一些有效和干净的编码。

# 它们是什么？

Getters 和 Setters 是几乎所有编程语言中众所周知的设计模式。他们让你在常规变量上创造惊人的高级计算。它们可用于对数据进行预处理。TypeScript 中的每个变量都可以有它的 setter 和 getter 函数。虽然这些 getters 和 setters 看起来像任何其他函数声明，但它们可以用作普通变量来赋值、执行操作或在模板中显示数据。

# 如何实现它们？

变量声明

# 使用变量

正如您在上面的例子中看到的，为任何变量创建 setter 和 getter 以及使用它们都非常简单。货币值在使用前使用转换度量进行转换。因此，在使用货币值之前，我们不需要总是使用转换器。

# 与 Angular 一起使用

你不需要使用 ngOnChanges，它由所有的变量变化调用，并检查需要操作的变量，然后进行操作。相反，您可以为变量使用 setter，在使用它们之前您必须执行处理。

**在**之前

**在**之后

> 它可用于复杂验证后显示的模板。我们可以有一个 getter 函数来执行组件中的操作，并使用变量来代替复杂的验证。这使得代码更加有组织和干净

所以，这个简单的 typescript getter 和 setter 函数对我们非常有益。但也有一些缺点或不足。

## 在输入装饰中使用 Getter 函数的 CONS:

如果我们在模板中使用它，它将在每个变更检测周期中被调用。最坏的情况是，当使用变更检测默认方法而不是 OnPush 时，它会被重复调用。

我还建议，只有当您希望在哑组件中进行一些轻量级操作，仅仅是为了呈现表(下面提到的只是使用 setter ),而不是进行更多的计算，如进行 API 调用、订阅等时，我们才应该在输入端使用这些方法。因此，在使用这些方法之前，请确保您非常清楚它的用途以及它对应用程序的影响。

如果你遇到这些函数的任何其他令人敬畏的用法，请告诉我。

希望，这篇文章有所帮助。快乐编码…！

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)