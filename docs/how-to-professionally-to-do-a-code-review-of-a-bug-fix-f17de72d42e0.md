# 如何专业地对 Bug 修复进行代码审查

> 原文：<https://levelup.gitconnected.com/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0>

## 审查 bug 修复时要问的几个重要问题。

![](img/6ac027b333935c22fd4d81dc462ad3fa.png)

[Le Wagon](https://unsplash.com/@lewagon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

每个开发人员都会定期对 bug 修复进行代码审查，这是他们日常工作的一部分。这个过程简单明了，但是为了开发出具有良好代码质量的项目，开发人员应该记住并遵循一些最佳实践。

作为一名专业开发人员，在回顾队友的 bug 修复时，你会问自己这些问题吗？

# 单元测试有变化吗？

修复代码中的 bug 应该伴随着单元测试的添加或修改。

> 包含修复 bug 的代码更改但不包含单元测试更改的 pull 请求是一种气味。

如果代码更改是为了修复 bug，但是没有添加或更改任何单元测试，那么单元测试覆盖率是不够的。

假设开发人员实现了一个将一些字符串选项转换成数字的方法，并用单元测试覆盖了它。

```
public int Convert(string option)
{
   if (option == "No") return 0;
   else if (option == "Yes") return 1;
   else return -1;
}[InlineData("No", 0)]
[InlineData("Yes", 1)]
[InlineData("Invalid", -1)]
public void VerifyConvert(string option, int expectedValue)
{
   var actualResult = Convert(option);
   Assert.Equal(expectedValue, actualResult);
}
```

当前的单元测试完全覆盖了`Convert`方法——行和分支覆盖率都是 100%。

想象一下，一个开发人员忘记添加第三个“也许”选项，质量控制工程师报告了这个错误。如果开发人员通过简单地将缺少的选项添加到`Convert`方法中来解决问题，而没有将新的测试用例添加到单元测试中，那么该方法将不再具有完整的单元测试覆盖范围。

```
public int Convert(string option)
{
   if (option == "No") return 0;
   else if (option == "Yes") return 1;
   **else if (option == "Maybe") return 2;** 
   else return "-1";
}[InlineData("No", 0)]
[InlineData("Yes", 1)]
[InlineData("Invalid", -1)]
**//The test case for "Maybe" option is missing**
public void VerifyConvert(string option, int expectedValue)
{
   var actualResult = Convert(option);
   Assert.Equal(expectedValue, actualResult);
}
```

测试用例的缺失意味着，如果某个开发人员后来在重构过程中不小心将“2”改为“20”或者删除了“也许”选项，那么单元测试仍然会是绿色的，因为测试用例`[InlineDate("Maybe", 2)]`并没有使用添加到方法中的新选项来实现。问题会在一段时间后被发现，甚至可能在生产代码中被发现。因此，对于业务逻辑中的任何修正，相应的单元测试中必须有变化。代码审查是发现单元测试缺失的最佳时机。

# 我们是在解决根本原因还是仅仅是症状？

了解开发人员是在试图修复错误的根本原因，还是只是一个症状，这一点很重要。找到根本原因往往比仅仅修复症状需要更多的时间。但这是唯一正确的做事方式，让我们来看看为什么:

```
public decimal CalculateOrderPrice(int orderId)
{
   Order order = _orderRepository.GetOrder(orderId); decimal price = order.Price; //Potential null reference here
}
```

`Order`类是一个引用类型，它可以包含一个空值，当应用程序试图访问`Price`属性时会崩溃。

“修复”这个问题最简单的方法是添加一个空检查，然后忘掉它。然而，空引用异常只是一个更严重问题的征兆，比如订单创建的逻辑。这个原始的根本原因会在应用程序的不同地方引起问题，简单地添加一个空检查并不能解决根本问题。

开发人员应该始终找到并修复问题的根本原因，以便完全消除所有症状。如果由于业务压力或其他原因，开发人员只有时间解决症状，他们仍然需要确定根本原因，并在产品待办事项中创建一个**技术债务单**。

[](https://medium.com/codex/technical-debt-management-best-practices-for-software-engineers-871a315ac812) [## 软件工程师的技术债务管理最佳实践

### 技术债务率=应该返工的代码行数/现有源代码行数* 100%

medium.com](https://medium.com/codex/technical-debt-management-best-practices-for-software-engineers-871a315ac812) 

# 最后的想法

根据您的项目，在代码审查期间可能会出现许多其他问题。如果你的项目没有端到端的自动化测试，你可以问，*“开发人员向质量控制工程师描述了变更的影响范围了吗？”。*如果你的项目有全面的技术文档，你可以问:*“文档是否已经更新以反映源代码的变化？”。*

总是问问题。您可以从我在文章中描述的那些开始，然后用您自己的扩展列表，以便更好地进行代码审查。

# 更多文章

[](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [## 面向技术领导者和资深人士的 50 个软件工程最佳实践

### 最佳工程师的最佳实践。

levelup.gitconnected.com](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [](/the-simplest-explanation-of-adapter-design-pattern-cd37f02bfecd) [## 适配器设计模式的最简单解释

### C#中的真实世界示例

levelup.gitconnected.com](/the-simplest-explanation-of-adapter-design-pattern-cd37f02bfecd)