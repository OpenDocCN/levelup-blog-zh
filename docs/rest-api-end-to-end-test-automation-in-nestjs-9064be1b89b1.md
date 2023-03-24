# Nestjs 中 REST API 端到端测试自动化

> 原文：<https://levelup.gitconnected.com/rest-api-end-to-end-test-automation-in-nestjs-9064be1b89b1>

![](img/87a0cc0e0b55ab3da535fd971ef67629.png)

图片[来自](https://pixabay.com/pl/users/mohamed_hassan-5229782/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=7281686) [Pixabay](https://pixabay.com/pl//?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=7281686) 的穆罕默德·哈桑

你好！如果你读过我以前的文章，你可能会发现我喜欢 e2e 测试中的自动化。自动化保护我和我的团队免受挫折。另外，当编写测试非常简单时，每个人都可以编写它们！

# 问题

编写端到端测试需要大量的检查。使用什么 URL，服务器期望什么，服务器返回什么？当你改变一些东西而不使用应用程序源代码中的接口时，你就有了一个 bug，直到你启动它们时你才知道。随着时间的推移，这变得混乱、耗时，并阻碍团队编写测试。

# 想法

在上一篇文章中，我写了当我们使用 GraphQL 时的自动化测试。但是 GraphQL 并不是在我们的应用程序中创建 API 的唯一方法！

这次我们将看看 REST APIs。在这种情况下，我们没有像 GraphQL 中那样的静态契约。因此，我们需要小心并生成文档，以便能够解释它并生成 SDK。为了我们的目的，我们将使用 swagger。多亏了 Nestjs，它很容易使用。我们需要做的就是增加一些装饰者。此外，这个文档可以帮助我们的团队在使用我们 API 的程序员之间共享知识。

# 解决办法

由于我们想要使用 swagger，首先要做的是安装所需的依赖项:

`yarn add @nestjs/swagger swagger-ui-express`

下一步是在 bootstrap 方法中注册我们的 API。所以我们需要转到 main.ts 文件并添加一些代码。这一步允许我们从 swagger 的角度检查我们的 API 看起来如何。如果你愿意，你可以跳过这一步。稍后，我将向您展示如何将开放 API 规范生成到文件中。

主页面

要生成 SDK，我们需要在文件中有一个规范。所以现在是时候生成一次了！只需在 src 文件夹中创建`generate-open-api.ts`文件并粘贴代码:

`generate-open-api.ts`

为了简化新生成器的使用，我们将向 package.json 添加一个脚本。

```
{ "scripts": { "build:open-api-spec": "nest start —-entryFile generate-open-api", }}
```

现在是施展魔法的时候了。我们可以在`nest-cli.json`注册插件，节省一些添加装饰者的时间！

`nest-cli.json`

# 设置测试

为了从 JSON 文件生成 SDK，我们将使用 npmjs.com 上可用的库。

`yarn add -D oazapfts`

该库提供了一个 CLI 来生成所有所需类型的 ts 文件。我们将向 package.json 文件添加另一个命令。

```
{
  "scripts": {
    "build:open-api-spec": "nest start --entryFile generate-open-api",
    "test:build-test-sdk": "oazapfts open-api.json test/temp/sdk.ts",
    "test:generate-sdk": "yarn build:open-api-spec && yarn test:build-test-sdk"
    ...
  }
}
```

现在，当我们运行命令`test:generate-sdk`时，我们将得到 ts 文件类型！此外，我们可以将该文件添加到`.gitignore`中，每次更改后，我们将节省格式化该文件的时间。

我们前面步骤的结果是一个文件，其中的类型和函数都可以使用。现在，我们将在此基础上添加抽象，并为我们的用户创建会话。

我们将创建工厂，命名为 SessionFactory。这个类将负责创建 api 客户端。

会话生成器. ts

简短的帮助方法来准备我们的应用程序进行测试，我们已经准备好测试我们的 API 了！

我们的测试看起来像这样:

示例测试

在 generate-open-api.ts 中，通过覆盖 operationIdFactory 选项，可以很容易地更改我们方法的名称。

# 摘要

在本文中，我向您展示了如何测试 REST API。希望你喜欢这个教程！

**链接到资源库**:[https://github . com/adziok/nestjs-graph QL-testing/tree/rest-API](https://github.com/adziok/nestjs-graphql-testing/tree/rest-api)