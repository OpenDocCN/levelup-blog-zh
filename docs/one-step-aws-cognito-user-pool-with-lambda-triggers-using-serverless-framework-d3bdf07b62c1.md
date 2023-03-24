# 使用无服务器框架的 Lambda 触发器的一步式 AWS 认知用户池

> 原文：<https://levelup.gitconnected.com/one-step-aws-cognito-user-pool-with-lambda-triggers-using-serverless-framework-d3bdf07b62c1>

![](img/926d92aa87abdf7ac32d6f9f35a7952b.png)

## 在一个无服务器堆栈中部署您的 Cognito 用户池和自定义触发器，并在此过程中更好地理解无服务器

无服务器框架使您能够创建一个 Cognito 用户池，对其进行全面配置，并向其添加自定义 lambda 触发器，所有这些都在一个部署步骤中完成。

# 创建用户池

让我们首先在无服务器堆栈的**资源**部分设置一个简单的 Cognito 用户池:

Serverless 允许您在堆栈的 **Resources** 部分使用 CloudFormation 模板，因此我在我的 Cognito pool 配置中拥有由 [CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cognito-userpool.html) 提供的所有控制。在这里，我创建了一个名为`mikes-example-pool` *的新池，*通过设置`UserNameAttributes`指定我希望用户使用电子邮件地址注册该池，并配置 Cognito 使用`AutoVerifiedAttributes`自动启动这些电子邮件地址的验证过程。我已经给用户池起了一个逻辑资源名`CognitoUserPoolMikesExamplePool`。记住这一点——它以后会变得很重要！

# 添加自定义 Lambda 触发器

接下来，我想在我的堆栈中添加一个定制的 Cognito Lambda 触发器。

为了实现这一点，我可以向我的无服务器文件的 **Functions** 部分添加一个 lambda 函数，并用类型为 **cognitoUserPool 的事件来配置它。**

在这里，我创建了一个名为`cognitoCustomMessageLambda` 的 lambda 函数，它将触发`CustomMessage` Cognito 触发器。无服务器支持 AWS 提供的[以及这些](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html)键[引用的所有自定义触发器类型](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-cognito-userpool-lambdaconfig.html)[，目前包括:CustomMessage、CreateAuthChallenge、DefineAuthChallenge、VerifyAuthChallengeResponse、PostAuthentication、PostConfirmation、PreAuthentication、PreSignUp、PreTokenGeneration 和 UserMigration。](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html)

# 连接池和 Lambda 触发器

当我将 **cognitoUserPool** 事件添加到我的无服务器文件中的 lambda 时，框架的默认行为是创建一个新的 cognitoUserPool 并将其添加到由无服务器生成的 CloudFormation 模板中。这个新池就是我们的自定义 Lambda 触发器所连接的。

根据无服务器[命名约定](https://www.serverless.com/framework/docs/providers/aws/guide/resources/#aws-cloudformation-resource-reference)，当这个新池被添加到我们生成的 CloudFormation 模板中时，它将被赋予一个形式为`CognitoUserPool**{normalizedPoolId}**`的逻辑资源名称，其中{normalizedPoolId}是我在我的 **cognitoUserPool** 事件中作为`pool`的值提供的名称。在上面的例子中，我的池的 normalizedPoolId 是`MikesExamplePool`。

因此，缺省情况下，Serverless 将尝试向它生成的 CloudFormation 堆栈添加一个名为`CognitoUserPoolMikesExamplePool`的新 Cognito 池，并将我的 lambda 触发器连接到这个池。

然而，这并不是我想要的；我希望我的触发器连接到我在 **Resources 中声明的池。**为了实现这一点，我必须利用无服务器给你的能力[覆盖云信息资源](https://www.serverless.com/framework/docs/providers/aws/guide/resources/#override-aws-cloudformation-resource)。我可以通过在我的 **Resources** 部分中声明我的 Cognito 池来做到这一点，使用无服务器想要为它将要生成的池使用的逻辑资源名；在这个例子中，这个名字将会是`CognitoUserPoolMikesExamplePool` **。**使用这个逻辑资源名称会导致 Serverless 用我在 **Resources 中声明的模板覆盖它为该资源默认生成的 CloudFormation。**这确保了通过将 **cognitoUserPool** 事件添加到我的 lambda 中而生成的默认池将被我已经声明的池所替换，并且我的触发器将被连接到这个池。

这允许我创建一个用户池，并在一个无服务器部署中向其中添加一个定制的 lambda 触发器。

# 解决纷争

似乎无法正确配置您的池和触发器？要更清楚地了解 Serverless 实际在做什么，一个好方法是查看框架为您生成和部署的 CloudFormation 模板。您可以在运行`serverless package`或`serverless deploy`时生成的`.serverless`目录下的`cloudformation-template-update-stack.json`文件中找到这个文件。

# 资源

无服务器[文档](https://www.serverless.com/framework/docs/providers/aws/guide/resources/#override-aws-cloudformation-resource)描述了通用云构造覆盖方法
无服务器[文档](https://www.serverless.com/framework/docs/providers/aws/events/cognito-user-pool/#overriding-a-generated-user-pool)描述了这种方法与 cognitoUserPool lambda 事件的使用
无服务器[命名约定](https://www.serverless.com/framework/docs/providers/aws/guide/resources/#aws-cloudformation-resource-reference)用于云构造覆盖
更多[关于实现特定 Cognito lambda 触发器的信息](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html)