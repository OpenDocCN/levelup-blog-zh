# 停止在代码中使用无意义的命名

> 原文：<https://levelup.gitconnected.com/stop-using-meaningless-naming-in-your-code-121f4eaff6fd>

## 给事物起正确的名字很容易。

![](img/c6f475467071d73e81da15d5f09e9da7.png)

[胖子 Bytyqi](https://unsplash.com/@fatosi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

软件开发人员花大部分时间阅读代码，而不是编写代码。由于这个事实，代码应该读起来像一本书，这样它不仅可以被编译器理解，也可以被其他开发人员理解。

这里就不跟大家分享标准的、众所周知的命名约定了。我整理了一些静态代码分析器通常不会报告的常见侵入性命名违规，以及如何修复它们。

# 名称中的模糊词语

程序员经常使用含糊不清的词语，比如“Temp”、“Dto”、“Info”、“Item”、“Base”等等。，这不会给名称增加任何价值。

通常只要把名字里的黄鼠狼字去掉就够了，意思不变。

## 严重的

```
public abstract class EntityBase
{
}
```

## 好的

```
public abstract class Entity
{
}
```

# 负布尔名称

开发人员应该付出额外的努力，试图扭转他们头脑中的负面布尔名称。

## 严重的

```
if (!isInactive)
{
    //Do something
}
```

## 好的

```
if (isActive)
{
    //Do something
}
```

如果你把两段相同的代码翻译成简单的英语，第一段听起来像是*“如果不是不活动的，那么做点什么”*，第二段听起来像是*“如果是活动的，那么做点什么”。第二句没有否定，这样更容易阅读。*

# 不可读的单元测试名称

最常见的命名约定之一是测试名称包括被测试方法的名称、条件和预期结果。

根据这个约定命名的单元测试不容易阅读。此外，它们需要更多的维护工作，因为每次您重命名测试中的方法时，您也需要重命名单元测试。

最好给你的单元测试命名，就好像你想让非技术人员容易理解测试的目的是什么，而不必去猜测。

## 严重的

```
[Test]
public void IsPrime_InputIs1_ReturnFalse()
{
}
```

## 好的

```
[Test]
public void Number_1_is_not_a_prime_number()
{
}
```

# 服务/经理/助手/实干家痴迷

开发人员通常可以避免在类的末尾使用 Service、Manager、Helper、Doer 这样的词，这样名字会更清晰。

## 严重的

```
public class OrderDiscountCalculationService
{
}
```

## 好的

```
public class OrderDiscountCalculator
{
}
```

避免使用“服务”、“经理”、“助手”、“实干家”这样的词——这将鼓励你为你要实现的类想出一个更精确的名字。一个类的名字越具体，它所包含的功能就越具体。

# 将参数名放在方法名中

通常存储库方法被命名为`GetByProperty1AndProperty2...`

在方法名称中包含方法参数名称会使方法难以阅读和维护。这是违反干原则的。

## 严重的

```
public IReadOnlyList<Order> GetOrdersByStoreIdAndOrderStatus(StoreId storeId, OrderStatus status)
{
}
```

## 好的

```
public IReadOnlyList<Order> GetOrders(long storeId, Status status)
{
}
```

另一个好的选择是根据它所服务的**业务场景**来命名存储库方法，例如`GetStoreRelatedOrders`。

# 摘要

*   避免使用“临时”、“Dto”、“信息”、“项目”、“基础”等含糊其辞的词语，因为它们没有任何价值。
*   对字段、属性和方法使用正布尔名称。
*   为单元测试命名，以便没有技术知识的人能够理解其目的。
*   不要在类或接口的名称中添加“服务”、“管理器”、“助手”、“实干家”等词语。
*   不要在方法名中重复方法参数的名称。

## 我的其他文章

[](/4-ways-to-implement-builder-design-pattern-in-c-dd193e07096c) [## 在 C#中实现生成器设计模式的 4 种方法

### 构建器，流畅构建器，严格构建器，嵌套构建器。

levelup.gitconnected.com](/4-ways-to-implement-builder-design-pattern-in-c-dd193e07096c) [](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [](/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b) [## 5 GitHub。净回购，让你的技术技能更上一层楼

### 亲身体验 GitHub 资源库。

levelup.gitconnected.com](/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b)