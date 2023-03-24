# 早期创业的理想软件+ DevOps 堆栈

> 原文：<https://levelup.gitconnected.com/the-ideal-software-devops-stack-for-an-early-stage-startup-bd099e99247>

我们将在这里讨论的堆栈将适用于许多创业公司，同时相对简单的实施和保持低成本。

![](img/2ab94826a5b2f0a081faf9df56929ef0.png)

Med Badr Chemmaoui 在 [Unsplash](https://unsplash.com/photos/ZSPBhokqDMc) 拍摄的照片

# 介绍

在我与初创公司合作的这些年里，我犯了足够多的架构错误，以至于对不同阶段和不同类型的公司什么行得通，什么行不通有很好的感觉。经常很容易让自己被前沿的新奇事物吸引。当我在裸机上运行的 [OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift) 上为我的第一个创业公司的堆栈实施基于微服务的架构时，我就遇到了这种情况。**除非有非常充分的理由，否则不要这样做。**

在这里，我们将介绍一个简单的架构和支持工具，对于大多数电子商务、金融科技、移动应用等初创公司来说，这些工具应该足够了。

# 太长；没读过(TLDR)

*   基础设施:亚马逊网络服务(AWS) +弹性豆茎
*   软件栈:Ruby on Rails+react js/react native(带类型脚本)
*   存储:S3 +极光的 PostgreSQL + Redis
*   CDN:云锋
*   CICD:代码管道+ Github 操作(用于非 AWS 部署)
*   分析:哨兵+ Hotjar/Smartlook +分支

# 哪个基础设施提供商？

有几个基础架构即服务提供商。最常见的是亚马逊网络服务(AWS)、谷歌云平台(GCP)和 Azure。

这些平台各有利弊，我根据项目需求使用所有三个(和其他)平台。然而，如果你是一家初创公司，我建议你坚持使用 AWS，理由如下:

1.  [AWS 激活计划](https://aws.amazon.com/activate/)为自筹资金的初创公司提供 10000 美元的信贷，为风投支持的初创公司提供高达 100000 美元的信贷。1000 美元的信贷只需要填写一份在线申请，几天内就会做出决定。此外，如果你加入免费的创业学校，你还可以获得额外的 5000 美元的学分。
2.  作为该领域的先行者，AWS 成功创建了最大的开发人员社区，这使得招聘具备 AWS 知识的工程师变得更加容易。

# 在哪里运行我的代码？

这是我们工程师喜欢去的地方。托管后端有许多选择和排列。你可以买一个服务器，直接在上面运行代码。甚至可以添加[open stack](https://www.openstack.org/)+[open shift](https://www.redhat.com/en/technologies/cloud-computing/openshift)在办公室的集群上运行托管容器。或者你可以走另一个极端，使用 [Heroku](https://www.heroku.com/) 为你管理一切，只需从 Git 简单推送。

然而，作为一家初创公司，我推荐几个指导原则:

1.  简单:选择设置和维护时间更短的方法。每当你倾向于你的 DevOps，你没有创造新的功能，没有建立价值。
2.  低成本:当您有 1，100 或 1000 个每日活跃用户(DAU)时，您不需要服务器集群和专用的流程编排服务器。延伸你的跑道，把你的钱投资到能带来价值的地方。与此同时，要小心基于使用的计费方式，它可能会突然激增。
3.  多才多艺:你是创业公司。能够快速旋转是必须的。因此，选择一个限制你实现在不久的将来很可能需要的新事物的架构是不理想的。

基于以上原则，我推荐使用[弹性豆茎(EB)](https://aws.amazon.com/elasticbeanstalk/) 。请注意，其他云提供商也提供类似的服务，如 [GCPs 托管实例组(MIG)](https://thepaulo.medium.com/a-complete-guide-to-deploying-a-containerized-application-using-managed-instance-groups-migs-in-2593c0819ab2) ，但我们坚持使用基于上一节的 AWS。

原因很简单:

1.  EB 运行在一个 VPC 内，在那里你也可以分配你的其他资源，以确保一定程度的隔离(安全)从公共互联网。
2.  它自动管理部署、实例故障和所需的更新，因此您不必担心这些问题。
3.  与更多的交钥匙解决方案不同，您可以共同托管应用程序，例如运行异步作业的辅助服务(稍后将详细介绍)。
4.  它根据您需要的服务器数量和规模提供可预测的价格(大多数初创公司只需一个 T3 即可运行。每个环境中的中型或小型实例)。

# 我应该使用什么软件堆栈？

有很多不同的语言和框架。每一个都是为了解决特定的用例而开发的，然后扩展为更多的用例服务。在许多情况下，创业公司使用的最佳堆栈可能是您的团队最熟悉的。然而，如果选择仅仅基于:

1.  开发速度:小团队应该能够快速迭代和添加新功能。
2.  低成本:没有付费或专有的框架。
3.  稳定:没有实验性的、最近发布的和支持差的栈。专注于产品。如果你的创业成功了，你可以分配整个团队去探索未来。

对于后端，使用 Ruby on Rails。这是一个久经考验的框架，非常固执己见，允许你专注于特性而不是基础。

对于前端，ReactJS/ReactNative + Typescript 允许您在所有主要平台上使用非常相似的代码来运行您的应用程序。它经过了脸书的考验，有一个繁荣的社区，有很多开源模块。

# 在哪里保存数据？

## 大型对象(图像、视频、二进制文件等)

推荐的解决方案是使用[自动气象站 S3](https://aws.amazon.com/s3/) 。它是可伸缩的、可靠的、简单的、通用的，并且与其他 AWS 资源很好地集成，例如当一个新的对象被上传时能够触发一个 Lambda。在视频处理中非常有用的功能，如下文所示:

[](https://betterprogramming.pub/storing-and-encoding-videos-with-ruby-on-rails-lambda-and-s3-3de934b2897e) [## 用 Ruby on Rails、Lambda 和 S3 存储和编码视频

### 向您的新应用添加视频上传和处理功能的简单而可扩展的方法。

better 编程. pub](https://betterprogramming.pub/storing-and-encoding-videos-with-ruby-on-rails-lambda-and-s3-3de934b2897e) 

如果没有必要或没有可能需要与其他 AWS 资源集成 [Wasabi](https://wasabi.com/) 可能是一个很好的 API 兼容的替代方案，成本更低。但是，不要为了每月节省 50%的 10 美元而更换提供商，而不得不为此学习新的界面。当您扩展时，您可以随时迁移。

## 小对象(元数据等)

这可能是一个更有争议的话题。在处理用户数据、应用程序数据等的存储时，我们通常会谈到拥有一个数据库。数据库有很多种类型，每种类型都有几种风格。

首先，要在 noSQL 或关系数据库之间做出选择。在一些情况下，noSQL 数据库是更好的选择，但是，对于大多数早期创业公司来说，关系数据库更合适，原因如下:

1.  无需担心数据的一致性。在一个 [ACID](https://www.keboola.com/blog/acid-transactions) 兼容的 DB 中，这是一个保证。
2.  它可以与已经建立并经过实战检验的框架无缝集成，比如 Ruby on Rails、Django 等。
3.  一个固定的模式！在一家初创公司，事情会变化得非常快。不必在代码中添加安全的访问器和其他保护措施，拥有一个具有可靠迁移的一致模式将使工程更不容易出错。

我不仅会推荐一个普通的 SQL 数据库，而且会更具体地推荐使用亚马逊 Aurora 的 PostgreSQL，理由如下:

1.  他们有一个价格可预测的无服务器版本(您可以锁定大小)。
2.  即使没有读取副本，它也会在 6 个存储卷上镜像数据，因此不太可能发生数据丢失。
3.  它是自我管理的，因此不需要专门的工程资源来保持其运行。

## 临时数据

您可能需要存储短暂的数据，比如来自查询和 API 的缓存结果。为此， [Redis](https://redis.io/) 和 AWS 为其管理的解决方案，[elastic cache](https://aws.amazon.com//redis/)将会运行良好。

# 如何减少加载时间？

当提供静态资产(甚至是可以缓存的公共 API 结果)时，通过在边缘(靠近用户)缓存结果来减少服务器上的延迟和负载是非常有用的。为此，您可以使用内容交付网络(CDN)。

AWS 提供的 CDN 名为 [CloudFront](https://aws.amazon.com/cloudfront/) ，与 S3(用于大型对象存储)和 elastic beanstalk(用于 API)无缝集成。下面的例子集成了 S3 + CloudFront 来服务一个静态网站:

[](https://thepaulo.medium.com/continuous-deployment-pipeline-for-react-app-on-aws-s3-cloudfront-ac60f455642a) [## AWS S3 + CloudFront 上 React 应用的持续部署渠道

### 简单回顾了如何为单页面应用程序(ReactJS)创建一个连续的部署管道，而不需要…

thepaulo.medium.com](https://thepaulo.medium.com/continuous-deployment-pipeline-for-react-app-on-aws-s3-cloudfront-ac60f455642a) 

# 如何自动化部署？

启动开发的速度对于您拥有一个自动部署持续更新的系统至关重要。

根据您正在开发的内容，这里有几个选项。

1.  对于将应用部署到 iOS 应用商店或 Android Play 商店，我建议使用 [Github Actions](https://thepaulo.medium.com/a-simple-continuous-deployment-of-a-react-native-app-to-google-play-bda85eaa954c) 。这也适用于定制场景，比如部署一个 [Zendesk 应用](https://thepaulo.medium.com/continuous-deployment-for-zat-app-983b072bf132)。
2.  要将后端代码部署到 Elastic Beanstalk，请使用 AWS 的内置[代码管道](https://aws.amazon.com/codepipeline/)。

# 如何监控使用情况？

## 崩溃和错误

[Sentry](https://sentry.io/) 是一个很棒且易于集成的解决方案，用于监控前端和后端的错误。它还提供性能监控。

## 使用

收集数据以便从用户行为中学习对任何初创公司都是至关重要的。

在早期阶段，当你的用户数量为个位数时，使用 [HotJar](https://hotjar.com/r/r75aa7d) 开发网络应用，或者使用 [SmartLook](https://www.smartlook.com/) 开发移动应用。这将为您提供每个用户会话的记录，您可以了解他们在使用您的应用程序时看什么或做什么。

一旦你有了合理数量的用户，聚集数据就变得很重要。其中一个最好的平台是[振幅](https://amplitude.com/)。它还允许您创建仪表板来跟踪您的 KPI。

## 属性

在早期阶段(以及后期阶段)了解你的用户来自哪里是很重要的。你可能有一个推荐流程，有影响力的活动，或者只是有广告。

跟踪用户何时点击打开 app store 的链接，然后下载应用程序并在其中注册帐户。您可以使用一种称为延迟深度链接的技术。为您提供开箱即用的平台之一是 [Branch](https://branch.io/) 。

# 结论

开发任何工程系统都有许多正确的方法。众多的选择往往会导致决策瘫痪，或者长时间探索不同的产品和服务，直到选定某样东西。然而，创业公司在短时间内有很多事情要做。

为了缩短周期，我在这里提出了一个技术堆栈，它在我之前的几个项目中发挥了很好的作用:

*   基础设施:亚马逊网络服务(AWS) +弹性豆茎
*   软件栈:Ruby on Rails+react js/react native(带类型脚本)
*   存储:S3 +极光的 PostgreSQL + Redis
*   CDN:云锋
*   CICD:代码管道+ Github 操作(用于非 AWS 部署)
*   分析:哨兵+ Hotjar/Smartlook +分支

*希望这有所帮助！如果你有任何问题或者只是想聊聊创业、创业、软件开发或工程，请给我发电子邮件保罗@*[*avantsoft.com.br*](https://avantsoft.com.br/)*或者在这里留下评论。*

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随: [Twitter](https://twitter.com/gitconnected) ， [LinkedIn](https://www.linkedin.com/company/gitconnected) ，[迅](https://newsletter.levelup.dev/)
升一级就是转型科技招聘👉 [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)