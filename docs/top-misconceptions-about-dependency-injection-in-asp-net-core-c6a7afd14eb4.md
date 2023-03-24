# ASP.NET 核心中关于依赖注入的主要误解

> 原文：<https://levelup.gitconnected.com/top-misconceptions-about-dependency-injection-in-asp-net-core-c6a7afd14eb4>

## 这甚至会导致错误。

![](img/96330709bbd1abfb03f3bb3d8bc6a5a2.png)

照片由[努贝尔森·费尔南德斯](https://unsplash.com/@nublson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的工作中，我经常注意到那些最近开始在 ASP.NET 核心中使用依赖注入的开发人员，或者那些对这个主题不够关注的人，有一系列的误解。

一些误解会导致 bug，而另一些则会导致冗余编码。让我们来看看我发现最重复的那些。

# 每个 Web 请求仅创建一次作用域类

在 ASP.NET 核心中使用依赖注入注册一个类时，开发人员可以从三种生命周期中选择一种:瞬态、限定作用域和单例。

瞬态意味着每次从 DI 容器请求该类时，都会创建一个新的实例。Singleton 意味着将为整个应用程序生存期创建该类的单个实例。它将在 web 请求之间共享，并且只在应用程序关闭时释放。

当应该为每个 web 请求创建一次类的实例时，开发人员使用限定范围的生存期:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IUserRepository, UserRepository>();
}
```

但是，作用域生存期不能保证每个 web 请求只创建一次作用域类。限定了作用域的生存期意味着类在给定的作用域中只会被实例化**一次，但是在一个 web 请求中可以创建多个作用域。**

简单地说，在 ASP.NET 核心中，范围是实例化一个`IServiceScope`类型和调用它的`Dispose`方法之间的代码区域。

`IServiceScope`实例包含用于解析在`ConfigureServices`方法中注册的实例的`ServiceProvider`属性。

如果一个范围内的同一类型被解析多次，将总是返回相同的实例。

ASP。NET Core 在 web 请求开始时创建一个范围，并在 web 请求结束时释放该范围。这就是为什么每个 web 请求只创建一次作用域实例，但并不总是如此。

没有什么可以阻止开发人员创建子范围。从不同范围解析的相同类型的实例也将不同。

有些情况下，手动创建作用域是获取应用程序服务实例的唯一正确方式，例如，从单例解析作用域服务。对作用域行为的认识可以保护您免受各种问题的困扰。

🔔 [**现订阅**](https://esashamathews.medium.com/subscribe) **，以免错过我的下一篇文章。**

# 依赖注入框架始终验证强制依赖

[强制依赖](https://blog.ploeh.dk/2014/06/02/captive-dependency/)是一种依赖注入反模式，可能会导致代码中的错误。

要解决强制依赖问题，您所要做的就是将生存期较短的类注入到生存期较长的类中。**例如，将作用域服务注入到 singleton** 中。在这种情况下，不会为每个 web 请求创建和释放一个作用域类。作用域类将具有与单例相同的生存期，并且实际上将成为单例。

为什么这真的是个问题？

在 web 应用程序中，具有类似于缓存的状态的单例实例被设计为线程安全的，以避免当多个请求访问同一个单例对象时出现并发错误。但是，作用域类不需要是线程安全的，除非开发人员在 web 请求中启动其他并发线程。

强制依赖问题延长了对象的生存期。如果不是线程安全的作用域类成为一个单一实例，app 中就会存在并发错误。

ASP。NET Core 通过验证强制依赖关系的依赖关系图并在发现异常时引发异常来保护开发人员免受此问题的影响。

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<UserService>();
    services.AddScoped<IUserRepository, UserRepository>();
}public class UserService
{
    public UserService(IUserRepository userRepository) 
    { 
    }
}public class UserRepository : IUserRepository
{
}
```

运行上面的代码将导致一个异常，说明作用域类`IUserRepository`不能从 singleton `UserService.`使用

误解是，强制依赖项的 ASP.NET 核心验证将始终有效。但是，在 ASP.NET 核心中，强制依赖关系验证默认仅在**开发**环境中完成。

如果代码没有在本地进行良好的测试，则在开发以外的环境中不会出现异常。相反，服务的生存期将意外延长，从而导致潜在的并发性和其他问题。

要确保始终针对强制依赖关系验证代码，请在应用程序入口点将 **ValidateScopes** 选项设置为 true:

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();

        }).UseDefaultServiceProvider((context, options) => {
            **options.ValidateScopes = true;**
        });
```

# 依赖注入容器始终处置对象

依赖注入容器不仅负责实例化对象，还负责处置对象。

暂态和作用域服务在作用域末尾(在 ASP.NET Core 中的 web 请求末尾)处置，而单例类在应用程序关闭之前处置。

依赖注入容器完全管理应用程序服务的生命周期。开发人员不需要记得在适当的时候调用`Dispose`方法。

然而，在极少数情况下，开发人员可能需要自己实例化类，然后将实例提供给 DI 容器:

```
public void ConfigureServices(IServiceCollection services)
{
    **services.AddSingleton(new Service());**
}
```

在这种情况下，正如开发人员可能预料的那样，该对象不会在应用程序关闭时被释放。现在，他们需要记住自己释放实例，以避免由于未释放资源而可能出现的问题。

另外，开发人员可能会错误地使用上面的语法来注册一个对象，而不是这个:

```
**services.AddSingleton<Service>();**
```

使用这种语法，对象将被创建，然后由依赖注入容器处理，这是更好的选择。

两条线很像，很容易一不小心用错。小心点。

# 依赖注入容器只接受带接口的类

构造函数依赖注入经常使用接口来实现。这就是为什么一些开发人员错误地认为类必须有一个接口才能在依赖注入容器中注册。

这种误解导致为并不真正需要接口的类提取接口。

下面的示例演示如何向阿迪容器注册没有接口的类:

```
**//Class without interface**
public class SomeClass
{
}**//Registering the class**
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<SomeClass>();
}**Injecting the class into some service**
public class SomeService
{
   public SomeService(SomeClass someClass)
   {
   }
}
```

接口是一个很棒的特性，它允许你为类型定义一个契约，实现松散耦合，在运行时替换类型，促进可测试性等等。然而，在类之间的编译时依赖是可接受的情况下，最好不要用冗余接口污染代码。

# 摘要

*   如果开发人员创建额外的作用域，则在单个 web 请求中可以创建一个作用域类的许多实例。
*   依赖注入容器仅在开发环境中验证受控依赖的依赖图。确保为所有环境启用验证。
*   DI 容器只处理它创建的对象。在 DI 容器之外创建的对象必须由开发人员处理。
*   类不需要在 DI 容器中注册接口。

## 我的其他文章

[](/stop-using-meaningless-naming-in-your-code-121f4eaff6fd) [## 停止在代码中使用无意义的命名

### 给事物起正确的名字很容易。

levelup.gitconnected.com](/stop-using-meaningless-naming-in-your-code-121f4eaff6fd) [](/5-ways-to-clone-an-object-in-c-d1374ec28efa) [## 在 C#中克隆对象的 5 种方法

### 各有利弊

levelup.gitconnected.com](/5-ways-to-clone-an-object-in-c-d1374ec28efa) [](/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0) [## 如何专业地对 Bug 修复进行代码审查

### 审查 bug 修复时要问的几个重要问题。

levelup.gitconnected.com](/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0)