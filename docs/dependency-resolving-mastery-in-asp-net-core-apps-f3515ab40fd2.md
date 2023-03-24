# ASP.NET 核心应用程序中的依赖性解决技巧

> 原文：<https://levelup.gitconnected.com/dependency-resolving-mastery-in-asp-net-core-apps-f3515ab40fd2>

## 如何正确解析应用程序不同部分的依赖关系？

![](img/260711c936fc10fc06ecea70c97ed9da.png)

由[凯文·Ku](https://unsplash.com/@ikukevk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

依赖注入是软件开发中一个相当简单的概念。然而，当它在一个复杂的 ASP.NET 核心项目的整个代码库中大量使用时，通常会出现许多细微差别。

使用 ASP.NET 核心时，开发人员可能会注意到，众所周知的构造函数注入技术在应用程序的某些地方工作得很好，而在其他地方则完全不合适。

在本帖中，我们将探讨如何正确解析来自 ASP.NET 核心应用程序不同部分的虚拟`IService`接口。

```
public interface IService
{
    void Do();
}public class Service : IService
{
    public void Do()
    {
        Console.WriteLine("Do");
    }
}
```

# 从构造函数解析依赖关系

在绝大多数情况下，构造函数注入技术用于解决依赖关系。

```
public class ParentService
{
    private readonly **IService _service**; public ParentService(**IService service**) =>
        _service = service;
}
```

通过构造函数注入依赖关系极大地简化了代码的可读性。为了理解一个特定的类使用什么依赖关系，开发人员只需要看一下类的构造函数。

然而，构造函数注入至少在以下情况下不起作用:

*   依赖项的生存期比注入依赖项的类的生存期短。这个问题被称为**受控依赖**，并且已经在我的[另一篇文章](/top-misconceptions-about-dependency-injection-in-asp-net-core-c6a7afd14eb4)中描述过。
*   当不再需要依赖项时，开发人员需要删除它，而不是等待 DI 容器来处理它(瞬态或限定范围的依赖项仅在请求结束时处理)。
*   需要将依赖项注入到自定义属性的构造函数中，但是只能将文本传递给属性构造函数。

在这篇文章的剩余部分，我们将看看开发人员可能会在哪里遇到上述限制，以及如何克服它们。

# 从程序类解析依赖关系

当需要从 web 应用程序入口点(即`Program`类)解析依赖关系时，构造函数注入将不起作用。

上面的代码没有太大意义。应用的入口点是静态的`Main`方法，所以不会调用`Program`类的非静态构造函数。即使`Main`方法是非静态的，构造函数注入仍然没有意义，因为`ConfigureServices`方法的执行比`Program`类的构造函数晚得多。

通过访问`IHost`对象，可以从`Program`类中正确解析所需的依赖关系:

在这种情况下，依赖关系很容易解决，因为`ConfigureServices` 方法是在`IHost`对象完全构建之前执行的。

另一个重要的注意事项:`IService`对象将被放置在创建它的作用域的末尾(第 13 行)。因此，从自定义范围解析依赖关系允许开发人员在需要时更好地控制依赖关系的生存期。

# 解决中间件的依赖性

中间件的生存期是单一的，因此具有单一生存期的依赖关系可以安全地注入到中间件构造函数中:

然而，有时也有必要将限定范围的或暂时的依赖注入到中间件中。试图通过中间件构造器做到这一点将导致**强制依赖问题**。正确的方法是将依赖注入到`Invoke`方法中，如下所示:

注意，具有单一生存期的依赖项不仅可以在构造函数中注入，也可以在`Invoke`方法中注入。

# 解析托管服务的依赖关系

托管服务具有单一生存期，因此只有具有相同生存期的依赖项才能通过构造函数安全地注入。

不应将限定范围的或暂时的依赖注入托管服务的构造函数中，以避免强制依赖问题。

安全的方法是将`IServiceProvider`实例注入托管服务的构造函数中，并通过在`ExecuteAsync`方法中创建一个范围来解决依赖性。

`IService`接口的实例将在调用`GetRequiredService`方法时创建，并在`ExecuteAsync`方法结束时由于其作用域被释放而被释放。

# 从操作筛选器属性解析依赖关系

当需要在 API 端点执行之前或之后执行某些逻辑时，可以使用动作过滤器。实现动作过滤器的一种方法是从`ActionFilterAttribute`中派生出自己的类，然后覆盖所需的方法。

有时可能需要将一些依赖项注入到属性中，如下所示:

```
**//Action filter:**
public class MyAttribute : ActionFilterAttribute
{
    private readonly IService _service; public MyAttribute(IService service) => _service = service; public override void OnActionExecuting(ActionExecutingContext context)
    {
        _service.Do(); //...
    }
}**//API Endpoint:** [MyAttribute]
public IEnumerable<Student> Get()
{
    //...
}
```

但是，将非基元类型注入属性的构造函数将使该属性由于编译错误而无法应用于控制器操作— *没有给定的参数对应于“MyAttribute”的必需形参“service”。我的属性(IService)'* 。

解决属性依赖性的一种有效方法是访问执行上下文:

然而，这种方法并不干净，因为开发人员需要检查`MyAttribute`类的整个实现，以便理解属性使用了什么依赖关系。基本上，它是一个服务定位器反模式，在代码中创建隐式依赖关系。

一个更干净的解决方案是开始使用`IActionFilter`接口和`ServiceFilter`属性，这允许开发人员使用构造函数注入技术:

# 最后的想法

我们已经涵盖了 ASP.NET 核心应用程序中开发人员可能需要注入依赖项的大部分地方。

构造函数注入是一项惊人的技术，但它并不是 100%的时候都有效。了解其他方法会让开发人员在日常工作中更加自信。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](/7-tricky-questions-to-ask-net-developer-in-a-job-interview-9cdb3789db54) [## 要问的 7 个棘手问题。NET 开发人员在工作面试

### 带着答案。

levelup.gitconnected.com](/7-tricky-questions-to-ask-net-developer-in-a-job-interview-9cdb3789db54)