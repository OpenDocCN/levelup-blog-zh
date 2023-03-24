# 在应用程序启动时在 ASP.NET 核心中运行一次代码的 3 种方法

> 原文：<https://levelup.gitconnected.com/3-ways-to-run-code-once-at-application-startup-in-asp-net-core-bcf45a6b6605>

## 开发人员应该将初始化内存缓存的代码放在哪里？

![](img/e165fdc273c74342eb7b2fb6b5fad192.png)

照片由克莱门斯·范·雷在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

开发人员可能会发现，在 ASP.NET 核心应用程序启动时，某些代码只需执行一次。例如，开发人员可能需要填充内存中的缓存、运行后台任务、从一些外部源读取配置、发布应用程序成功启动的事件等。

ASP。NET Core 为开发人员提供了几种不同的方法来实现这一目标，但它们并不等同，开发人员在做出最终选择之前应该仔细分析。

# 直接从程序或启动类运行代码

在应用程序启动时只执行一次一段代码的最明显和直接的方法可能是将这段代码直接放入`Program`类的`Main`方法，或者`Startup`类的`Configure`方法。ASP.NET 核心确保这些方法以及其中的代码只在应用程序启动时执行。

开发人员可以根据具体情况选择两种方式来放置启动逻辑。例如，如果启动逻辑应该只在配置了请求处理管道之后运行，开发人员应该将它放在`Configure`方法的末尾，而不是在`IHost`对象创建之后。

将逻辑放在 main 中(或在极少数情况下放在 Configure 方法中)的主要优点是，启动逻辑可以在应用程序开始处理 web 请求之前执行，这对于初始化内存缓存或读取配置等任务非常重要。

# 从托管服务运行代码

ASP。NET Core 为开发人员提供了一个`IHostedService`接口，该接口具有在应用程序启动和停止时执行的`StartAsync`和`StopAsync`方法。该接口通常用于触发长时间运行的后台任务，但是`StartAsync`本身必须快速返回，以免阻塞其他托管服务(如果有的话)。

另外，最好知道在配置应用程序的请求管道之前(在执行`Configure`方法之前)运行了`StartAsync`方法。当启动逻辑只需要在应用程序的请求管道配置完成后运行时，这可能是一个问题。

# `Subscribe to` IHostApplicationLifetime 事件

另一个 ASP.NET 核心接口`IHostApplicationLifetime`允许开发人员为其处理程序订阅应用启动、应用停止和应用停止事件:

重要的是要注意，`OnStarted`事件只有在应用程序主机完全启动之后才会发布，也就是说，在服务和请求处理管道已经配置好并且应用程序已经开始为 web 请求提供服务之后。这意味着，如果在应用程序开始为 web 请求提供服务之前需要执行一些启动逻辑，那么就不应该选择生存期事件。

# 摘要

*   在应用程序开始处理 web 请求之前，所有类型的启动任务都可以直接从程序或启动类中执行。
*   长期运行的后台任务应该由托管服务启动和停止。
*   开发者可以使用`IHostApplicationLifetime`接口订阅`OnStarted`事件，该事件在应用主机完全配置并启动后发布。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](/top-misconceptions-about-dependency-injection-in-asp-net-core-c6a7afd14eb4) [## ASP.NET 核心中关于依赖注入的主要误解

### 这甚至会导致错误。

levelup.gitconnected.com](/top-misconceptions-about-dependency-injection-in-asp-net-core-c6a7afd14eb4)