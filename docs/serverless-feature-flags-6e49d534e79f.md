# 无服务器功能标志🚀

> 原文：<https://levelup.gitconnected.com/serverless-feature-flags-6e49d534e79f>

![](img/f82348ae8ba9157a3f166a6fd810be80.png)

保罗·花冈在 [Unsplash](https://unsplash.com/s/photos/config?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当在 AWS Cloud 上的无服务器环境中使用配置时，您通常有几个选项来存储和访问您的配置，尤其是在使用动态特性标志时。这篇博客文章讨论了使用 [AWS AppConfig](https://docs.aws.amazon.com/appconfig/latest/userguide/what-is-appconfig.html) 和 [AWS AppConfig Lambda 扩展](https://docs.aws.amazon.com/appconfig/latest/userguide/appconfig-integration-lambda-extensions.html)的优势，以便在没有第三方服务的情况下获得[特性标志](https://martinfowler.com/articles/feature-toggles.html)的能力。基本代码回购可以在[这里](https://github.com/leegilmorecode/serverless-feature-flag)找到。🙇‍♂️

# 首先，什么是“特征标志”？

Martin Fowler 的一篇文章中讨论了特征标志/切换:

> *特性切换(*通常也被称为特性标志*)是一种强大的技术，允许团队在不改变代码的情况下修改系统行为。它们分为不同的使用类别，在实现和管理切换时，将这种分类考虑进去是很重要的。*

当然，有许多第三方软件提供商提供功能标志服务，其中主要的是[黑暗启动](https://launchdarkly.com/?utm_source=google&utm_medium=cpc&utm_campaign=&utm_terms=launchdarkly&matchtype=e&utm_content=523657237397&gclid=CjwKCAjww-CGBhALEiwAQzWxOiyCdxf3gRZjZJCFv5lur6IEf-Gxw_H5qD4ylinogDB91ICvYP3mGhoCge4QAvD_BwE)。本文讨论了如何使用[无服务器框架](https://www.serverless.com/)和本地 AWS 服务来实现这一点。

特性标志通常用于在代码中动态地打开或关闭某个特性，以提高开发速度并降低部署风险，因此您可以独立地部署代码和启用/禁用该特性。一些使用案例包括:

*   面向客户的新功能。
*   重构现有的业务逻辑。
*   一个新的非面向客户的功能。

这通常允许团队执行 A/B 测试和新特性的逐步部署，以在完全部署之前获得生产信心。

它使回滚变得非常简单，无需重新部署代码/基础架构，降低了取消部署的风险。它还允许团队随着时间的推移在产品中部署不完整的功能(尤其是在各种无服务器服务上)，并在小版本的基础上做出数据驱动的决策，而不是大爆炸的方法。

# AWS AppConfig 有什么帮助？

通常，在访问 lambdas 中的配置数据时，您将执行以下操作之一:

1.  使用无服务器框架本机`[ssm:](https://www.serverless.com/framework/docs/providers/aws/guide/variables#reference-variables-using-the-ssm-parameter-store)`引用变量调用在**构建时**从参数存储中获取配置。这种方法的问题是配置是静态的(*在包构建时*嵌入)，虽然可能适用于很少改变的配置，但对于特性标志来说不是很好。
2.  使用参数存储，但是每次 lambda 通过代码调用时都提取配置(**无缓存**)。现在配置不再是静态的；然而，每次从 AWS 参数存储调用时获取配置的成本很高。
3.  如上所述，但是使用 S3 来存储配置细节。同样，这意味着您需要在每次调用时从 S3 读取数据。在我看来，S3 的配置与参数存储相比也不容易更改。
4.  在最基本的情况下，您可以在 lambda 中使用环境变量，但是这些变量容易出现人为错误，没有对已更改内容的审核，不容易在控制台之外进行更改，并且在更改值时容易出现人为错误。

## AppConfig Lambda 层扩展拯救！

AWS AppConfig 允许您将配置存储在以下四个位置之一:

1.  [AWS 参数存储](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)。
2.  AWS AppConfig 托管。
3.  [AWS S3](https://aws.amazon.com/s3/) 。
4.  [AWS 代码管道](https://aws.amazon.com/codepipeline/)。

同时还允许您创建一个具有一组配置和部署的应用程序，允许团队非常容易地将更新的配置部署到一组目标上(*以及恢复到先前的部署配置*)。

AppConfig Lambda 扩展通过使用 [Lambda 扩展](https://docs.aws.amazon.com/lambda/latest/dg/using-extensions.html)，使得 Lambda 和 AppConfig 之间的集成无缝。AppConfig Lambda 扩展将自动为你的配置和运行中的 Lambda 一起构建一个缓存，只从 AppConfig 中按可配置的时间顺序提取最新的配置。这意味着配置可以在所有被调用的 lambda 函数中非常快速地更新，没有上一节详述的开销和限制，也不需要重新部署任何代码！

AWS 将 lambda 扩展服务描述为(【2020 年 10 月 8 日):

> AWS Lambda 宣布了 Lambda 扩展的预览版，这是一种将 Lambda 与您喜欢的监控、可观察性、安全性和治理工具轻松集成的新方法。在这篇文章中，我将解释 Lambda 扩展是如何工作的，如何开始使用它们，以及 AWS Lambda Ready 合作伙伴提供的扩展。
> 
> 扩展有助于解决客户的一个常见请求，使他们现有的工具更容易与 Lambda 集成。以前，客户告诉我们，将 Lambda 与他们喜欢的工具集成需要额外的操作和配置任务。此外，像日志代理这样的工具是长时间运行的进程，不容易在 Lambda 上运行。
> [**https://AWS . Amazon . com/blogs/compute/introducing-AWS-lambda-extensions-in-preview/**](https://aws.amazon.com/blogs/compute/introducing-aws-lambda-extensions-in-preview/)

# 好吧，给我看看代码！😜

下面的`serverless.yml`片段显示了通过使用 AppConfig 和 Lambda 扩展添加特性标志的基本配置，以及第一个配置值:

正如您在上面的文件中看到的，AppConfig Lambda 扩展是通过 Lambda 层添加的，参考资料部分有 AppConfig 的应用程序、部署策略和配置概要文件的构建(*包括配置本身*)。这个 JSON 对象可以保存特定应用程序的所有特性标志，本质上是一组布尔值。

在实际的 lambda 处理程序中，我们现在可以从与 lambda 一起本地运行的扩展中访问配置( *localhost* )，这在下面的基本版本中显示:

上面的代码显示了从扩展调用时在缓存中本地拉取最新的配置，在`serverless.yml`文件中的配置意味着每 30 秒钟检查一次 AWS AppConfig 服务的更新。

您可以使用该值来决定是否启用该特性，而不是在响应中记录并从 API Gateway 返回该值。

配置也可以直接从 CodePipeline 中提取，这意味着实际配置值的修改可以在 CI/CD 管道中完成，甚至不需要进行无服务器部署。

# 后续步骤..

创建一个[中间件](https://github.com/middyjs/middy)函数来从本地主机拉取配置是非常直接的，如上所示，它可以以可重用的方式默认添加到任何 lambda 函数中(*可以打包后从 NPM 安装，或者 monorepo* )。

这意味着您可以从中间件的返回值中析构配置，并且只使用它，如果它已经被提供的话。

在`serverless.yml`文件的资源部分创建一个用于生成[云生成](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_AppConfig.html)的[无服务器插件](https://www.serverless.com/plugins/)也非常简单(**我将把它添加到我的待办事项列表中！**)🤓

如果你是一个组织，认为与使用第三方产品相比，上述方法可以节省大量资金，那么使用 lambda 上的 [AWS SDK](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/AppConfig.html) 创建一个带有 API 的 web 客户端并不困难，可以创建一个小型的内部产品，与服务提供商的功能相当。(**我想我会为了好玩创建一个免费开源版本，可以部署到任何 AWS 账号！**)😎

# 包扎

如果你喜欢阅读这篇文章，让我们联系以下任何一个:

[https://www.linkedin.com/in/lee-james-gilmore/](https://www.linkedin.com/in/lee-james-gilmore/)T17[https://twitter.com/LeeJamesGilmore](https://twitter.com/LeeJamesGilmore)

如果你觉得这些文章鼓舞人心或有用，请随时用虚拟咖啡[https://www.buymeacoffee.com/leegilmore](https://www.buymeacoffee.com/leegilmore)来支持我，不管怎样，让我们联系和聊天吧！☕️

如果你喜欢这些帖子，请关注我的简介[李·詹姆斯·吉尔摩](https://medium.com/u/2906c6def240?source=post_page-----39c4f4ae5aff----------------------)以获取更多的帖子/系列，不要忘记联系我并问好👋

**本文由** [**Sedai.io**](https://www.sedai.io/) 赞助

![](img/4c77bc2d275bd557dda90aa2fe842d2e.png)

# 关于我

**"** *大家好，我是 Lee，英国的 AWS 认证技术架构师和多语言首席软件工程师，是一名技术云架构师和无服务器主管，过去 5 年来主要从事 AWS 上的全栈 JavaScript 工作。*

*我认为自己是一个热爱 AWS、创新、软件架构和技术的无服务器布道者。*

******** 所提供的信息是我个人的观点，我对这些信息的使用不承担任何责任。**