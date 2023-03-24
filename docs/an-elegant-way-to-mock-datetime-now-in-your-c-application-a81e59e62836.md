# 一种模仿日期时间的优雅方式。现在，在您的 C#应用程序中

> 原文：<https://levelup.gitconnected.com/an-elegant-way-to-mock-datetime-now-in-your-c-application-a81e59e62836>

## 你武器库中的另一种编码技术。

![](img/f6967c6af69a55e6889b28d5042d9ab6.png)

照片由 [Sonja Langford](https://unsplash.com/@sonjalangford?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我之前的一篇[文章](/5-ways-to-mock-datetime-now-for-unit-testing-in-c-bf0438eab032)中，我展示了 5 种模拟`DateTime.Now`属性进行单元测试的方法，其中 4 种基于不同的包装类。今天我们将探索另一种在测试中使用委托模仿`DateTime`的技术。

# 有什么问题？

在开始探索解决方案之前，让我们快速回忆一下通过在应用程序代码中直接使用`DateTime.Now`可以得到的所有问题。

```
public class Service
{
    private readonly ILogger<Service> _logger; public Service(ILogger<Service> logger)
        => _logger = logger; public void LogNow()
    {
        _logger.LogInformation($"Current time is {**DateTime.Now**}");
    }
}
```

在应用程序的不同地方直接使用`DateTime.Now`或`DateTime.UtcNow`属性有几个缺点:

*   当从代码中的不同位置直接调用这些属性时，开发人员无法快速地将整个应用程序从`DateTime.Now`切换到`DateTime.UtcNow`，反之亦然。
*   在上面的例子中，`DateTime`对象是`Service`类的隐式依赖。这意味着开发人员需要从上到下查看该类的整个代码，以了解该类是否使用了`DateTime`。另一方面，`ILogger<Service>`是一个显式依赖(注入到构造函数中)，这要容易揭示得多。
*   如果不使用第三方库，比如 [Pose](/5-ways-to-mock-datetime-now-for-unit-testing-in-c-bf0438eab032) ，那么`DateTime.Now`不能简单地在单元测试中被模仿。

# 解决方法是什么？

`DateTime.Now`上面描述的问题可以通过实现你自己的包装类或者使用第三方库来解决，正如我在这里[所描述的](/5-ways-to-mock-datetime-now-for-unit-testing-in-c-bf0438eab032)。但是我们可以走得更远，使用委托以更少的努力实现相同的目标。

首先，我们需要定义一个返回`DateTime`类型且不带参数的委托:

```
public delegate DateTime **CurrentTime**();
```

下一步是在依赖注入容器中为我们的委托注册一个 lambda 表达式。lambda 表达式应该根据您的需要返回一个`DateTime.Now`到`DateTime.UtcNow`的实例:

```
builder.Services.AddSingleton<**CurrentTime**>(() => **DateTime.Now**);
```

万事俱备，开始在应用服务中注入`CurrentTime`实例并使用它:

```
public class Service
{
    private readonly **CurrentTime _currentTime**;
    private readonly ILogger<Service> _logger; public Service(**CurrentTime currentTime**, ILogger<Service> logger)
    {
        **_currentTime = currentTime;**
        _logger = logger;
    } public void LogNow()
    {
        _logger.LogInformation($"Current time is {_**currentTime()**}");
    }
}
```

现在让我们分析一下管理`DateTime.Now`属性的基于委托的方法的优缺点。

## 优点:

*   `CurrentTime`委托是一个显式依赖，因为它被注入到类构造函数中。
*   要将整个应用程序从`DateTime.Now`切换到`DateTime.UtcNow`，只需要在一个地方修改 lambda 表达式。
*   在单元测试中，可以用返回预定义时间戳的 lambda 表达式轻松模拟`CurrentTime`委托。

## 缺点:

*   现在可以在应用程序中混合使用这两种方法——使用`CurrentTime`,在某些情况下仍然可以直接调用`DateTime.Now`属性。为了确保只使用`CurrentTime`，开发人员应该在代码审查清单中描述该规则，或者/和用新的定制规则扩展静态代码分析器。

编码快乐！

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](/top-misconceptions-about-dependency-injection-in-asp-net-core-c6a7afd14eb4) [## ASP.NET 核心中关于依赖注入的主要误解

### 这甚至会导致错误。

levelup.gitconnected.com](/top-misconceptions-about-dependency-injection-in-asp-net-core-c6a7afd14eb4)