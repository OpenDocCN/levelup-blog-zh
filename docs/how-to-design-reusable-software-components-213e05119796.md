# 开发人员编写可重用代码的两个简单技巧

> 原文：<https://levelup.gitconnected.com/how-to-design-reusable-software-components-213e05119796>

![](img/f87d8a23df7a2e35d1cfac3c29227e63.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

代码重用是一件很棒的事情，因为开发人员不必从头开始实现项目中的每个新特性。新的功能可以建立在系统中已经存在的组件之上，例如方法、类、库或整个微服务。

代码重用加快了开发时间。此外，当新功能建立在现有组件之上时，开发人员会感到有信心，因为组件很可能被单元测试所覆盖，甚至可能是产品代码的一部分。这意味着现有代码已经过很好的测试。

然而，即使在大型软件系统中，也很难找到可重用的组件，因为这些组件没有被设计为在新的上下文中重用。

可复用的软件组件和不可复用的软件组件有什么不同？一个可复用的软件组件遵循**单一责任原则**，并且**松散耦合**到它的依赖项，而一个不可复用的组件违反其中一个或者两个。

# 单一责任原则

无法重用软件组件的第一个原因是违反了单一责任原则。每个组件，不管其级别(方法、类等。)，必须有单一的改变理由。组件的行为应该用一个不包含连词“和”或“或”的句子来描述。

让我们看看为什么违反单一责任原则会扼杀代码重用，在这个简单的例子中:

```
public class FileParser
{
   public bool Parse(FileInfo file)
   {
      if(file.Extension != ".txt")
      {
         return false;
      } var parseResult = Parse(file);    

      return true;
   }

   //TODO: parsing logic here
}
```

这个类违反了单一责任原则，因为它有两个改变的原因。第一个原因是需要更改验证逻辑，第二个原因是需要更改解析文件的逻辑。

开发人员将不能在新的上下文中只重用验证逻辑，因为 SRP violator 类的所有职责必须一起重用。

当需要重用属于同一类的几个职责中的一个时，开发人员有什么选择？实际上有三个:

1.  开发人员可以将验证逻辑复制并粘贴到新位置。
2.  开发人员可以实现一个布尔标志，并在外部设置它来启用或禁用验证逻辑。
3.  验证逻辑的责任应该提取到一个单独的类中。

第一个和第二个选项只会降低应用程序的可维护性。第三个是每个开发者都应该努力避免在项目中出现技术债务。

在某些情况下，违反单一责任原则的类仍然可以在新的上下文中重用，而无需对其进行任何更改。当新的上下文需要类中包含的所有职责时，这是可能的。然而，这可能是偶然发生的，而不是故意的。

让组件有一个改变的理由是迈向代码重用的第一大步。然而，为了拥有可重用的组件，还有第二个重要的原则要遵循。这被称为松耦合。

# 松耦合

该类通常具有注入类构造函数或通过静态属性访问的依赖项。这个类应该有依赖关系，因为它不需要自己完成所有的工作。否则，将违反单一责任原则。

当要使一个类依赖于另一个类时，有紧耦合和松耦合两种选择。紧密耦合意味着类之间的关系是在编译时定义的，在运行时无法打破这种关系。

```
public class ReportGenerator
{
   public Report Generate()
   {
      var data = HttpClient.GetData("http://projectapi.com/data/");

      return GenerateReport(data);
   } //TODO: report generation logic here
}
```

*report generator 类与其依赖项 HttpClient 类密切相关。*

与其依赖关系密切的类通常不能被重用，因为新的上下文可能不知道如何处理它。

在上面的例子中，新的上下文可能想要提供自己的数据来从数据库生成报告，而不是通过 API 接收它。但是紧密耦合迫使客户端重用他们不需要的 HttpClient。因此，他们不会重用报告生成逻辑。

ReportGenerator 必须从数据源中抽象出来。一种方法是将 ReportGenerator 类重构为类似如下的形式:

```
public class ReportGenerator
{
    private readonly IReportDataProvider _reportDataProvider; public ReportGenerator(IReportDataProvider reportDataProvider)
    {
        _reportDataProvider = reportDataProvider;
    } public Report Generate()
    {
        var data = _reportDataProvider.GetData(); return GenerateReport(data);
    }

    //TODO: report generation logic here
}
```

HttpClient 类必须封装在 IReportDataProvider 接口的实现中。需要重用 ReportGenerator 类的客户端只需提供自己的 IReportDataProvider 接口实现，该接口可以封装从数据库或任何其他来源获取的数据。

松散耦合通常通过使用依赖注入技术和接口来实现，但是还有其他设计模式需要考虑，比如中介、环境上下文等。

# 结论

当开发可重用代码或重构不可重用代码时，开发人员不应该重新发明轮子。面向对象设计中有相当多的**设计模式**(装饰者、代理、责任链、门面……)和**设计原则**(控制反转、依赖反转……)教导开发人员即使在最复杂的场景中也要将这些基本原则应用于软件组件。

# 关于可维护性的更多信息

[](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [](https://medium.com/codex/when-code-quality-metrics-can-be-of-real-value-to-software-engineers-7fc2de96afc4) [## 充分利用代码质量度量

### 在软件工程中充分利用圈复杂度、可维护性指数、单元测试覆盖率和其他度量

medium.com](https://medium.com/codex/when-code-quality-metrics-can-be-of-real-value-to-software-engineers-7fc2de96afc4)