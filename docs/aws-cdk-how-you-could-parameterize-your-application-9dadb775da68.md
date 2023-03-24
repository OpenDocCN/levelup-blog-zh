# AWS CDK —如何配置您的应用程序

> 原文：<https://levelup.gitconnected.com/aws-cdk-how-you-could-parameterize-your-application-9dadb775da68>

![](img/f80955f15320fac81fa4b9a135ff2a19.png)

照片由[丹尼斯·莱昂](https://unsplash.com/@denisseleon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我已经在亚马逊网络服务公司工作了将近两年。大约 8 个月前，我从普通的 CloudFormation 模板开始，转而使用云开发工具包。这是一个非常简洁的工具，使得资源部署变得相当容易。我最喜欢的是你如何模块化你的代码，以及你如何轻松地在不同的栈间共享资源。然而，我鼓励你在马上进入 CDK 之前开始学习 CloudFormation，因为 CDK 编译 CloudFormation 模板，有时你需要知道它们是如何工作的，否则你会遇到一些问题。让我给你看一个例子:

CDK 陷阱

有人可能认为`myBucketArnParam`的值会被打印到控制台。但实际上它打印了:`${Token[TOKEN.575]}`因为 SSM 参数不是在编译时解析的，而是在执行模板时解析的。另一个问题是，`.grantRead()`应该允许“*使用这个密钥来解密 bucket […]* 的内容。如果桶和键在同一个堆栈中，这才是正确的，因为键策略必须与键一起创建。换句话说，你将不被允许解密数据。不管怎样，还是来说说这篇文章的主题吧。

我一直在努力寻找正确参数化/配置我的应用程序的方法。有几种方法，每种都有自己的缺陷。我已经说过，代码模块化是 CDK 的主要优势之一。然而，有时你最终会得到一个堆栈，这个堆栈创建了一个构造，而这个构造本身又创建了一个构造。例如:您将 API 网关分离成自己的构造。它需要一个桶来存储用户的图像。这个桶是用 KMS 密钥加密的，并且有一些带条件的复杂策略，所以您将它放入自己的构造中。存储桶的名称必须是 fix，因为您需要授予另一个帐户(b)中的角色跨帐户访问权限，因此不能在堆栈之间轻松共享存储桶名称。角色需要有 bucket 的名称，以便只授予对这个特定 bucket 的访问权限([授予最小特权](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege))。那么……在哪里存储桶名呢？

下面，我将向你展示我试图克服这个问题的方法，以及它们的缺陷和我今天正在使用的方法。然而，我还没有一个超级的，复杂的解决方案，所以请看看这个更多的建议。我们都还在学习如何使用 CDK。

*为简单起见，我只关注桶。

## 首先，在堆栈/构造的属性中定义存储桶名称

一种解决方案是扩展堆栈的构造器(或 props ),并通过构造传递 bucket 名称，直到您需要它。那么它是如何工作的:

应用程序

这很简单。您会看到 bucket 名称是在哪里定义的，以及它最终在哪里被使用。然而，这只是一个简单的用例，在现实世界的应用程序中，您有大量的配置，比如存储库名称、域名、存储库所有者(Github)等等。接口会很快变得非常冗长，当添加或删除新内容时，您必须适应几个地方。这可能是一个烂摊子。

## 第二，在 cdk.json 中定义 bucket 名称

另一个解决方案是使用作为[运行时上下文](https://docs.aws.amazon.com/cdk/latest/guide/context.html)公开的`cdk.json`，并通过使用`this.node.tryGetContext()`来使用 bucket 名称。那么代码看起来是什么样的呢:

cdk.json

应用程序

我省略了`StackA`和`ApiConstruct`，因为它们不再需要任何改编。正如您所看到的，我们用这个解决方案克服了冗长的接口。然而，我们不容易看到在哪里使用了 bucket 名称，必须手动搜索它。如果我们在某些情况下需要替换配置，比如部署到不同的阶段，该怎么办？我们不能在一个应用程序中有几个`cdk.json`文件。我们也没有合适的[智能感知](https://code.visualstudio.com/docs/editor/intellisense)，因为`tryGetContext()`不知道底层的`cdk.json`。这让我想到了第三种方法。

## 3 通过使用自定义配置来定义存储桶名称

我今天使用的解决方案是一个自定义配置和一个加载配置的类。一切都是从那里进口的。同样，代码看起来像什么:

配置. json

配置文件

应用程序

看起来像以前的解决方案，但现在我们可以交换配置，并拥有适当的智能感知。像 VS Code 这样的现代 ide 可以向你展示某个东西在哪里被使用([见](https://code.visualstudio.com/docs/getstarted/tips-and-tricks#_go-to-references))，因此我们不需要手动搜索它。我不知道这是否是最好的方法。不过，用起来感觉还是不错的。我还没有在一个超级大的应用程序中尝试过，如果我这样做了，我会更新这篇文章。

你的方法是什么？

//BMA