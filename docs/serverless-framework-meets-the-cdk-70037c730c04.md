# 无服务器框架符合 CDK🚀

> 原文：<https://levelup.gitconnected.com/serverless-framework-meets-the-cdk-70037c730c04>

![](img/77c506d38de906cb060245aac4c13744.png)

丹尼尔·斯通在 [Unsplash](https://unsplash.com/t/architecture?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

## 设置场景

假设您的团队正在为您当前的项目使用[无服务器框架](https://www.serverless.com/)，并且您对您的 [IaC](https://en.wikipedia.org/wiki/Infrastructure_as_code) 有以下基本的定制需求:

1.  如果阶段是生产或准备阶段，则向 lambda 添加 CloudWatch 警报，但是如果是 QA 或短暂环境(*例如 pr-123* )，则不要添加。这可能是因为您希望在这些环境中降低成本，并且您会在开发过程中收到许多您不想要的垃圾警告。

你可以越来越深入，例如:

1.  如果阶段是 QA 或短暂的环境，那么共享一个 [ElasticSearch](https://aws.amazon.com/elasticsearch-service/) 集群，使用阶段作为前缀生成索引名称；但是，在生产或准备阶段，创建一个单独的 ES 群集。您可以在非生产/非转移环境中执行此操作，因为对于短暂的环境，每次创建和删除群集都需要时间，而且成本也很高。
2.  在生产或试运行中有 3 个实例，但是在 QA 或短暂的环境中使用 1 个实例。同样，这可能是为了降低成本，您的负载测试只在您需要更大的集群以增强信心的阶段和生产中运行。

> **注意:**这些都是虚构的需求，只是为了展示不管你的需求如何定制，你都可以用这种方法获得多大的创造力。

使用无服务器框架和 YML，这些定制的需求可能相当麻烦和难以实现(*即使使用社区创建的插件*)，然而当使用 AWS CDK 这样的框架时，这可以很容易地实现，因为您有能力使用 TypeScript 进行条件编程和导入函数。

## 那么，我们如何使用无服务器框架来实现这一点呢？

实现这一点的一种方法是使用 JS 或 TS 作为您的无服务器配置文件，而不是使用 YML ( `*serverless.js*` *或* `*serverless.ts*`)，这就提供了使用导入函数的能力，从本质上使无服务器框架 100%可扩展。如果你可以在 JavaScript 或 TypeScript 中实现，那么你也可以在无服务器框架和 IaC 中实现！💥

这在无服务器框架文档以及节点和类型脚本的示例中有进一步的描述:[https://www . server less . com/Framework/docs/providers/AWS/guide/intro # services](https://www.serverless.com/framework/docs/providers/aws/guide/intro#services)

> 该框架还处理从项目根目录下的 Javascript 文件`serverless.js`或 Typescript 文件`serverless.ts`中本地导出 JSON 对象。虽然框架是语言不可知的，但使用 Node.js 编写的项目将从对服务定义文件使用相同的语言中受益匪浅。

大多数团队自然会直接使用 YML，让我们面对现实吧，它比 JSON 漂亮得多，但在我看来扩展性较差！😍

> 两年前，我和我的团队在一个大型企业无服务器项目中使用了这种方法，这种方法非常成功。这种方法和 [Jest](https://jestjs.io/) 一起意味着我们实际上也可以对我们的逻辑进行单元测试(包括 CloudFormation 输出的快照),因为它应该是确定性的，所以我们可以更加安心；在我们的 [Lerna monorepo](https://lerna.js.org/) 包中，我们也有可重用的函数、类型和接口，以便在多个无服务器服务中使用它们。
> 
> 这也允许我们使用预定义的文件夹策略为不同的资源进行更多的资源和配置(这也可以在 YML 中通过拉入外部文件来实现，但是使用 TS/JS，您可以直接导入并单击 IDE 中的本地函数，这是一种更好的开发人员体验)。

## 好，听起来不错——给我举个例子！

当您从无服务器框架 CLI 中使用[模板功能](https://www.serverless.com/framework/docs/providers/aws/cli-reference/create/)时，选择 **aws-nodejs-typscript** 模板类型，它将在根文件夹中为您生成以下默认 **serverless.ts** 以及 API 网关和“hello”lambda 函数(*调用和下面的自动生成输出*):

```
serverless create --template aws-nodejs-typescript
```

使用无服务器 CLI 自动生成的 serverless.ts 文件示例

这允许我们基于 stage 环境变量，为我们关于警报的特定需求(高于的*)创建并单元测试一个名为 **configureAlarms** 的新函数；然后可以导入到 **serverless.ts** 文件中使用。这将基本上在从函数返回的正确的 [CloudFormation](https://aws.amazon.com/cloudformation/) 中传播到资源部分。例如在第 33 行:*

每个阶段定制警报创建的示例 serverless.ts

以下 sudo 代码显示了导入的 **configureAlarms** 函数在本例中可能的样子:

使用您正在部署的 stage 环境变量运行 serverless 命令(*或者您也可以使用 serverless 中的 dotenv 特性来执行此操作*):

```
STAGE=prod sls deploy --stage=prod
```

## 太棒了，还有其他好处吗？

这种方法还有另外三个好处:

1.  在过去几年中，围绕无服务器配置的配置选项大量增加，现在变得非常冗长。通过使用 Serverless + TypeScript，您将拥有配置的显式类型，从而允许您使用 IDE intellisense 浏览配置。这是优于 YML 的一个主要优点。
2.  如果你有可以在多个无服务器项目中共享和使用的通用函数，你可以通过 NPM 或者通过使用 monorepo 打包和导入它们以获得更好的重用。
3.  您可以键入您的 stage(*正如我上面所做的，并导入它们*)，并从配置文件中获取您的 stage 值，以确保您的 IaC 代码中没有键入错误，并确保已经提供了环境变量(*如果没有，该错误将阻止无服务器部署*)。配置文件可以像下面这样简单，以确保您不必在代码中到处使用`process.env.STAGE` ,也就是说，将它很好地包装在可以在任何地方导入的配置服务中:

```
// the imported getStage function just returns the value of
// process.env.STAGE and throws an error if it is not supplied
const stage = getStage();export const config = {
   stage: `${stage}`,
};
```

# 包扎

虽然这是一个基本的 sudo 代码示例，说明了您可以实现的目标，但希望这将展示一种方法，当 YML 过于严格时，这种方法可能适合您的需求。

让我们连接以下任何一项:

[https://www.linkedin.com/in/lee-james-gilmore/](https://www.linkedin.com/in/lee-james-gilmore/)https://twitter.com/LeeJamesGilmore

如果你觉得这些文章鼓舞人心或有用，请随时用虚拟咖啡[https://www.buymeacoffee.com/leegilmore](https://www.buymeacoffee.com/leegilmore)来支持我，不管怎样，让我们联系和聊天吧！☕️

如果你喜欢这些帖子，请关注我的简介[李·詹姆斯·吉尔摩](https://medium.com/u/2906c6def240?source=post_page-----39c4f4ae5aff----------------------)以获取更多的帖子/系列，别忘了联系我并打招呼👋

**本文由**[**sedai . io**赞助](https://www.sedai.io/)

![](img/4c77bc2d275bd557dda90aa2fe842d2e.png)

# 关于我

**"** *大家好，我是 Lee，英国的 AWS 认证技术架构师和多语言软件工程师，是一名技术云架构师和无服务器主管，过去 5 年来主要从事 AWS 上的全栈 JavaScript 工作。*

*我认为自己是一个热爱 AWS、创新、软件架构和技术的无服务器布道者。*

******** 所提供的信息是我个人的观点，我对这些信息的使用不承担任何责任。**