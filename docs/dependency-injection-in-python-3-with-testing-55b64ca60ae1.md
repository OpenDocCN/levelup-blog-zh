# Python 3 中的依赖注入与测试

> 原文：<https://levelup.gitconnected.com/dependency-injection-in-python-3-with-testing-55b64ca60ae1>

![](img/c7375ba32e6bde82f7bce2464b950b02.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

依赖反转是控制反转(IoC)的一种具体实现，是一种面向对象的软件设计原则，它通过将较低级别的类与较高级别的类解耦来创建不太脆弱的代码，并使编写测试更容易。它通过将对象作为参数传递给类的构造函数，而不是在类本身内部实例化新的对象，来完成这一壮举。本文并不打算支持依赖注入的优点——这已经是一个很好的主题[——而是展示一个 Python 中的例子，以及如何为使用依赖反转的代码编写单元测试。依赖*注入*是一种依赖倒置，通常通过采用依赖注入框架，创建一个容器对象来管理依赖。](https://medium.com/better-programming/the-6-benefits-of-dependency-injection-7802b207ec69)

在本文中，我们将:

*   创建一个没有依赖注入的格式化字符串消息的人为例子。
*   重构它以使用依赖倒置，并编写一个简单的单元测试。
*   再次重构它，使用[python-dependency-injector](https://github.com/ets-labs/python-dependency-injector)，这是一个流行的 DI 框架，用于在容器中创建依赖关系。

# 没有依赖注入的例子

无依赖性反转的示例

在上面例子的第 8 行，`MessageFormatter`依赖项被硬连接到`MessageWriter`类。这段代码运行得非常好，但是如果在未来的版本中,`MessageWriter`的实现被更改为接受构造函数参数，该怎么办呢？这将包括在所有类之间传递参数，这是没有必要的。此外，我们怎么可能为我们的`write`函数编写一些有意义的单元测试，它甚至不返回任何东西！在构造函数或方法中实例化一个对象是一种代码味道，说明这个类在实现中太死板了。

严格代码的一个长期后果是意大利面条式的代码，这是由于依赖关系被随意地连接在整个代码库中，使得几乎不可能理解谁拥有什么以及类的真实范围是什么。当您发现修改一个类需要更改多个类来添加同一个参数时，依赖反转很可能就是您的锦囊妙计中缺少的工具。

# 重构以包含依赖倒置

那么，这怎么解决呢？我们可以将`MessageFormatter`的一个实例传递给`MessageWriter`，而不是在`MessageWriter`的构造函数中创建`MessageFormatter`。

依赖倒置的例子

起初，这种差别是微妙的，但却是强大的。现在，如果我们想为`MessageWriter`编写单元测试，我们可以创建一个`MessageFormatter`的模拟实例，并将其作为参数传递给`MessageWriter`的构造函数。这个模拟允许我们替换依赖项，因为这不是我们单元测试所关心的。以下示例使用了 [pytest](https://docs.pytest.org/en/latest/) 和 [pytest-mock](https://github.com/pytest-dev/pytest-mock/) 。

依赖倒置的单元测试示例

这段代码开始变得更好看了。它不那么脆弱，并且有可能编写简洁而有意义的单元测试。不幸的是，这个例子有一个隐藏的问题。随着这个代码库的增长，更多的依赖会出现在`main()`函数中，导致它失控。最后，我们会发现我们一直在从程序的最高层向程序的最底层传递依赖关系。这是乏味的，并且会导致一些带有大量参数的非常粗糙的构造函数。幸运的是，仍然有一种更好的方法可以做到这一点，那就是依赖依赖注入。

# 重构以使用依赖注入

许多语言都有依赖注入框架，允许创建一个容器对象来管理依赖及其创建的细节。C#有 [Autofac](https://autofac.org/) 和 [Ninject](http://www.ninject.org/) ，C++有[【Boost】。DI](https://boost-experimental.github.io/di/) 和 Python 有[依赖注入器](https://github.com/ets-labs/python-dependency-injector)。所有这些框架的工作方式本质上都是一样的。首先创建一个容器类来注册您的依赖项(在依赖注入器的说法中，这些注册被称为提供者)，然后在您的各种类中，您调用容器对象来为您解析依赖项。

根据具体情况，有许多类型的提供程序可供使用。从依赖注入器文档中:

> **提供者** —基本提供者类。
> 
> **Callable** —在每次调用时调用包装的可调用程序的提供者。支持位置和关键字参数注入。
> 
> **工厂** —在每次调用时创建指定类的新实例的提供者。支持位置和关键字参数注入，以及属性注入。
> 
> **Singleton** —提供者在第一次调用时创建指定类的新实例，并在下一次调用时返回相同的实例。支持位置和关键字参数注入，以及属性注入。
> 
> **对象** —按“原样”返回所提供实例的提供者。
> 
> **ExternalDependency** —对于开发需要外部依赖的自给自足的库、模块和应用程序非常有用的提供者。
> 
> **配置** —帮助实现配置选项的后期静态绑定的提供者—先使用，后定义。

从我个人的经验来看，使用`Factory`和`Singleton`提供者可以解决大多数用例。下面演示了如何创建一个容器对象——下面恰当地命名为`Container`——然后是如何利用在容器中创建的提供者来解析`MessageFormatter`依赖关系。

依赖注入的例子

在第 27 行，我们使用`Factory`提供者来定义如何创建一个`MessageFormatter`对象。每次我们像在第 14 行那样调用`Container.message_formatter()`，我们都会得到一个新的`MessageFormatter`实例。前面演示的单元测试和最后一个例子一样。为了让上面的例子与我们的单元测试一起工作，我们必须在`MessageWriter`构造函数中实现一个 if 语句。虽然这并不理想，但它的缺点被依赖注入的优点掩盖了。

现在，这段代码的优势在于，通过能够替换具体的实现，它变得更加模块化；通过能够模拟更高级别的依赖关系，它变得更加可测试；通过没有过多的构造函数参数，它变得更加简洁。更高级别的依赖不再通过不直接使用依赖的类来编织。对于代码库的长期可维护性(和可读性)来说，没有什么比引用一个类中的对象更糟糕的了，该对象的存在只是为了传递给更低级别的依赖项。依赖注入器框架很小，简单，易于使用；几乎没有什么能让它成为任何 Python 项目的直接和有价值的组成部分。

# 结束语

希望这篇博文能提供一些信息，并向您展示如何在 Python 项目中包含依赖注入，重点是可测试性。对于这篇博文中包含的完整的、可运行的代码示例，请访问我的 GitHub 页面。

快乐编码！🧑🏻‍💻

[git hub](https://bit.ly/dbudwin-github)|[LinkedIn](https://bit.ly/dbudwin-linkedin)|[中](https://bit.ly/dbudwin-medium) | [Budw.in](https://bit.ly/budw_dot_in)