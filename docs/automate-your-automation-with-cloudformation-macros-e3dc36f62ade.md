# 使用 CloudFormation 宏实现自动化

> 原文：<https://levelup.gitconnected.com/automate-your-automation-with-cloudformation-macros-e3dc36f62ade>

![](img/428c96a71c228006dda8616059a10bdf.png)

图片由[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3885331)的 Gerd Altmann 提供

## 作为代码的基础设施已经改变了云世界的游戏规则。但是您知道可以自动创建 AWS 资源吗？

我在 2019 年初第一次开始了我的无服务器之旅。我着迷于你能很快拼凑起来的所有东西，无法相信我在整个软件生涯中错过了什么。

对我来说特别的是 CloudFormation，特别是[无服务器应用模型(SAM)](https://aws.amazon.com/serverless/sam/) 。SAM 允许您快速、轻松地定义无服务器函数、API 和事件源映射。

当您在后台部署 SAM 应用程序时，它会将您的无服务器引用转换为 CloudFormation 资源，并将它们推入 AWS。

我喜欢这个想法，我可以通过代码定义架构，并让它成功地部署到任何 AWS 帐户。在某种意义上，它有点像一个容器。如果我知道它能工作，我可以在任何地方运行它。

随着时间的推移，我的用例开始变得越来越高级。我开始为特定类型的资源设置模式，并对资源进行“拷”——这意味着当我创建这种类型的资源时，我也需要创建另一种资源。

这些都不难，但容易出错。有人可能会忘记添加资源或弄错命名约定。

然后我发现了 CloudFormation 宏。

![](img/e3669d72f5cab47305e42d5bc1e2db50.png)

*图片由* [*罗宾希金斯*](https://pixabay.com/users/robinhiggins-1321953/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2681507) *从* [*Pixabay*](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2681507)

# 什么是宏？

一个 [CloudFormation 宏](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-macros.html)是一个在部署上运行的函数，它转换模板中定义的资源。它可以做任何事情—添加新资源、删除特定资源或操纵现有资源。

如果你使用 SAM，那么你已经在使用一个宏，甚至可能不知道它。在每个 SAM 模板中都定义了一个指向无服务器的`Transform`属性.....AWS 里的东西。嗯，那个“东西”实际上是一个宏，它将把 SAM 中定义的快捷方式转化为 CloudFormation 资源。

当您开始寻找构建资源的模式时，宏就派上了用场。当你构建一个新的 lambda 函数时，有没有一个资源总是被创建的？每当实现一个新的 API 端点时，是否有一个手动过程发生？

将人的因素从任何手动过程中去除应该是一个目标，因为它消除了出错的机会。机器每次都会以相同的方式执行任务。人类不会。我们会分心或懒惰，或认为有些事情是不必要的，不会去做。

如果你能自动化你的自动化，从长远来看，你就为自己的成功做好了准备。一次又一次地，开发会做得更快，更一致。

# 构建一个示例

假设您有一个场景，每次创建带有特定环境变量的 lambda 函数时，您都希望创建一个 SSM 参数来跟踪该值。这将允许您从 SSM 控制台查看整个应用程序中该环境变量的所有值。

这可以用宏来完成。因此，让我们建立一个自动化的过程。

我们知道我们的宏有两个目标:

*   λ函数
*   特定的环境变量。

当 CloudFormation 宏运行时，它们在具有以下属性的事件中传递:

```
{ 
 "accountId": "", // AWS Account Id
 "fragment": {}, // JSON representation of your SAM/CloudFormation template
 "transformId": "", // Id of the specific transform
 "requestId": "", // Id of this run
 "region": "", // Region being deployed into
 "params": {}, // Parameters used in the transform
 "templateParameterValues": { } // Parameter values used in the template
}
```

包含在`fragment.Resources`中的值是由您的模板生成的所有资源。因此，对于我们的示例，我们需要将资源过滤到具有特定环境变量的 lambda 函数。这个变量将被称为`LAMBDA_VALUE`。

我们的宏将被写成 NodeJS 函数。为了在 SAM 模板中找到 lambda 资源，我们需要编写以下 javascript:

```
const lambdas = []; 
const resourceKeys = Object.keys(resources); 
for (let i = 0; i < resourceKeys.length; i++) { 
  const resource = resources[resourceKeys[i]]; 
  if (resource.Type === 'AWS::Serverless::Function' 
    && resource.Properties.Environment.Variables.LAMBDA_VALUE) {
   lambdas.push({ 
      name: resourceKeys[i],
      details: resource 
   }); 
  } 
}
```

这个代码片段生成了一个数组，其中包含所有具有我们的环境变量的 lambda 函数。现在我们想用环境变量中的数据构建 SSM 参数。

```
lambdas.forEach((lambda) => { event.fragment.Resources[`${lambda.name}Parameter`] = { 
  Type: 'AWS::SSM::Parameter', 
  Properties: { 
    Name: `${lambda.name}Parameter`, 
    Type: 'String', 
    Value: lambda.details.Properties.Environment.Variables.LAMBDA_VALUE
   } 
  }; 
});
```

这会将新的 SSM 参数资源直接添加到要创建的 AWS 资源列表中。最后，我们需要以宏期望响应的格式返回更新的资源。

所以在我们的函数中我们返回:

```
return { 
  requestId: event.requestId, 
  status: 'success', 
  fragment: event.fragment 
};
```

现在我们已经写好了源代码([在这里查看完整源代码](https://gist.github.com/allenheltondev/1281088e952ef428e7971ffd0b7ec1af#file-function-js))，我们需要创建宏资源。在我们的 SAM/CloudFormation 模板中，我们需要创建一个类型为`AWS::CloudFormation::Macro`的资源，它指向我们刚刚创建的函数。

```
AddSSMParametersMacro: 
  Type: AWS::CloudFormation::Macro 
  Properties: 
    Name: AddSSMParameters 
    Description: Adds SSM Parameters for lambdas with the LAMBDA_VALUE env var 
    FunctionName: 
      Ref: AddSSMParametersFunction
```

宏是简单的资源，它们接受名字和对函数的引用。一旦我们构建了它并将其部署到我们的 AWS 帐户中，我们将能够在我们的其他 SAM 或 CloudFormation 模板中使用它。

# 使用宏

此时，宏已经构建好，可以使用了。要将其添加到 SAM 或 CloudFormation 模板中以供使用，请将其添加到`Transform`属性中。

```
Transform: [AWS::Serverless-2016-10-31, AddSSMParametersMacro]
```

该属性是一个数组，可以运行任意多的宏。*只要宏在您部署到的同一个 AWS 帐户*中，它就可以正常工作。

在部署模板的幕后，它将按照模板中指定的顺序遍历所有的转换。在我们的示例中，它将首先运行 SAM 转换，将快速简单的 SAM 快捷方式转换为成熟的 CloudFormation。然后，它将运行我们的自定义宏，分析所有的函数，并添加任何新的 SSM 参数。

运行转换后，它会将所有资源部署到 AWS 中。

很简单，对吧？

![](img/f56f4c154eda1678b5d7439afa498d4c.png)

*图片由*[*combreak*](https://pixabay.com/users/comfreak-51581/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2380009)*转自* [*Pixabay*](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2380009)

# 尝试

宏非常适合在您的管道中添加另一层自动化。对于包含手动过程的场景，宏是完美的。

请记住，宏是 lambda 函数。你可以让他们做任何事。写入 DynamoDB，调用一个外部 API，[触发一个 webhook 事件](https://medium.com/better-programming/how-to-integrate-your-app-with-webhooks-using-amazon-sns-a36aad22f3b1)，可能性无穷无尽。

你可以在这里找到我们代码演练[的完整例子。AWS 还在 GitHub 上提供了一个完整的宏](https://gist.github.com/allenheltondev/1281088e952ef428e7971ffd0b7ec1af)库来帮助你的自动化之旅。

我希望您能找到一些宏的伟大用例，并使您的管道更加高效。

继续自动化！