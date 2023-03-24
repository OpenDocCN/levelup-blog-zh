# 中 SpecFlow 和行为驱动开发的高级技巧。网

> 原文：<https://levelup.gitconnected.com/advanced-tips-for-specflow-and-behavior-driven-development-in-net-3dcf93dc2655>

## SpecFlow 和您的 C#代码的专业技巧！

## 我们将关注场景上下文、安装和拆卸、ide 中的导航，以及在步骤定义中使用参数。

![](img/a4fc25a8a63b444745725b3eeaf00d83.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你可能已经看过我的另一篇关于行为驱动开发的文章。NET，这是一个关于如何为你设置它的教程。在这篇文章中，除了基础知识之外，我将给出更多的技巧，来提高你编写好的测试和更快地调试失败测试的能力。

[](https://xeladu.medium.com/behaviour-driven-development-bdd-with-c-and-specflow-1bb2deb13024) [## 使用 C#和 SpecFlow 的行为驱动开发(BDD)

### 这是一个关于如何在. NET 应用程序中使用 C#和 SpecFlow 的行为驱动开发(BDD)的介绍指南。

xeladu.medium.com](https://xeladu.medium.com/behaviour-driven-development-bdd-with-c-and-specflow-1bb2deb13024) 

## 使用场景上下文/功能上下文

[ScenarioContext](https://docs.specflow.org/projects/specflow/en/latest/Bindings/ScenarioContext.html#scenariocontext) 是一个状态对象，只要场景运行，它就存在。您可以使用它作为一个`Dictionary<string, object>`在步骤之间传递数据，这样您就不需要在您的步骤类中定义私有字段。
要访问 ScenarioContext，将其注入绑定类并保存对它的引用。

ScenarioContext 的一个有用属性是`CurrentScenarioBlock` 属性。它允许你区分`Given`、`When`和`Then`块。
有一个类似的对象 [FeatureContext](https://docs.specflow.org/projects/specflow/en/latest/Bindings/FeatureContext.html) ，和 ScenarioContext 基本相同，但是只要特性运行它就存在。如果您有与几个场景相关的数据，那么您可以使用 FeatureContext。

## 安装和拆卸的可能性

典型的单元测试框架，如 MsTest 或 NUnit，允许您为每个测试和每个测试类定义安装和拆卸方法。SpecFlow 提供了相同的功能，它们被称为[钩子](https://docs.specflow.org/projects/specflow/en/latest/Bindings/Hooks.html)。

SpecFlow 测试中所有可用的挂钩类型

为了准备一个数据库，您可能会选择`[BeforeTestRun]`和`[AfterTestRun]`属性。在每个特性或场景之前，你可能想要重置数据，所以你应该使用`[BeforeFeature]/[BeforeScenario]`和`[AfterFeature]/[AfterScenario]`属性。

## 使用 IDE

[SpecFlow](https://marketplace.visualstudio.com/items?itemName=TechTalkSpecFlowTeam.SpecFlowForVisualStudio2022) 扩展添加了一些不错的特性，您应该使用它们来提高工作效率。

*   当您在功能文件的某一行中单击并按 F12 键(或 CTRL + Click)时，IDE 会直接跳转到绑定方法。
*   要查找一个绑定方法的所有引用，右键单击该方法并选择**查找步骤定义用法**。
*   没有绑定方法的特征文件中的线具有不同的文本颜色。在编辑器中点击右键，选择**定义步骤**，即可生成方法。
*   您可以在特性文件中放置断点，测试执行将在该步骤执行之前停止。

## 在步骤定义中使用多个参数

要将多个值作为参数传递给步骤定义，您需要在您的特征文件中定义一个[数据表](https://docs.specflow.org/projects/specflow/en/latest/Gherkin/Gherkin-Reference.html#data-tables)。请参见下面的示例。

带有数据表的特征文件

访问数据表的项

## 运行具有不同参数的方案

要使用不同的参数多次运行一个场景，您需要定义一个[场景大纲](https://docs.specflow.org/projects/specflow/en/latest/Gherkin/Gherkin-Reference.html#scenario-outline)。每个占位符必须匹配`Examples`表中的一列。请参见下面的示例。

使用表定义多个方案运行。

该场景将针对表中的每一行运行。因此，如果您需要更多的测试用例，只需再添加一行即可。

## 结论

这些是我在编写测试时使用 SpecFlow 提高生产率的高级技巧。如果你喜欢这篇文章，我会很高兴得到掌声👏另外，如果你还没跟上我，我也很感激。

🌲 [linktr.ee](https://linktr.ee/xeladu) |☕ [咖啡](https://www.buymeacoffee.com/xeladu) |🎁[捐赠](https://www.paypal.com/donate/?hosted_button_id=JPWK39GGPAAFQ) |💻GitHub |🔔[订阅](https://xeladu.medium.com/subscribe)

顺便说一句:如果你还没有 Medium 会员，我推荐你使用[│我的推荐链接◀](https://medium.com/@xeladu/membership) ，因为它会让你访问 Medium 上的所有内容，并以一小部分费用支持我，而不会为你带来任何额外费用。谢谢大家！✨