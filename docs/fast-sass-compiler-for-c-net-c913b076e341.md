# C#快速 Sass 编译器。网

> 原文：<https://levelup.gitconnected.com/fast-sass-compiler-for-c-net-c913b076e341>

## 如何用 C#从 Sass 文件生成 CSS？NET 而不使用 NodeJS。

![](img/ee75543694750609d3ca89fd8f25f1a6.png)

# 介绍

在我之前的故事中，我写过关于如何在 ASP.NET MVC 应用程序中使用 sass 的文章。我使用 NodeJS 和一个 C# watcher 来定义哪些 Sass 文件被更改，并相应地生成 CSS 文件。除了观察这些 Sass 文件中的变化，我还想在构建时生成 CSS 文件，让 CI/CD 管道自动构建它们。在试图实现这一点的时候，我不得不改变和添加了相当多的文件，感觉开发过程并没有变得更容易。它也确实产生了所有**节点模块**的开销。

因此，这让我想到，我是否可以自己创建一个包来做所有这些事情，但是提高它的可用性和性能。经过一些编程 [AspNetCore。SassCompiler](https://github.com/koenvzeijl/AspNetCore.SassCompiler) 已创建。sassCompiler 使用 dart-sass 可执行文件从 Sass 文件生成 CSS 文件。为什么是 dart-sass 可执行文件？你会在表演部分读到。

# 可用性

如上所述，我想创建一个性能更好、开发者体验更好的 NuGet 包。为了实现这一点，我们创建了一个只需要添加 NuGet 包的包。作为默认配置，包将在`/Styles`文件夹中查找 Sass 文件，并相应地在`wwwroot/css`中生成 CSS 文件。默认使用 dart-sass 参数`--stle=compressed`。您可以通过向您的`appsettings.json`添加并更改以下部件来轻松改变这一点:

```
{
  "SassCompiler": {
    "SourceFolder": "Styles",
    "TargetFolder": "wwwroot\\css",
    "Arguments": "--style=compressed"
  }
}
```

如果您想在您的项目中包含 Sass 文件监视器，您还需要在`startup.cs`中添加下面的 lines ConfigureServices 方法:

```
public void ConfigureServices(IServiceCollection services) 
{

#if DEBUG
  services.AddSassCompiler();
#endif

}
```

**就这些**！现在，您的项目中添加了一个简单快速的 Sass 编译器，适用于所有开发人员和 CI/CD 管道。在下一段中，你会读到为什么它比我在前面的故事中使用的技术更快。

# 表演

在我之前关于用 C#编译 Sass 的故事中。我使用 JavaScript 编译器来转换 Sass 文件。通过对这种方法做更多的研究，结果是"**Dart Sass 在 Node 上仍然比在 Dart VM 上慢得多 T16，并且随着原始 Dart 代码变得更快，这种相对减慢变得更加明显。"要了解更多信息，我推荐阅读下面的 GitHub 页面:**

[https://github.com/sass/dart-sass/blob/master/perf.md](https://github.com/sass/dart-sass/blob/master/perf.md)

# 结论

作为开发人员，我们希望让我们的生活尽可能简单。编写 Sass 而不是 CSS 是去除前端开发中重复性工作的一种方式。但是在决定编写 Sass 之后，我们需要找到一种有效地将其转换为 CSS 的方法。使用 NuGet 包 [AspNetCore。SassCompiler](https://github.com/koenvzeijl/AspNetCore.SassCompiler) 将使这个过程变得简单快捷。如果你想看一个例子，我可以向你推荐:【https://github.com/koenvzeijl/AspNetCore. sass compiler/tree/master/Samples/AspNetCore。SassCompiler.Sample

快乐编码😊

# 资源

*   [https://github.com/koenvzeijl/AspNetCore.SassCompiler](https://github.com/koenvzeijl/AspNetCore.SassCompiler)
*   [https://github.com/sass/dart-sass/blob/master/perf.md](https://github.com/sass/dart-sass/blob/master/perf.md)