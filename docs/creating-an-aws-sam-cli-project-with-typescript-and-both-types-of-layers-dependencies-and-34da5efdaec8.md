# 使用 Typescript 和两种类型的层(依赖项和功能)创建 AWS SAM CLI 项目

> 原文：<https://levelup.gitconnected.com/creating-an-aws-sam-cli-project-with-typescript-and-both-types-of-layers-dependencies-and-34da5efdaec8>

![](img/a468842f6bdb12bc68e38409adced642.png)

由[约书亚·阿拉贡](https://unsplash.com/@goshua13?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你想跳过教程，直接获取代码，这里有 GitHub repo:

[](https://github.com/Borduhh/serverless-SAM-typescript-boilerplate) [## borduhh/server less-SAM-typescript-boilerplate

### 使用 Typescript 和 Webpack 的带有功能和依赖层的 AWS SAM CLI 样板文件。GitHub 是超过 50 个…

github.com](https://github.com/Borduhh/serverless-SAM-typescript-boilerplate) 

最近，有人向我介绍了如何使用 SAM CLI 创建 AWS Lambda 部署。和往常一样，我喜欢尝试新工具，所以我一头扎了进去。大约 30 分钟后，我的头感觉像…

试图弄清楚 SAM CLI 如何使用 Typescript

我很快意识到建立一个生产级的应用程序需要大量的工作。更有甚者，使用我最喜欢的工具(ESLint、Typescript 和更漂亮的)变得更加令人头疼。更糟糕的是，没有任何关于使用 Typescript 和函数层构建样板文件的好文章。然而，在阅读了大量文档和 StackOverflow 搜索之后，我能够拼凑出一个模板结构，它允许我使用我最喜欢的工具快速创建和部署功能和层。

# 项目结构

为了使这个项目易于扩展到较小的团队和项目，我选择坚持使用 monorepo 结构，而不是将每个 API 都编码在自己的 repo 中。这使得代码在部署到 AWS CloudFormation 之前更容易处理和测试。

在本文结束时，项目结构将如下所示:

```
Helloworld-Application
  apis/
    helloworld/
      functions/
        greeting/
          dist/
          src/
              __tests__/
          tsconfig.json
          package.json
      layers/
        api-responses
          dist/
          src/
              __tests__/
          tsconfig.json
          package.json
        global-dependencies/
          package.json
    template.yaml
  package.json
  .gitignore
```

如果您想添加另一个 API，只需复制`helloword`目录。如果想做一个新函数，复制`greeting`函数即可。就这么简单，但我将在最后介绍所需的具体更改。让我们开始建造吧。

# 入门—安装 SAM CLI 和 AWS 工具包

这个项目需要 SAM CLI。我还推荐使用 AWS 工具包，因为它能让您深入了解 IDE 中的部署。为了简洁起见，我不打算重复这些步骤，但是这里有一些很好的资源可以帮助您设置环境:

*   安装 SAM CLI—[https://docs . AWS . Amazon . com/server less-application-model/latest/developer guide/server less-SAM-CLI-install . html](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
*   安装用于 VSCode 的 AWS 工具包—[https://docs . AWS . Amazon . com/Toolkit-for-vs code/latest/user guide/setup-Toolkit . html](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/setup-toolkit.html)

# 入门—设置项目目录结构

首先，让我们建立项目的基本结构，这样我们就可以很容易地将所有东西放在需要的地方。在 IDE 中创建以下文件夹:

```
Helloworld-Application
  apis/
    helloworld/
      functions/
        greeting/
      layers/
        api-responses/
        global-dependencies/
```

现在导航到终端中的`Helloworld-Application`文件夹，用`npm init`命令初始化一个 NPM 项目。

一旦你完成了提示，请随意进入并移除你的`package.json`中的`main`线并保存它。

最后，让我们为一个 SAM 项目添加默认的`.gitignore`文件，这样这些东西就不会出现在我们的回购中了。代码如下:

默认。SAM 无服务器项目的 gitignore

# 设置依赖层

首先是依赖层。这包含了在您的函数之间共享的所有 npm 包。这一层将非常容易设置，因为我们在这个项目中不会用到它。但是因为我们正在构建一个样板模板，所以包含它是很重要的，因为您的大多数生产项目功能将共享共同的依赖关系。所以我们来设置一下。

在 IDE 中导航到`/apis/helloworld/layers/global-dependencies`，让我们创建一个简单的`package.json`文件。代码如下:

Global Dependencies package.json 文件

很简单，对吧？请记住，在生产应用程序中，您可以在这里添加任何共享的依赖项。

这就是依赖层的全部内容。让我们确保 SAM CLI 可以在构建阶段找到我们的全局依赖项。在 IDE 中导航到`/apis/helloworld/`，让我们创建一个`template.yaml`。该文件用于告诉 SAM CLI 如何构建我们的项目。以下是目前为止的代码:

HelloWorld API template.yaml 文件

为此，我们告诉 SAM CLI，我们有一个名为`GlobalDependenciesLayer`的层，它位于`/layers/global-dependencies`。默认情况下，SAM CLI 将查找`package.json`文件，并将其中的所有依赖项安装到引用该层的每个函数中。

***注意:*** *您在* `*ContentUri*` *中指定的文件夹内必须有一个 package.json 文件，并带有名称和版本，否则当您运行* `*build*` *命令时 SAM CLI 将出错。*

现在，如果我们在终端中导航回`/apis/helloworld/`，并键入`sam build`，您应该会得到以下结果:

```
Building layer 'GlobalDependenciesLayer'
Running NodejsNpmBuilder:NpmPack
Running NodejsNpmBuilder:CopyNpmrc
Running NodejsNpmBuilder:CopySource
Running NodejsNpmBuilder:NpmInstall
Running NodejsNpmBuilder:CleanUpNpmrcBuild SucceededBuilt Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yamlCommands you can use next
=========================
[*] Invoke Function: sam local invoke
[*] Deploy: sam deploy --guided
```

恭喜你，你已经创建了一个依赖层！

# 设置功能层

拥有一个依赖层是很酷的，但是如果我们有一个定制的代码库，而不是我们想要用于多种功能的 NPM 包，那该怎么办呢？这就是功能层的用武之地。在这一层，我们将构建一个简单的 API 响应库，这样我们就不必在以后手工将它们编码到每个函数中。

让我们在 IDE 中导航到`/apis/helloworld/layers/api-responses/`,并在这里添加另一个 package.json，与前面类似。这个 package.json 还将包含我们的开发环境依赖项和一些脚本，以便在我们稍后打包代码时帮助我们。

API 响应层 package.json 文件

现在，通过在您的终端中导航到`/apis/helloworld/layers/api-responses/`并键入`npm install`来安装所有的`devDependencies`来安装所有的东西。

现在我们可以开始设置我们的开发环境了。先说大家最喜欢的工具，ESLint，还有更漂亮的。以下是我为他们两个设置的配置:

API 响应层。eslintrc 和。漂亮的文件

请记住，这些都是我个人对 ESLint 和 pretty 的喜好。您可以轻松地更改或删除它们，这不会影响项目。

让我们也配置 Typescript 来处理我们的项目，因为它是大多数生产应用程序中的必备组件。继续使用以下代码在`/apis/helloworld/layers/api-responses/`中创建一个`tsconfig.json`文件:

API 响应层 tsconfig.json 文件

***注意:*** *您可能会从 Typescript 开始得到一个* `*No inputs were found in config file*` *错误。不要担心，当我们构建代码时，它会消失。*

现在，为了确保我们的代码被正确打包，让我们设置我们的 Webpack 配置。使用以下代码在与您的`tsconfig.json`相同的目录中创建一个`webpack.config.ts`文件:

API 响应层 webpack.config.js 文件

最后但同样重要的是，让我们确保我们可以使用 Jest 通过单元测试遵循测试驱动开发(TDD)过程。在同一个目录下创建一个`jest.config.js`文件。

API 响应层 jest.config.js 文件

我告诉过你这需要很多准备时间。现在我们完成了配置，终于可以开始实际的逻辑工作了(唷！).

# 构建我们的功能层

所有的设置都准备好了，是时候构建一些默认响应了，我们将在以后的函数中使用它们。使用 TDD(测试驱动开发)方法，让我们首先为我们的默认 API 响应构建测试。在`/api-responses/src/__tests__`中创建`defaultResponses.ts`文件。下面是测试代码:

API 响应层默认值 Responses.test.ts

现在我们知道了我们要测试什么，我们可以只写我们需要的代码来满足我们的测试用例。让我们在`/api-responses/src`中创建`defaultResponses.ts`文件。代码如下:

API 响应层默认响应. ts 文件

***注意:*** *我们在这里使用的是* `*any*` *类型，因为您可以传入自定义标题和数据类型的无限组合。*

下面是对`defaultResponses.ts`中发生的事情的分析。在我们的函数中，我们需要用一个`body`、`headers`和`statusCode`以 AWS Lambda 理解的方式格式化我们的响应。我们可以简单地在每个函数中分别构建对象，但是这违背了软件开发的基本规则，即枯燥(不要重复)的编码。相反，我们可以在这里对对象进行一次编码，然后在任何需要的时候调用这个函数。

现在让我们确保我们的代码通过了测试。在您的终端中导航到`/apis/helloworld/api-responses`并运行`npm test`命令。您应该得到显示所有通过测试的输出，如下所示:

```
> api-responses-layer@1.0.0 test
/serverless-SAM-typscript-boilerplate/apis/helloworld/layers/api-responses> jest**PASS ** src/__tests__/**defaultResponses.test.ts** API Response Success Tests
✓ Should return a default response (3 ms)
✓ Should return custom headers (1 ms)
✓ Should return 204 with no body
✓ Should return 200 with body data
API Response Error Tests
✓ Should return a default error (1 ms)
✓ Should return custom headers (1 ms)
✓ Should return 500 error wtih general (1 ms)
✓ Should return 400 error with message**Test Suites: 1 passed**, 1 total
**Tests:       8 passed**, 8 total
**Snapshots:** 0 total
**Time:**        2.396 sRan all test suites.
```

好消息！一切都在按预期运行。现在让我们通过在终端中运行`npm run build:prod`命令来构建生产代码。这将把`package.json`文件复制到`/dist`文件夹中，然后打包我们所有的源代码。完成后，您应该会得到以下输出:

```
> api-responses-layer@1.0.0 build:prod 
/serverless-SAM-typscript-boilerplate/apis/helloworld/layers/api-responses> npm run clean && mkdir ./dist && cp package.json ./dist/package.json && NODE_ENV=${NODE_ENV:-production} webpack> api-responses-layer@1.0.0 clean
/serverless-SAM-typscript-boilerplate/apis/helloworld/layers/api-responses> rm -rf dist/Hash: **53f69af59a60000f45fc** Version: webpack **4.43.0** Time: **1990**ms
Built at: 07/22/2020 **10:32:54 AM****Asset**      **Size**  **Chunks**                   **Chunk Names****defaultApiResponses.js**  1.65 KiB       **0**  **[emitted]**        defaultApiResponses**defaultApiResponses.js.map**  7.09 KiB       **0**  **[emitted] [dev]**  defaultApiResponsesEntrypoint **defaultApiResponses** = **defaultApiResponses.js** **defaultApiResponses.js.map**[0] **./src/defaultResponses.ts** 1.86 KiB {**0**} **[built]**
```

现在让我们在`/apis/helloworld/template.yaml`文件中注册我们的新层。新文件应该如下所示:

带有 API 响应层更新的 template.yaml

如果我们在终端中导航回`/apis/helloworld/`并运行`sam build`命令，我们将看到我们的第二层已经成功构建。输出应该如下所示:

```
Building layer 'GlobalDependenciesLayer'
Running NodejsNpmBuilder:NpmPack
Running NodejsNpmBuilder:CopyNpmrc
Running NodejsNpmBuilder:CopySource
Running NodejsNpmBuilder:NpmInstall
Running NodejsNpmBuilder:CleanUpNpmrc
Building layer 'GlobalApiResponsesLayer'
Running NodejsNpmBuilder:NpmPack
Running NodejsNpmBuilder:CopyNpmrc
Running NodejsNpmBuilder:CopySource
Running NodejsNpmBuilder:NpmInstall
Running NodejsNpmBuilder:CleanUpNpmrcBuild SucceededBuilt Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yamlCommands you can use next
=========================
[*] Invoke Function: sam local invoke
[*] Deploy: sam deploy --guided
```

# 构建我们的问候功能

到目前为止，我们已经有了两层，其中包含了一些可重用的依赖项和代码。让我们构建我们的第一个函数，这样我们就可以看到它们是如何联系在一起的。在 IDE 中导航到`/apis/helloworld/functions/greeting`，让我们创建另一个`package.json`文件。这段代码看起来应该很熟悉:

问候功能包. json 文件

现在通过在你的终端中导航到`/apis/helloworld/functions/greeting`并使用`npm install`命令来安装所有的东西。

另外，确保将以下文件从`/apis/helloworld/layers/api-responses`复制到`/apis/helloworld/functions/greeting`:

```
.eslintrc
.prettierrc
jest.config.js
tsconfig.json
webpack.config.js
```

因为函数都是自包含的程序，我们需要确保我们所有的环境配置都被继承。一旦完成，创建`/greeting/src/__tests__/handler.test.ts`文件，让我们像往常一样首先编写单元测试。代码如下:

问候函数 handler.test.ts

现在我们进去在`/greeting/src/handler.ts`里面写实际的函数。代码如下:

问候功能处理程序. ts

现在，如果你粘贴代码，你可能会看到一个`Cannot find module ‘/opt/nodejs/defaultResponses’ or its corresponding type declarations.` 类型脚本错误。

让我们回顾一下为什么会抛出这个错误。抛出错误的行是导入我们的 API 响应层的行，这里是:

```
import response from '/opt/nodejs/defaultResponses';
```

我们从`template.yaml`文件中知道，我们告诉 SAM CLI 从`layers/api-responses/dist`获取 API 响应层，那么为什么我们在`/opt/nodejs`引用它呢？这是因为 SAM CLI 将所有功能层打包到`/opt/nodejs/`。

***注意:*** *如果有多个层的文件名相同，那么最后引用的层将是打包成* `*/opt/nodejs*` *的代码。*

现在有趣的部分来了。Typescript 不知道我们正在开发一个 SAM CLI 应用程序，所以它不知道在哪里寻找我们的文件。为了解决这个问题，让我们通过在我们的`tsconfig.json`文件中添加`baseUrl`和`paths`变量来声明一个别名。现在应该是这样的:

问候功能 tsconfig.json 文件

嘣，错误消失了！好吧，让我们确保所有的测试都正常。在终端导航到`/apis/helloworld/functions/greeting`并运行`npm test`命令。它应该是这样的:

问候功能 Jest 配置错误

WTF？！？！？又一个错误！我以为我们已经修好了？

好吧，看来带有`ts-jest`的 Jest 无法读取我们的`tsconfig.json`。不过我们很幸运，`ts-jest`创建了`pathsToModuleNameMapper`实用程序来帮助解决这个问题。让我们打开我们的`/apis/helloworld/functions/greeting/jest.config.js`文件并添加它。下面是新代码:

问候功能 jest.config.js 文件

我们现在再做一次测试。你应该知道这个:

问候功能玩笑测试通过

我们现在处于最后阶段。我们所要做的就是用 SAM CLI 模板注册函数并构建代码。首先让我们在`/apis/helloworld/template.yaml`文件中注册这个函数。现在应该是这样的:

包含问候功能的 template.yaml 文件

现在，在我们得到类似 Typescript 和 Jest 的另一个模块未找到错误之前，让我们更新我们的`webpack.config.ts`文件，将`alias`添加到 Webpack 的解析器中。以下是新文件:

问候功能 webpack.config.ts 文件

好了，现在让我们在终端中导航到`/apis/helloworld/functions/greeting`并运行`npm run build:prod`命令来打包我们的新函数。您应该得到以下输出:

问候功能 Webpack 成功输出

很好，现在让我们用 SAM CLI 构建应用程序。在终端导航到`/apis/helloworld`并运行`sam build`命令。一切都应该构建成功，您应该得到这个输出。

sam build 命令的输出

恭喜你！您已经成功地用 Typescript 和 layers 构建了一个无服务器样板文件。如果您运行`sam local start-api`命令，您将看到可以调用您的函数的 URL。它看起来会像这样:

```
Mounting GreetingFunction at http://127.0.0.1:3000/hello [GET]
```

如果您在浏览器中访问该 URL，您应该会看到如下输出:

```
{"error":{},"data":{"message":"Hello World!"}}
```

嘣！现在，这个无服务器的世界是你的了。走出去，建立下一个网飞或什么的。干杯！像往常一样，如果你发现一个错误，让我知道。