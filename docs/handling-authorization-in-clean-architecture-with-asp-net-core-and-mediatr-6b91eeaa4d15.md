# 用 ASP.NET 核心和中介器处理干净架构中的授权

> 原文：<https://levelup.gitconnected.com/handling-authorization-in-clean-architecture-with-asp-net-core-and-mediatr-6b91eeaa4d15>

![](img/14b901e8e2e9d07e4280188cf4141fbb.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Rohan Makhecha](https://unsplash.com/@rohanmakhecha?utm_source=medium&utm_medium=referral) 拍摄的照片

# 前言

最近，我开始与 CQRS 和 MediatR 一起使用 Clean Architecture，以便更好地关注我的应用程序和整体设计。但是我，可能也是大多数 Clean Architecture 的新手，一直在努力弄清楚在这个架构设置中应该如何以及在哪里处理授权。在你变得傲慢之前，就像“我的天啊，这个开发商这么糟糕，他不知道怎么做授权”。呃…看，我可以像其他人一样在谷歌上输入单词，好吗——这不是我现在遇到的问题。

# 我们现在面临的是沟通的失败

问题来自老大哥。微软会告诉你，控制器动作应该这样修饰:

此外，如果除了对用户进行身份验证之外，您还需要其他授权要求，那么您应该创建一个策略来处理高级授权逻辑:

不要让我告诉你这个开箱即用的解决方案在所有情况下都是不好的，因为它不是。如果你没有使用 Clean Architecture，并且你只有一个项目，其中所有的逻辑都包含在一个混乱的堆中，那么使用这个；这是为你做的，不要再看这篇文章了。但是在干净的架构领域，如果我们按照微软的文档定义授权策略，我们将在表示层中定义，并且至少执行业务逻辑。因此，要么我们有重叠的关注点，要么业务逻辑必须委托表示层负责，并在调用应用层内的任何业务逻辑之前执行必要的授权检查。

# 我们需要更深入

我们如何解决这个问题？很简单，我亲爱的华生先生。你看，我们把所有的授权检查都移到了更深的层次。让他们尽可能的往下走，但是不要再往下了！对我自己来说，我发现应用层是放置所有授权检查的最安全的地方，因为我的所有业务逻辑都在那里。幸运的是，我使用 mediator，如果你正在阅读这篇文章，你可能也在使用 mediator，这意味着我们可以利用管道行为来帮助我们。

在我以前的一篇文章中，我创建了一个管道行为，我们可以根据自己的目的对其进行扩展。所以，如果你还没有看这篇文章，那就去读一读:[用联发科和 ASP.NET 核心创建基本授权管道](https://medium.com/@austin.davies0101/creating-a-basic-authorization-pipeline-with-mediatr-and-asp-net-core-c257fe3cc76b)

# 该去工作了

让我们从概述基本思想开始:我们将创建一个授权管道行为，它将在任何 MediatR 请求之前运行，并检查该请求是否有某种授权需求。如果是这样，执行那些授权检查，然后，如果授权失败，抛出一个异常，或者让请求执行它的逻辑。

首先，让我们定义几个接口来包装 MediatR 的请求<>和请求处理程序类型:

这两个接口都应该位于应用程序层。

如果想到前面的清单让你想知道我们为什么要包装 MediatR 的接口，我用下面的内容离题:让我们谈谈强迫开发人员进入所谓的“成功之坑”。我这么说是什么意思？[解决方案架构师贾森·泰勒](https://github.com/jasontaylordev)，我认为他相当得体地解释了这个概念，他说“当你设计任何应用程序时，一个好的经验法则是清楚地定义那些使用它的人正在做什么。好的架构应该让做错事变得困难；作为一名开发人员，你不应该去猜测你做的事情是否正确。”。

考虑到这一点，我还会说包装这些接口的原因在本文的后面会变得越来越明显。

# 模仿老大哥

我将假设你实现了我上一篇文章中[的授权管道行为。然而，我在最后提出了一个问题，即利用这种行为会导致可重用性问题。但是如果你是一名优秀的开发人员，正如我所怀疑的那样，你可能会注意到我的警告，并按照我概述的方式使所有授权逻辑可重用。但是当你这么做的时候发生了什么？在实现了几个请求授权器之后，您是否注意到了一个模式？我猜想您很可能执行了与执行可重用授权逻辑相同的逻辑，并以完全相同的方式为每个请求授权者处理来自授权结果的响应。让我们根除这些问题。](https://medium.com/@austin.davies0101/creating-a-basic-authorization-pipeline-with-mediatr-and-asp-net-core-c257fe3cc76b)

让我们将 IAuthorizer 界面改为:

你会注意到 IAuthorizer <>界面有了几乎完全不同的用途；它不再具有先前版本中的 AuthorizeAsync()方法。您可能还会注意到，泛型类型名称略有不同；这个名字更多地反映了 MediatR 的 IRequestHandler 接口的通用类型名。

接下来，为了让我们的生活更简单，让我们创建一个抽象类，我们的 Authorizer 类将扩展这个抽象类:

这个抽象类为我们做了几件事。它负责通过“user requirement(IAuthorizationRequirement)”向“requirements”属性初始化和添加新的需求，最后，它仍然强制扩展它的类实现“IAuthorizer”。BuildPolicy()"方法，这对于将所需的参数传递给处理授权逻辑的授权需求非常重要。

接下来，我们将替换 RequestAuthorizationBehavior 类的实现，这样它将同时使用 IAuthorizer <>接口和 IAuthorizationRequirement:

在这里，我们注入 IAuthorizer <>的所有相关具体实现，如果您还记得的话，它是由 AbstractRequestAuthorizer 实现的。接下来，我们遍历 IAuthorizer 列表，并使用当前 MediatR 请求调用 BuildPolicy()方法。接下来，我们需要将当前授权者的所有需求添加到一个列表中，这样我们就可以等待需求处理者的响应。如果任何要求失败，我们将停止循环的执行，并抛出我们在上一篇文章中创建的未授权异常。

# 该是橡胶上路的时候了

此时，您应该已经具备了开始创建您的授权者和需求类所需的一切。如果您还没有注册，那么您将需要在您的 DI 容器中注册前面的管道行为和程序集中的所有授权者。如果你已经阅读了我的上一篇文章，你很可能已经这样做了，如果没有(或者你需要复习一下)看一下这里:[用 MediatR 和 ASP.NET 核心创建一个基本的授权管道](https://medium.com/@austin.davies0101/creating-a-basic-authorization-pipeline-with-mediatr-and-asp-net-core-c257fe3cc76b)。

我将继续上一篇文章中的例子，我们需要获得一些关于在线视频课程的数据:

如果你不记得了，这个请求的前提是，我想获得一个课程的特定视频的细节。然而，所有这些信息都被认为是有特权的，我们只希望订阅该课程的用户能够访问关于该视频的信息。

记住这个需求，让我们创建我们的第一个需求。这个需求可以并且应该在其他授权者可以访问的地方。将需求视为应用程序可以使用的可重用共享授权逻辑。对我来说，我将所有的需求类放在我的应用层中一个名为 Authorization 的文件夹中。

看起来就像你的标准媒体请求，嗯？接下来，让我们用下面的代码替换当前的“GetCourseVideoDetailAuthorizer”类:

请注意，我们使用了前面的“AbstractRequestAuthorizer”类，并使用新初始化的“mustavecoursesubscriptionrequirement”需求(显然是一个中介器)来调用其助手方法“UseRequirement”。IRequest 对象)，它包含特定于请求的信息，以帮助需求处理程序确定它是否应该通过授权检查。

# 结论

你有它！现在，您有了一种简单且可重用的方法来在应用程序层中运行和创建授权逻辑。我希望你喜欢这篇文章，下次再见。

# 附录

**Nuget 包**

[](https://www.nuget.org/packages/MediatR.Behaviors.Authorization/) [## 联发科。行为.授权 1.0.1

### 一个简单的请求授权包，允许您构建和运行特定于请求的授权需求…

www.nuget.org](https://www.nuget.org/packages/MediatR.Behaviors.Authorization/) 

**GitHub 来源**

[](https://github.com/AustinDavies/MediatR.Behaviors.Authorization) [## Austin Davies/mediator。行为.授权

### 一个简单的请求授权包，允许您构建和运行特定于请求的授权需求…

github.com](https://github.com/AustinDavies/MediatR.Behaviors.Authorization) [](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)