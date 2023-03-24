# 使用@Url 扩展在 C#中轻松生成链接

> 原文：<https://levelup.gitconnected.com/easy-link-generation-in-c-with-url-extensions-96183a4f2d84>

## 如何充分利用新的 C#源代码生成器

![](img/c1ae32afdb8dfa9cfe584d906397c5b3.png)

丹尼尔·冯·阿彭在 Unsplash[拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 介绍

自从 C# MVC 开始以来，开发人员一直在努力寻找在他们的视图中生成**URL 的最佳方式。有些人用字符串内插法写下整个 URL，而有些人用`@Url.Action("Action", "Controller")`。随着 ASP.NET 核心，微软推出了[标签助手](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-5.0)，但它仍然缺乏智能感知功能，因为它仍然使用神奇的字符串。**

在本文中，我将向您展示如何通过使用一个非常有用的名为 **UrlActionGenerator** 的包，在 ASP.NET 核心 MVC 应用程序中轻松生成链接。因此，让我们深入了解这个包使用的技术，并了解如何使用它来自己生成链接。

**TL；DR；**

*   使用包 [UrlActionGenerator](https://github.com/sleeuwen/UrlActionGenerator) 轻松生成链接
*   在生成过程中获取 IntelliSense 和有关链接生成的生成错误信息

# 技术

就像我之前说的，我们将使用包 [UrlActionGenerator](https://github.com/sleeuwen/UrlActionGenerator) 。本包由 [**SLeeuwen**](https://github.com/sleeuwen) 编写。UrlActionGenerator 利用了**源生成器**技术。根据微软的说法:“源代码生成器是在编译过程中运行的一段代码，它可以检查你的程序，以生成与你的代码的其余部分一起编译的附加文件。”

你现在可能会问自己，但这对我有什么好处？别担心😊我也这么做了。

要了解所有的好处，我强烈推荐阅读来自的这篇文章。网队。一个很好的收获是，通过在编译时执行**控制器发现阶段**，源代码生成器可以用来提高应用程序的性能。这可能会导致一些更快的启动时间，因为今天在运行时发生的动作可能会被推到编译时。

SLeeuwen 看到了这个机会，决定利用它。他写了一个库，使用源代码生成器在编译时发现你的控制器和动作。这些动作将作为扩展方法添加到`IUrlHelper`类中。使用该软件包有两大好处:

## 优势

*   **IntelliSense** 是各种代码编辑功能的统称，包括代码完成、参数信息、快速信息和成员列表。UrlActionGenerator 为 URL 生成提供了所有这些选项
*   **构建错误信息**是 UrlActionGenerator 的另一个非常有用的好处。当控制器方法需要参数时，当您在视图中使用 URL 生成器时，编译器会直接警告您。

# 使用

UrlActionGenerator 的 GitHub 库很好地展示了如何在代码中使用这个包，这也是我在这里使用这个例子的原因。

## 无参数

假设我们有以下控制器:

```
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

然后，我们可以在您的视图中使用以下代码轻松生成该操作的 URL—`/Home/Index`:

```
@Url.Actions().Home.Index()
```

## 带参数

但这还不是全部。有了这个包，我们还可以生成包含参数的链接。如果我们有以下控制器方法:

```
public class HomeController : Controller
{
    public IActionResult Search(int page, int pageSize = 20)
    {
        return View();
    }
}
```

我们可以用下面的代码生成 URL:

```
@Url.Actions().Home.Search(1);                  <-- 1.
@Url.Actions().Home.Search(2, pageSize: 50);    <-- 2.
```

这将为 1 生成 URL“`/Home/Search?page=1`”，为 2 生成 URL“`/Home/Search?page=2&pageSize=50`”。正如你所看到的，因为参数`page`是必需的，你**必须**把它提供给方法。如果不这样做，将会产生一个构建错误。

# 结论

很长一段时间以来，MVC 开发人员都在努力寻找在他们的视图中生成 URL 的最佳方式。但地平线上终于有了一丝曙光。包 [UrlActionGenerator](https://github.com/sleeuwen/UrlActionGenerator) 为开发人员提供了一种使用智能感知生成链接和构建错误信息的新方法。

有关如何实现该包的更多信息，请查看存储库中提供的示例项目:[https://github . com/sleeuwen/UrlActionGenerator/tree/master/samples/AspNetCoreSample](https://github.com/sleeuwen/UrlActionGenerator/tree/master/samples/AspNetCoreSample)

快乐编码😊

![](img/095d3d82a769bfc84cc3b54b1e73c6d6.png)

[https://ko-fi.com/koenvanzeijl](https://ko-fi.com/koenvanzeijl)

# 资源

*   微软博客:[https://dev blogs . Microsoft . com/dotnet/introducing-c-source-generators](https://devblogs.microsoft.com/dotnet/introducing-c-source-generators/)
*   套餐:【https://github.com/sleeuwen/UrlActionGenerator 