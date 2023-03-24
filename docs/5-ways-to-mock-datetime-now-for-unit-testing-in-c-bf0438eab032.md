# 模仿日期时间的 5 种方法。现在用 C#进行单元测试

> 原文：<https://levelup.gitconnected.com/5-ways-to-mock-datetime-now-for-unit-testing-in-c-bf0438eab032>

## 各有利弊。

![](img/48102c9300f88e8405f3b40775255bfc.png)

照片由[卢克·切瑟](https://unsplash.com/@lukechesser?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

从`DateTime.Now`属性中抽象出应用程序逻辑是开发人员进行单元测试的常见任务。

当`DateTime`被硬编码成逻辑时，单元测试将不可靠。

在下面的示例中，单元测试只有在非常快的机器上执行时才能通过:

应用逻辑中使用的`DateTime.Now`实例通常与测试用例断言中使用的`DateTime.Now`具有不同的毫秒/节拍数。

至少有 5 种方法可以解决单元测试中的`DateTime.Now`问题，并使它们可预测地运行。

# 1.IDateTimeProvider 接口

最常见的方法之一是引入应用程序逻辑将使用的接口，而不是直接使用`DateTime.Now`属性。

## 优点:

*   `IDateTimeProvider`依赖关系是显式的。要了解一个特定的类是否能与`IDateTimeProvider`一起工作，开发人员只需要看看这个类的构造函数。没有必要分析类逻辑的其余部分。
*   拥有一个返回当前`DateTime`的方法比拥有一个属性更自然。通常，当 C#开发人员多次调用同一个属性时，他们期望获得相同的值。

## 缺点:

*   所有代码库中的许多类构造函数在列表中都有一个额外的`IDateTimeProvider`参数，这会污染代码。
*   有必要在 DI 容器中注册接口和实现。
*   没有什么可以阻止开发人员直接使用`DateTime`类型，绕过`IDateTimeProvider`实现。

# 2.单一 DateTimeProvider 类

前面的例子可以简化。接口`IDateTimeProvider`及其两个实现可以合并成一个`DateTimeProvider`类。

## 优点:

*   `DateTimeProvider`是一个显式依赖，因为它被注入到构造函数中。
*   解决方案很简单，因为它只有一个类。

## 缺点:

*   同样，没有什么可以阻止开发人员直接使用`DateTime`类型。
*   需要在 DI 容器中进行额外注册。

# 3.周围环境模式

有一种方法可以使用环境上下文模式实现为`DateTime`提供一个全局访问点。

## 优点:

*   使用`DateTimeProvider`与使用`DateTime`类非常相似。
*   不需要做 DI 注册。

## 缺点:

*   因为`DateTimeProvider`的实现是基于一个共享的静态属性，所以测试不能并行运行。
*   `DateTimeProvider`的依赖是隐式的，因为不需要将它注入到构造函数中来使用它。开发人员应该检查该类的整个实现，看它是否在那里被使用。
*   同样，没有什么可以阻止开发人员直接使用`DateTime`类型。

# 4.单行包装

下面的实现为`DateTime.Now`属性提供了一个简单的包装器。

## 优点:

*   `DateTimeProvider`是一个单行实现。
*   `DateTimeProvider`依赖关系是显式的。

## 缺点:

*   属性`Now`必须是虚拟的。否则，Moq 框架将无法在模仿类型上创建包装。我们用仅用于单元测试目的的东西污染了应用程序逻辑。
*   需要对`DateTimeProvider`进行 DI 注册。
*   同样，没有什么可以阻止开发人员直接使用`DateTime`类型。

# 5.姿势库

最后一种方法是保持应用程序逻辑不变，使用 [Pose](https://github.com/tonerdo/pose) 库，它允许您用自己的委托替换任何属性或方法。

## 优点:

*   无需更改应用程序逻辑。
*   开发者唯一的选择就是`DateTime`类型。

## 缺点:

*   嘲笑你不拥有的类型通常是一种不好的做法。
*   第三方库可能包含错误，需要额外的维护工作等。
*   `DateTime`类型将在应用程序逻辑中隐式使用。

我们已经看到了实现相同目标的 5 种不同方法，各有利弊。

感谢阅读！

考虑订阅我的电报频道 [**软件开发日报**](https://t.me/sd_daily) 从我这里获取更多内容。

## 我的其他文章

[](/static-classes-and-methods-are-they-terrible-c9c611a921b3) [## 静态类和方法——它们很糟糕吗？

### 从设计角度探索 C#静态类和方法。

levelup.gitconnected.com](/static-classes-and-methods-are-they-terrible-c9c611a921b3) [](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [## 面向技术领导者和资深人士的 50 个软件工程最佳实践

### 最佳工程师的最佳实践。

levelup.gitconnected.com](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [](/5-asp-net-core-open-source-projects-to-gain-practical-knowledge-24fbf9164230) [## 5 个 ASP.NET 核心开源项目，获取实用知识

### 在实践中学习。

levelup.gitconnected.com](/5-asp-net-core-open-source-projects-to-gain-practical-knowledge-24fbf9164230)