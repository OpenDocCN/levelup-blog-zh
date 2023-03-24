# 企业无服务器🚀安全性

> 原文：<https://levelup.gitconnected.com/enterprise-serverless-security-170d1aeefc22>

![](img/3fdaf4541433e44abf05e8a436062be5.png)

[https://unsplash.com/@brizmaker](https://unsplash.com/@brizmaker)

从我自己的角度来看，企业无服务器系列中的这一部分专门讨论了无服务器应用程序的一些安全方面和工具。你可以点击下面的“*系列介绍*链接阅读该系列背后的想法。

该系列分为以下几个部分:

1.  [系列介绍](https://medium.com/@leejamesgilmore/enterprise-serverless-series-358be237b510)🚀
2.  [工装](https://medium.com/@leejamesgilmore/enterprise-serverless-tooling-aa2b84d932de)🚀
3.  [建筑](https://medium.com/@leejamesgilmore/enterprise-serverless-architecture-39c4f4ae5aff)🚀
4.  [数据库](https://leejamesgilmore.medium.com/enterprise-serverless-databases-208b8790998)🚀
5.  [提示&提示](https://medium.com/@leejamesgilmore/enterprise-serverless-hints-tips-32a41cdabc0d)🚀
6.  [AWS 限制&限制](https://medium.com/@leejamesgilmore/enterprise-serverless-aws-limits-limitations-ce99e20432be)🚀
7.  **安全**🚀
8.  [有用的资源](https://medium.com/@leejamesgilmore/enterprise-serverless-useful-resources-cd8e987f6d69)🚀

# 安全性

以下是我个人根据之前的大型云项目想到的一些关键领域，但不限于此(*我会在想到这些领域时添加更多内容*):

## API 验证✅

在大规模的无服务器项目中，提前验证传入的 API 请求非常重要，因为这通常是针对不良参与者和攻击者的面向公众的 API(在发布/过度发布/不良有效载荷等下*)。通常使用的 AWS 无服务器服务有[AWS API Gateway](https://aws.amazon.com/api-gateway/)(*REST*)和[AWS app sync](https://aws.amazon.com/appsync/)(*graph QL*)。*

**AWS AppSync**

对于 AWS AppSync，我通常为管道中的每个端点添加一个 validate 方法，例如在一个虚构的“get customer”查询中:

*映射模板*

```
{
   type: 'Query',
   field: 'getCustomer',
   request: resolve(
'src/customer/resolvers/pipelines/Before.req.vtl'
   ),
   response: resolve(
   'src/customer/resolvers/pipelines/After.res.vtl'
   ),
   kind: 'PIPELINE',
   functions: ['**validateGetCustomer**', 'getCustomer'],
},
```

*验证获取客户(基本示例)*

```
#set($errors = [])
#set($valid = $util.matches("${*customerId*}", $ctx*.args.customerId*))
#if (!$valid)
   #if ($util.isNullOrEmpty($ctx*.args.customerId*))
      #set($propertyRequiredError = "${propertyRequiredError}")
      $util.qr($errors.add($propertyRequiredError.replace("{0}", "Customer ID")))
   #else
      #set($propertyNotValidError = "${propertyNotValidError}")
      $util.qr($errors.add($propertyNotValidError.replace("{0}",
      $ctx*.args.customerId*).replace("{1}", "customer ID")))
   #end
#end
#if ($errors.size() > 0)
   $utils.error($util.toJson("${validationError}"), null, null, $errors)
#end
{}
```

这确保我们在传递到管道的下一部分(可能是 DynamoDB 或 Lambda 解析器)之前，使用正则表达式、长度、类型等预先验证请求的有效负载和/或参数。GraphQL 通常会在调用 API 之前提供一些基本的预验证，但你不能依赖它。

> *💡*我通常使用管道的 before 步骤来设置相关 id(共享 VTL 文件)，如果需要，使用 after 步骤来进行任何 post 错误转置。

**AWS API 网关**

通过 API Gateway，您可以使用 JSON 模式和请求验证器预先执行[基本请求验证](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-method-request-validation.html)，尽管在选择与 lambda 的[集成](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-integration-types.html)类型时，这可能是一个雷区——值得花一些时间尝试每种类型，以了解它们如何工作以及什么适合您的解决方案(*这是一篇独立的文章*)。

## 我是最小的特权

当谈到在 AWS 上构建任何云服务时，我认为一个基本的安全任务是确保特定的服务只有 IAM 权限来完成手头的任务(**仅此而已！**)。

例如，如果您允许 lambda 将消息放入一个队列，请确保您绑定了 lambda 角色，以便它只能将消息添加到特定的队列，而不是帐户中的每个 SQS 队列。

📖***对于 Lambda 函数，建议您遵循最低特权访问，并且只允许执行给定操作所需的访问。赋予一个角色过多的权限可能会导致系统被滥用。在安全上下文中，使用较小的函数来执行限定范围的活动有助于构建更好的无服务器应用程序。关于 IAM 角色，在多个 Lambda 函数中共享一个 IAM 角色可能会违反最低特权访问。***—**AWS 无服务器应用镜头******

****一个真实的例子可以是 [AWS AppSync](https://aws.amazon.com/appsync/) 中的 [DynamoDB](https://aws.amazon.com/dynamodb/) 解析器，其中与数据源相关联的服务角色只具有特定表的 DynamoDB 策略和所需的动作类型(*查询、扫描、更新等*)。****

****这可能是用于查询特定订单的 GraphQL 端点，例如:****

*****数据来源*****

```
****{
   type: 'AMAZON_DYNAMODB',
   name: '**OrderDataSource**',
   description: 'Order Data Source',
   config: {
      tableName: {
         'Fn::ImportValue': '**order-table-${self:custom.prefix}**',
      },
      serviceRoleArn: {
         'Fn::GetAtt': ['**OrderServiceRole**', 'Arn'],
      },
   },
}****
```

*****服务角色*****

```
******OrderServiceRole**: {
   Type: 'AWS::IAM::Role',
   Properties: {
   RoleName: '${self:custom.prefix}-order-sr',
   AssumeRolePolicyDocument: {
      Version: '2012-10-17',
      Statement: [
      {
         Effect: 'Allow',
         Principal: {
         Service: ['appsync.amazonaws.com'],
      },
         Action: ['sts:AssumeRole'],
      }],
   },
   Policies: [
      {
         PolicyName: '${self:custom.prefix}-order-sr-policy',
         PolicyDocument: {
         Version: '2012-10-17',
         Statement: [
         {
            Effect: 'Allow',
            **Action: ['dynamodb:Query'],**
            Resource: [
            {
               'Fn::ImportValue':
               **'order-table-${self:custom.prefix}-arn'**,
            },...****
```

****这确保了在本例中，当[数据源](https://docs.aws.amazon.com/appsync/latest/APIReference/API_DataSource.html)链接到[功能配置](https://docs.aws.amazon.com/appsync/latest/APIReference/API_FunctionConfiguration.html)时，GraphQL [解析器](https://docs.aws.amazon.com/appsync/latest/APIReference/API_Resolver.html)在本例中仅作为在特定*订单*表上执行手头的特定*查询*任务所需的权限；而且不能再做了(*不慎损坏*)。****

> *****💡*在上面这个具体的例子中，您在大型项目中可能面临的一个问题是，在具有这种细粒度访问级别的帐户上，IAM 角色不够用——限制可以在这里[找到](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-quotas.html)。假设您的解决方案中有 100 个单独的 GraphQL 端点，20 名开发人员在短期环境中工作，那么在任何给定的时间点都有 2K 个潜在的 IAM 角色(不包括您的解决方案中的其他角色)。****

## ****AWS 白皮书和资源****

****我个人觉得以下几点很有用:****

1.  ****[AWS 概述中的安全性、身份和合规性](https://aws.amazon.com/products/security/?nc=sn&loc=2)****
2.  ****[AWS 无服务器应用镜头白皮书](https://d1.awsstatic.com/whitepapers/architecture/AWS-Serverless-Applications-Lens.pdf)****
3.  ****[AWS 安全支柱白皮书](https://d1.awsstatic.com/whitepapers/architecture/AWS-Security-Pillar.pdf)****

## ****运行时和包版本****

****任何大型云项目的一个关键方面是确保您的包、项目依赖项和运行时版本没有任何已知的漏洞。****

****AWS Lambda 的伟大之处在于 Amazon 管理运行时的底层补丁；然而，如果您使用容器，例如使用 [AWS Fargate](https://aws.amazon.com/fargate/) ，您将需要自己管理基本映像版本的使用。****

****还必须使用开源解决方案将包和项目依赖项作为 CI/CD 管道的一部分进行扫描，以使团队意识到任何安全漏洞，从而可以减轻它们。****

## ****不要记录秘密！****

****说到记录，一定不要记录秘密或个人身份信息，如电子邮件地址或银行信息，因为这是一个坏演员的金矿😈。这也可能违反您公司的政策、GDPR 或您为客户提供服务的条款和条件。****

****将 [AWS AppSync](https://aws.amazon.com/appsync/) 与[server less-app sync-plugin](https://github.com/sid88in/serverless-appsync-plugin)一起使用的一个例子是根据您部署到的阶段来更改您的 serverless YAML/JSON 中的日志记录级别:****

```
****logConfig:
      loggingRoleArn: { Fn::GetAtt: [AppSyncLoggingServiceRole, Arn] } # Where AppSyncLoggingServiceRole is a role with CloudWatch Logs write access
      level: ERROR # Logging Level: NONE | ERROR | ALL
      excludeVerboseContent: false****
```

****这张地图通过云层映射到之后的[。我倾向于根据我们是处于暂时/QA/PP 还是 Prod 来切换日志记录的详细程度和级别，因为之前在默认情况下，我们可以在 AppSync CloudWatch 请求日志中看到记录的不记名令牌..🙈在短暂的环境和 QA 中，额外的日志记录可能对调试有用。](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-appsync-graphqlapi-logconfig.html)****

****📖***"*** *我们严格建议不要发送、记录和存储未加密的敏感数据，无论是作为 HTTP 请求路径/查询字符串的一部分还是在 Lambda 函数的标准输出中。***—**AWS 无服务器应用镜头********

## ******vpc 配置******

******在处理大型无服务器云项目时，您可能需要将 lambda 等资源连接到驻留在 [VPC](https://aws.amazon.com/vpc/) ( *例如 RDS 或 DocumentDB* )中的 AWS 服务。在这种情况下，正确设置入站和出站流量规则等非常重要。******

## ******限速******

******根据客户的使用情况在 API 上正确设置速率限制是任何大型项目中的一项重要任务，不应被遗忘，这可以在基础设施的各个级别完成( [*API 网关*](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html#apigateway-how-throttling-limits-are-applied)*/*[*AWS WAF*](https://aws.amazon.com/waf/)*/CloudFront/*[*AWS app sync*](https://www.serverless.com/aws-appsync)*/cloud flare 等*)，并且可以防止拒绝服务和[钱包](https://www.cequence.ai/use-cases/denial-of-wallet/#:~:text=Denial%20of%20Wallet%20attacks%20typically,an%20application%20denial%20of%20service.)******

> *******💡*在之前的 AWS AppSync 项目中，我就默认速率限制联系了 AWS，因为它在早期的文档中没有指定。现在是[现在是](https://docs.aws.amazon.com/general/latest/gr/appsync.html)，设置为整个账户每秒 1000 个请求，尽管可以通过与亚马逊对话来增加。******

## ******AWS 帐户上的 MFA 所有团队成员始终！******

******创建新的 AWS 帐户时，您应该做的首要任务之一是确保您将 MFA 作为标准来启用，但这不仅仅止于管理员设置它，这应该在项目和帐户的整个生命周期中对所有团队成员强制执行！******

******您的安全性取决于您最薄弱的环节——一名未启用 MFA 的团队成员被黑客攻击会向黑客公开您的所有资产——以及通过使用您的帐户进行流氓活动来拒绝钱包攻击等，无论团队的其他成员有多严格。😈******

## ******S3 铲斗配置******

******检查您的“私有”存储桶是否是公共的…🙏🏼******

## ******连续威胁建模******

******在之前的大规模云项目中，我管理过的一个工作出色的关键领域是在整个项目中持续进行[威胁建模](https://martinfowler.com/articles/agile-threat-modelling.html)，即在开发阶段以及上线后服务的任何部分发生重大变化时。******

******这种方法之所以行之有效，是因为根据我的经验，这通常是一项留到项目结束时，在完全安全签核之前完成的任务——对于大型项目来说，这可能是一项艰巨的任务，因为项目结束时需要一次完成许多活动部分。******

******与项目结束时的大量潜在返工相比，在当时(*之前或期间*)在该代码区域工作时减少任何问题也是有益的。它还让安全部门有信心知道您在整个 SDLC 过程中都将这一点融入到您的流程中。******

## ******预算******

******在 AWS 控制台中为您的帐户设置预算，以便在成本由于某种原因开始上升时得到主动提醒..******

## ******静态和传输中的加密******

******存储客户数据时，考虑静态和传输中的数据加密非常重要。存储静态加密数据的很好的例子有 [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html) 、 [AWS DocumentDB](https://docs.aws.amazon.com/documentdb/latest/developerguide/encryption-at-rest.html) 、 [AWS Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Overview.Encryption.html) 、 [AWS Elasticsearch](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/encryption-at-rest.html) 、 [AWS 参数存储](https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html)和 [AWS S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html) 等等。******

******那么，为什么要加密静态数据呢？本质上，在基本层面上，这意味着当数据被存储时，使用加密密钥将其加密成不同的格式，因此在数据泄露中变得无用(*,当然，除非攻击者能够通过某种方式解密数据*🔐 ).******

******传输中的数据本质上是在服务之间传输的“实时数据”。许多 AWS 服务，如 AWS EFS、AWS Glue 和 AWS Redshift，允许默认配置来防止窥探、访问或修改传输中的数据。默认情况下，许多服务使用 SSL/TLS 进行通信。******

## ******早期渗透测试******

******在大规模云项目的开发阶段运行 [Burp Suite](https://portswigger.net/burp) 等工具，可以像上述威胁建模一样，防止项目结束时的返工，因为根据我的经验，在项目结束时让专业外包公司运行渗透测试之前，您可以在早期发现大多数问题，并通过开发工作缓解这些问题。这可能包括解决围绕速率限制、响应头等的问题——根据我的经验，解决一个端点的问题通常会解决所有端点的问题👍******

> *******💡*通过查看特性文档，BurpSuite Pro 可以作为您的 [CI 集成的一部分运行](https://portswigger.net/burp#ci-integration)，尽管这不是我迄今为止在项目中做过的事情——这是我需要研究的事情。(在项目工作的不同阶段，总是在管道之外手工完成)******

## ******负载测试******

******说到对你的系统的信心，我认为必须在负载下测试，利用预期数字加上未来增长，并考虑季节性高峰(*销售、假期等)*。如果可能的话，在项目的早期进行测试也是有益的，这样可以避免如上所述的返工。根据我的经验，安全问题通常可以在负载测试和设置速率限制时发现，以确保攻击者无法使您的系统停止运行。******

******在使用 API 时，有许多工具可以使用，例如 JMeter 或[火炮](https://artillery.io/)；但是测试下游流程或后端集成也很重要，它们不是面向公众的；也许是使用队列或由 CloudWatch 事件触发的批处理的集成，以确保它们也像预期的那样在高负载下工作。******

> *******💡*负载测试的结果可能会显示峰值负载下或事件驱动系统中的客户响应时间，例如，当异步流程完成且向客户发送电子邮件时。在我看来，它不仅仅需要考虑 API 在什么样的负载下变得没有响应..一切都是为了客户体验。******

## ******结论******

******关于安全性，我要说的一件事是，在我看来，这是团队中每个人的职责，需要从项目的第一天开始考虑，并且贯穿始终。******

********下一节:** [有用资源](https://medium.com/@leejamesgilmore/enterprise-serverless-useful-resources-cd8e987f6d69)🚀
**上一节** : [AWS 限制&限制](https://medium.com/@leejamesgilmore/enterprise-serverless-aws-limits-limitations-ce99e20432be)🚀******

# ******包扎******

******让我们连接以下任何一项:******

******[https://www.linkedin.com/in/lee-james-gilmore/](https://www.linkedin.com/in/lee-james-gilmore/)T21[https://twitter.com/LeeJamesGilmore](https://twitter.com/LeeJamesGilmore)******

******如果你觉得这些文章鼓舞人心或有用，请随时用虚拟咖啡[https://www.buymeacoffee.com/leegilmore](https://www.buymeacoffee.com/leegilmore)来支持我，不管怎样，让我们联系和聊天吧！☕️******

******如果你喜欢这些帖子，请关注我的简介[李·詹姆斯·吉尔摩](https://medium.com/u/2906c6def240?source=post_page-----39c4f4ae5aff----------------------)以获取更多的帖子/系列，不要忘记联系并说**嗨**👋******

## ******关于我******

********"** *大家好，我是 Lee，英国的 AWS 认证技术架构师和多语言软件工程师，是一名技术云架构师和无服务器主管，过去 5 年来主要从事 AWS 上的全栈 JavaScript 工作。*******

*******我认为自己是一个热爱 AWS、创新、软件架构和技术的无服务器布道者。*******

************** 所提供的信息是我个人的观点，我对这些信息的使用不承担任何责任。********