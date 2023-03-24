# 中的单元测试最佳实践。NET C#

> 原文：<https://levelup.gitconnected.com/unit-testing-best-practices-in-net-c-3117ea4010b9>

## 最佳实践

## 中单元测试的提示、技巧和最佳实践。NET C#使用 NUnit 和 Moq

![](img/8baa488f7100302bc32612bc95a23abb.png)

由 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[戴红帽](https://unsplash.com/@girlwithredhat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)的女孩拍摄， [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整

# 范围

在本文中，我们将重点介绍使用 [**NUnit**](https://nunit.org/) 和 [**Moq**](https://www.nuget.org/packages/Moq/) 库来覆盖我们的**库时需要遵循的一些重要提示和技巧。带有单元测试的应用程序。**

您不应该将本文作为单元测试的权威指南，因为这不是本文的目标或范围。然而，在这篇文章中，你会发现一些重要的**提示**、**技巧**和**最佳实践**来帮助你用你的**达到最佳效果。NET C#** 应用单元测试和覆盖。

![](img/dae42316c04548aad197e34b378e3bf1.png)[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 🔥订阅艾哈迈德的时事通讯🔥

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/a96685b0cf9542013ca6471ad6db590b.png)

**使用的框架**。由[阿尔方斯·莫拉莱斯](https://unsplash.com/@alfonsmc10?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄，由[艾哈迈德·塔雷克](https://medium.com/@eng_ahmed.tarek)调整

# 使用的框架

## 单元测试框架

![](img/f220d998f0614ade947e9d6ab5b1a844.png)

**努尼特**。由 [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整的图像

我们将使用 [**NUnit**](https://nunit.org/) 作为单元测试框架。

你可以在 [**这个链接**](https://docs.nunit.org/#user-documentation) 找到所有的 NUnit 官方文档。

还有，你可以在这个 [**文档页**](https://docs.nunit.org/articles/nunit/writing-tests/attributes.html) 具体找到 NUnit 提供的所有属性的文档。

## 模拟框架

![](img/1a0789226d211abc0f21f146c79a140c.png)

**起订量**。由 [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整的图像

我们将使用 [**Moq**](https://www.nuget.org/packages/Moq/) 作为嘲讽框架。Moq 是. NET 最流行和友好的嘲讽框架。

如果你想对如何开始使用 Moq 有所帮助，你可以在 [**这个链接**](https://github.com/Moq/moq4/wiki/Quickstart) 上找到快速入门指南。

你可以在 [**这个链接**](https://github.com/moq/moq4) 找到所有的 Moq 官方文档。

![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/5645dcb49db18c02c165e823f05710c1.png)

提示和暗示。照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄，由[艾哈迈德·塔雷克](https://medium.com/@eng_ahmed.tarek)调整

# 技巧和提示

在这一节中，我们将讨论一些在编写单元测试时有用的技巧和提示。

## 设置和拆除

当我们编写单元测试时，我们通常会在单元测试前后做一些准备和清理代码。

NUnit 为我们提供了一些用于这些准备和清理任务的属性。

[**OneTimeSetup**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/onetimesetup.html) :用于设置第一次测试前只执行一次的代码。

[**设置**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/setup.html) :用于设置每次测试前要执行的代码。

[](https://docs.nunit.org/articles/nunit/writing-tests/attributes/teardown.html)**:用于设置每次测试后要执行的代码。**

**[**OneTimeTearDown**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/onetimeteardown.html) :用于设置上次测试后只执行一次的代码。**

**如果我们有两个测试，执行顺序如下:**

1.  **测试#1 前的一次性设置**
2.  **测试#1 前的设置**
3.  **测试#1 后拆卸**
4.  **测试#2 前的设置**
5.  **测试#2 后拆卸**
6.  **测试#2 后一分钟下降**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **测试案例**

**当我们编写单元测试时，我们可能需要以输入和输出对的形式覆盖不止一个测试用例。**

**因此，我们可以使用 NUnit 提供的 [**TestCase**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcase.html) 属性，而不是为每个测试用例编写多个单独的测试。**

**可以以多种格式使用 [**测试用例**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcase.html) 属性。**

**例如，您可以这样使用它:**

**或者你可以这样使用它:**

**甚至在更多方面。你可以随时查阅官方文件。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **来自一个源的测试用例**

**有时我们需要在多个测试之间共享相同的测试用例。在这种情况下使用 [**TestCase**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcase.html) 属性是不够的。**

**因此，NUnit 为我们提供了[**test case source**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcasesource.html)属性，这样我们就可以将我们的案例设置到一个共享模块中，然后开始在不同的测试中使用它。**

**这是我们可以使用[**test case source**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcasesource.html)属性的方法之一:**

**如果你想探索其他的方法，你可以随时查阅官方文件。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **多重值**

**有时我们需要使用多个值作为测试的输入。在这种情况下，只对每个值重复测试是不好的。**

**你可能会想到使用 [**测试用例**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcase.html) 属性，这样就可以了。然而，这将是一种矫枉过正。**

**此外，如果您有多个输入，并且每个输入都可能是选项列表中的一个，该怎么办呢？您可能需要为每个组合添加一个 [**测试用例**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcase.html) 属性。这将是一个彻底的混乱。**

**NUnit 没有做所有这些，而是为我们提供了 [**值**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/values.html) 属性。**

**例如，如果我们如下使用它:**

**这意味着`MyTest`测试将执行 6 次，如下所示:**

```
MyTest(1, "A")
MyTest(1, "B")
MyTest(2, "A")
MyTest(2, "B")
MyTest(3, "A")
MyTest(3, "B")
```

**看，使用 [**值**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/values.html) 属性可以省去我们显式编写所有这些案例的麻烦。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **一系列**

**如果我们需要做同样的事情，但是没有提供完整的值列表，该怎么办？我们只想提供第一个值、最后一个值和要移动的步骤。**

**对于这种情况，NUnit 为我们提供了 [**范围**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/range.html) 属性。**

**例如，如果我们如下使用它:**

**这意味着`MyTest`测试将执行 4 次，如下所示:**

```
MyTest(0.2)
MyTest(0.4)
MyTest(0.6)
MyTest(0.8)
```

**看，使用 [**范围**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/range.html) 属性可以省去我们显式编写所有这些案例的麻烦。**

**这里值得一提的是， [**值**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/values.html) 和 [**范围**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/range.html) 属性可以组合如下:**

**这意味着`MyTest`测试将被执行 9 次，如下所示:**

```
MyTest(1, 0.2)
MyTest(1, 0.4)
MyTest(1, 0.6)
MyTest(2, 0.2)
MyTest(2, 0.4)
MyTest(2, 0.6)
MyTest(3, 0.2)
MyTest(3, 0.4)
MyTest(3, 0.6)
```

**不错吧。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **重复的**

**有时我们发现自己需要以连续的方式多次重复运行单元测试。大多数情况下，我们需要这样做来测试性能影响或对一些代码进行压力测试。**

**我自己，在不止一个场合，我需要这样做，因为我们有一个单元测试，它“有时”会在构建服务器上失败。我怀疑这是由一些发生的比赛或一些资源没有完全控制造成的。**

**因此，在本例中，我使用了 NUnit 为我们提供的 [**重复**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/repeat.html) 属性。**

**例如，如果我们如下使用它:**

**这将导致`MyTest`测试连续运行 25 次。**

**使用 [**重复**](https://docs.nunit.org/articles/nunit/writing-tests/attributes/repeat.html) 属性使得检测不稳定的测试和找到失败的根本原因变得太容易了。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **集合断言**

**有时我们需要对集合做一些断言。在这种情况下，NUnit 为我们提供了一些有用的实用程序。**

****断言。IsEmpty:** 可用于测试字符串、集合或 IEnumerable。当与字符串一起使用时，如果该字符串是空字符串，则成功。与集合一起使用时，如果集合为空，则成功。**

****断言。IsNotEmpty:** 可用于测试字符串、集合或 IEnumerable。当与字符串一起使用时，如果该字符串不是空字符串，则成功。当与集合一起使用时，如果集合不为空，则成功。**

****断言。包含:**用于测试一个对象是否包含在一个集合中。**

****CollectionAssert。AllItemsAreInstancesOfType:**用于测试集合中的所有项目是否都是某一类型的实例。**

****CollectionAssert。AllItemsAreNotNull:** 用于测试集合中的所有项目是否都不为空。**

****CollectionAssert。AllItemsAreUnique:** 用于测试集合中的所有项目是否唯一。**

****CollectionAssert。AreEqual:** 用于测试一个集合中的每一项是否等于另一个集合中对应的一项。项根据它们在两个集合中的顺序进行比较。**

****收藏。AreEquivalent:** 用于测试一个集合中的每一项是否等于另一个集合中对应的一项。无论项目的顺序如何，都会进行比较。**

**收藏者。AreNotEqual: 是 **CollectionAssert 的反义词。都相等**。**

**收藏者。AreNotEquivalent: 是 **CollectionAssert 的反义词。相当于**。**

****CollectionAssert。DoesNotContain:** 用于测试项目集合是否不包含某个对象。**

****集合服务器。IsSubsetOf:** 用于测试一个项目集合是否是另一个集合的子集。**

****CollectionAssert。IsNotSubsetOf:** 用于测试一个项目集合是否不是另一个集合的子集。**

****CollectionAssert。IsOrdered:** 用于测试一组项目是否有序。**

**要了解更多的细节，你可以随时查阅官方文件。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **事件处理程序**

**有时你需要测试你的一些模块，这些模块处理由其他模块触发的事件。**

**在这些情况下，最好的处理方法是抽象出触发事件的模块，这样您就可以模拟它们进行测试。让我给你看一个例子。**

**假设我们有一个传感器，可以读取汽车或任何物体的加速度。**

**如您所见，它只是一个带有事件的简单接口。尽管为了我们的例子，我们实际上不需要一个实现，但为了简洁起见，我只提供了一个简单的实现。**

**现在，假设我们有一个`AccelerationDashboard`模块，它使用`IAccelerationSensor`模块总是获得更新的加速度值。**

**如你所见，我们的`AccelerationDashboard`类依赖于`IAccelerationSensor`模块，并期望它通过构造函数被注入。**

**此外，它只处理由`IAccelerationSensor`模块引发的`AccelerationChanged`事件。**

**现在，为了测试`AccelerationDashboard`模块，我们可以这样做:**

**看，就是这么简单。使用 **Moq** 我们可以模仿`IAccelerationSensor`模块并触发`AccelerationChanged`事件，以便断言`AccelerationDashboard`模块上的`Acceleration`值。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **定时器**

**当您的系统使用定时器时，您可能面临的挑战之一是如何用单元测试完全覆盖您的系统。**

**为此，您需要抽象出`Timer`类并做一些调整来实现您的目标。**

**关于这一点，我已经发表了一篇非常详细的文章。如果你对这个话题感兴趣，你可以查看我的文章 [**使用定时器的最佳实践。**NET c#](/best-practice-for-using-system-timers-timer-in-net-c-867ab6b5027?sk=df2c03aff9a8bddbd62ce4d342d55c71)。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **日期时间**

**此外，如果您的系统使用 **DateTime** 或 **DateTimeOffset** ，您最终将面临一些挑战，用单元测试完全覆盖您的模块。**

**为了克服这些挑战，您需要将这些类抽象成提供者，将它们定义为依赖项，然后您将能够根据需要模仿和存根它们。**

**关于这一点，我已经发表了一篇非常详细的文章。如果你对这个话题感兴趣，可以查看我的文章 [**中的日期时间最佳实践。**NET c#](/datetime-best-practices-in-net-c-4c2679fcc9e0?sk=818404f760995c45e0b8b6f948dd3ff6)。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **I/O 文件和文件夹操作**

**当系统严重依赖 I/O 文件和文件夹操作时，这是许多开发人员面临的著名挑战之一。**

**为了用单元测试完全覆盖这样的系统，开发人员必须超越他几乎显而易见的需求。系统设计本身必须适应一些抽象层，其中每一层在整个系统中都有一个明确定义的规则。**

**关于这一点，我已经发表了一篇非常详细的文章。如果你对这个话题感兴趣，你可以查看我的文章 [**如何全面覆盖基于 I/O 文件的应用。NET C#带单元测试**](/how-to-fully-cover-i-o-file-based-applications-in-net-c-with-unit-tests-ca75c07f3b2c?sk=bc164097aca7c1d31e32e4dd35a60163) 。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)**

## **控制台系统**

**当处理控制台系统时，当我们的模块直接依赖于`System.Console`时，用单元测试覆盖整个系统变得非常困难。**

**这就是为什么我们首先需要抽象控制台 Api，并开始将它们作为依赖项使用。这样，用单元测试覆盖我们的控制台系统就容易多了。**

**关于这一点，我已经发表了一篇非常详细的文章。如果有兴趣进入正题，可以查看我的文章 [**如何全面覆盖。带有单元测试的. NET C#控制台应用程序**](https://itnext.io/how-to-fully-cover-net-c-console-application-with-unit-tests-446927a4a793?sk=63c75b56de78903f09f0d0116df5fe3a) 。**

**![](img/dae42316c04548aad197e34b378e3bf1.png)****![](img/76d4292a186dc4fa63b608550317e4fe.png)**

****最终想法**。由 [Pietro Rampazzo](https://unsplash.com/@peterampazzo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄，由 [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整**

# **最后的想法**

**在本文中，我们讨论了使用 [**NUnit**](https://nunit.org/) 和 [**Moq**](https://www.nuget.org/packages/Moq/) 库来覆盖我们的**库时需要遵循的一些重要提示、技巧和最佳实践。NET C#** 带单元测试的应用。**

**我给你的建议是做你自己的研究，并尝试寻找其他资源，这些资源可能帮助你了解更多关于测试技术和相关的最佳实践。**

**最后，我希望你觉得读这个故事和我写它一样有趣。**

**![](img/846628afe4d458feb09f819f4ec6b3ff.png)**

# **希望这些内容对你有用。如果您想支持:**

**如果您还不是**中**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我就可以从**中**获得您的一部分费用，您无需支付任何额外费用。订阅 [**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他有趣的东西直接发送到您的收件箱。**

**![](img/4f9017034da38b34f4034f54bdf8ee17.png)**

# **其他资源**

**这些是你可能会发现有用的其他资源。**

**[](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b) [## 分页/分区—简化这一过程的主要等式

### 最后，这是您理解分页/分区主要等式并学习如何在代码中应用它们的机会。

levelup.gitconnected.com](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b) [](https://itnext.io/when-string-gethashcode-in-net-c-drives-you-crazy-c97ac7507d7b) [## 当字符串。中的 GetHashCode()。NET C#让你抓狂

### 知道什么时候依赖字符串。中的 GetHashCode()。NET C#，而当不是。

itnext.io](https://itnext.io/when-string-gethashcode-in-net-c-drives-you-crazy-c97ac7507d7b) [](/useful-free-online-tools-for-developers-ed882d4761c1) [## 对开发者有用的免费在线工具

### 这是一个有用的免费在线工具列表，可以帮助开发者完成日常任务。

levelup.gitconnected.com](/useful-free-online-tools-for-developers-ed882d4761c1) [](/mistakes-made-by-developers-79af38b070b7) [## 开发人员犯的错误

### 这些是开发人员最常犯的错误。

levelup.gitconnected.com](/mistakes-made-by-developers-79af38b070b7) ![](img/4f9017034da38b34f4034f54bdf8ee17.png)**