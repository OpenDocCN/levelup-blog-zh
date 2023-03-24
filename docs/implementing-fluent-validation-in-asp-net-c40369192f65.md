# 在 ASP 中实现流畅验证。网

> 原文：<https://levelup.gitconnected.com/implementing-fluent-validation-in-asp-net-c40369192f65>

## 深入了解这个强大的工具，您应该将它用于您的。网络应用

![](img/4ad7df701fd7c0f092725172e7270b55.png)

Daria Nepriakhina 在 [Unsplash](https://unsplash.com/photos/zoCDWPuiRuA) 上拍摄的照片

验证数据是开发软件时需要考虑的一个重要方面。然而，问题是验证会导致大量多余的代码，或者在整个应用程序中有相似的验证代码，这违反了 DRY 原则。

如果你不知道什么是干，我建议你看看下面的文章。

[](https://medium.com/codex/become-a-better-programmer-with-these-software-engineering-principles-204fa93e8094) [## 用这些软件工程原则成为更好的程序员

### 干，吻，YAGNI 解释道

medium.com](https://medium.com/codex/become-a-better-programmer-with-these-software-engineering-principles-204fa93e8094) 

在这篇文章中，我将实现 *FluentValidation，*这是一个免费的强大工具，它将帮助你轻松地创建验证，并且易于维护和阅读。说到这里，让我们开始吧。

## 装置

您可以在 Visual Studio 或您正在使用的 IDE 中直接找到该包，或者导航到 IDE 的文件夹并运行以下命令:

```
Install-Package FluentValidation
```

或者使用。NET CLI

```
dotnet add package FluentValidation
```

## 它是如何工作的

关于 *FluentValidation* 最好的事情之一是它友好的语法，因为它使得编写验证器如此直观。

首先，我们需要一个我们想要验证的类，所以假设我们想要添加一个客户。

为了使用 *FluentValidation* 进行验证，我们可以编写以下代码:

这确实是基本的，但它突出了工具的直观性，显然，这些是通用值，但您可以将它们匹配起来以满足您的数据库字段的特征。

我们会更深入一点，但在此之前，让我先向您展示如何向服务集合注册 *FluentValidation* ，以便它实际工作。

## 使用服务集合进行注册流验证

```
**public** **void** ConfigureServices(IServiceCollection services) 
{
    services.AddMvc(setup => 
    {
      *//...mvc setup...*
    }).AddFluentValidation();

    services.AddTransient<IValidator<Person>, PersonValidator>();
    *//etc*
}
```

这是第一种方法，但是每次创建验证器时都需要添加一个新的验证器，这有点烦人，所以有一种更好的方法。

```
.AddFluentValidation(fv =>{fv.RegisterValidatorsFromAssembly(Assembly.GetEntryAssembly());fv.RegisterValidatorsFromAssembly(Assembly.GetExecutingAssembly());fv.ImplicitlyValidateChildProperties = true;});
```

这将自动注册从 AbstractValidator 派生的所有验证器。而*ImplicitlyValidateChildProperties*所做的是强制验证尝试自动为每个属性寻找验证器。这基本上意味着，如果所有属性都无效，请求就不会触及控制器。

## 在域中使用验证器

使用验证器的一种方式是在控制器中，假设我们已经在启动时启用了*ImplicitlyValidateChildProperties*来停止所有无效的请求。然而，在我看来，控制器中的验证器应该更基本一些，就像我们在“它是如何工作的”副标题中看到的那样。

在那里进行简单的验证会很快向我们的前端返回一个错误，这样用户就可以编辑他的字段并重新提交。然而，如果我们在点击控制器之前就使用复杂的验证，那么对于一些可能无效的东西，会导致性能下降。

所以这就是为什么我们可以在我们的域逻辑中有第二个验证器来为我们做繁重的工作，这里的语法有点不同于仅仅写一个验证器类。

在您的域中实现验证器应该是这样的，如果验证确实失败了，就返回错误。

现在，正如我所说的，我们可以将验证扩展得更复杂一些。

我已经删除了验证的其余部分，这样看起来更整洁一些。我们在这里做的是将 *CustomerDomain* 传递给我们的验证器，然后调用域中的方法来检查电子邮件是否在使用中以及邮政编码是否有效。

你可以在文档 中找到一些方便的内置验证器*t*[，我不得不说他们的文档非常好。](https://docs.fluentvalidation.net/en/latest/built-in-validators.html)

## 结论

这就是 *FluentValidation、*的基础，不需要太深入地探讨这个话题，也不需要浪费你的时间。我想你会发现它真的很容易使用，而且非常直观，因为你可以用简单的英语阅读大多数验证，所以我建议你尝试一下，因为它需要验证你的数据到一个不同的水平，如果你付出更多的努力，你可以把前端的验证降到最低。