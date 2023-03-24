# 如何测试 NuxtJS 中间件

> 原文：<https://levelup.gitconnected.com/how-to-test-nuxtjs-middleware-793a4fbd039d>

![](img/85ab06869b13937a8d5d7d988f9c799d.png)

来自[像素](https://www.pexels.com/photo/female-engineer-controlling-flight-simulator-3862132/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[this is 工程](https://www.pexels.com/@thisisengineering?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)照片

## 测试直到你变成绿色

## 带有示例的 NuxtJS 中间件测试设置的分步教程

NuxtJS 框架允许快速构建和启动 Vue.js 应用程序。它的众多便利之一是能够通过中间件*传递请求，而不需要额外的服务器。如果您主要只是需要一个客户端 Vue.js 应用程序，但还需要支持一些服务器端 API 调用或操作，NuxtJS 可以让这一切变得简单。此外，如果您对第三方 API 的调用被限制在您的服务器端中间件中，这有助于确保您的 API 秘密不会暴露给浏览器客户端。*

服务器端中间件很棒，但是我们如何确保我们的中间件得到充分的测试？

> 在这个循序渐进的教程中，我们将介绍测试 NuxtJS 应用程序中使用的服务器中间件的设置。我们将使用 Je `st`和 S `uperTest`，浏览各种中间件示例及其相应的测试。

这是我们的教程路线图:

1.  创建一个新的 NuxtJS 应用程序。
2.  为我们的第一个服务器端中间件——一个基本 GET 请求的 API 端点——编写测试。
3.  创建一个模拟服务器的测试支持文件，并使用在`nuxt.config.js`注册的每个中间件。
4.  实现我们的中间件，让我们的测试变得绿色。
5.  有了我们的测试框架，再看几个中间件测试和实现的例子。

# 1.创建一个 NuxtJS 应用程序

我们将从创建一个新的 NuxtJS 应用程序开始。在本教程中，我们将使用`yarn`，但你也可以使用`npm`。在命令行中，我们将运行`yarn create nuxt-app`命令，然后回答一些提示。注意，对本教程很重要的是，我们**将**使用`ESLint`、`Jest`和`Universal (SSR)`渲染模式。

```
~$ **yarn create nuxt-app middleware-tests** ...
create-nuxt-app v2.15.0
✨  Generating Nuxt.js project in middleware-tests
? Project name *middleware-tests*
? Project description *My NuxtJS demonstration of middleware testing*
? Author name *Alvin Lee*
? Choose programming language *JavaScript*
? Choose the package manager *Yarn*
? Choose UI framework *None*
? Choose custom server framework *None (Recommended)*
? Choose Nuxt.js modules *None*
? Choose linting tools **ESLint**
? Choose test framework **Jest**
? Choose rendering mode **Universal (SSR)**
? Choose development tools *None
...* Done in 217.53s.
```

我们有我们的 NuxtJS 应用程序。让我们继续删除前端相关代码，因为这不是我们在这里关注的重点:

```
~$ **cd middleware-tests**
~/middleware-tests$ **rm components/***
~/middleware-tests$ **rm pages/***~/middleware-tests$ **yarn test** yarn run v1.22.4
$ jest
No tests found, exiting with code 1
```

好吧，这是个开始。我们还没有任何测试，但我们正在路上。

# 2.为获取端点中间件编写测试

让我们为我们的第一个中间件编写一些测试。我们想要的是一个基本的 API 端点，它可以完成以下任务:

*   接受对端点`/hello`的 GET 请求
*   需要一个 GET 查询参数`name`
*   返回一个带有`name`和`message`的 JSON 编码对象。例如，如果给定的`name`是`John`，那么应该返回的是:

```
{
  "name":"John",
  "message":"Hello, John."
}
```

*   如果`name`参数未给定或为空，则用状态码`500`响应。

很简单。请记住，我们不是要一个超级复杂的中间件——我们的目标是掌握中间件测试设置。为了测试，我们将使用 [Jest](https://jestjs.io/) 和 [SuperTest](https://github.com/visionmedia/supertest) 。Jest 已经嵌入到我们的 NuxtJS 应用程序中，但是我们需要添加 SuperTest:

```
~/middleware-tests$ **yarn add --dev supertest**
```

接下来，让我们创建一个`middleware`子文件夹。

```
~/middleware-tests$ **mkdir middleware**
```

让我们在这个新的`middleware`文件夹中创建我们的测试文件，将其命名为`hello.test.js`:

GET endpoint 中间件的测试文件:~/middleware-tests/middleware/hello . test . js

现在，当我们运行测试时，我们会注意到一个缺失的模块:

```
~/middleware-tests$ **yarn test**
yarn run v1.22.4
$ jest
 FAIL  middleware/hello.test.js
  ● Test suite failed to runCannot find module '../testSupport/serverSideApp' from 'hello.test.js'
```

没错——我们还没写`serverSideApp.js`。我们需要创建模拟 NuxtJS 的设置，为我们所有的中间件提供服务。让我们现在做那件事。

# 3.创建模拟服务器的测试支持文件

NuxtJS 的[文档写道:](https://nuxtjs.org/api/configuration-servermiddleware/)

> Nuxt 在内部创建了一个 [connect](https://github.com/senchalabs/connect) 实例，我们可以向其中添加我们自己的定制中间件。这允许我们注册额外的路由，而不需要外部服务器。

因此，对于我们的测试支持，我们想要创建一个[连接实例](https://github.com/senchalabs/connect)，它使用我们在`nuxt.config.js`中注册的所有中间件。我们上面的测试获取了那个实例(名为`app`)并将其交给 SuperTest，这样我们就可以四处查看并断言一些期望。

这个`app`就是我们需要在`testSupport/serverSideApp.js`中创建和导出的内容。让我们创建`testSupport`子文件夹:

```
~/middleware-tests$ **mkdir testSupport**
```

在这个文件夹中，我们创建了`serverSideApp.js`:

~/testSupport/serverSideApp.js

让我们看看上面的代码是做什么的。总的来说，我们正在创建一个 [Connect](https://github.com/senchalabs/connect) 实例，将它告知`nuxt.config.js`中定义的`use`各种中间件，然后我们导出该实例。

在`nuxt.config.js`的`serverMiddleware`数组中注册的中间件可以通过两种方式注册:

*   作为一个`string`，它是导出中间件处理函数的文件的位置——这个中间件没有挂载路径，这意味着它将应用于所有请求，而不管它们的路径。
*   作为带有`path`和`handler`的`object`。`path`是这个中间件将被应用的请求路径挂载点，而`handler`是中间件处理函数的位置。

在上面的代码中，我们检查了`serverMiddleware`数组中的每一项。如果它是一个`string`，我们导入处理函数并告诉我们的`app`到`use`它没有任何路径挂载点。如果它是一个`object`，我们导入在`handler`提供的位置找到的处理函数，并在`path`中定义的挂载点`use`它。

你可能熟悉与 Express 几乎相同的过程。这里没多大区别。我们选择使用 Connect，因为这是 NuxtJS 开箱即用的，我们希望我们的测试设置尽可能地反映生产设置。

顺便提一下，NuxtJS 文档还允许向`serverMiddleware`提供一个对象，而不是一个数组。使用这种对象格式是不太常见的方法——如果这是您决定要做的，您可以相应地调整您的`serverSideApp.js`代码。

现在，我们回到我们的测试:

```
~/middleware-tests$ **yarn test**
 FAIL  middleware/hello.test.js
  ● Test suite failed to runTypeError: _nuxt.default.serverMiddleware is not iterable
```

这是怎么回事？我们还没有在`nuxt.config.js`注册(或编写)我们的`hello.js`中间件。编辑`nuxt.config.js`，添加`serverMiddleware`配置属性如下:

将 serverMiddleware 属性添加到~/nuxt.config.js 中

现在，当我们运行测试时，我们被告知无法定位`hello.js`。我们的服务器中间件已经注册了，NuxtJS 正在找；但是我们还没有写出来。

是时候实现我们的中间件了。

# 4.实现中间件以通过测试

在`middleware/`文件夹中，我们实现了我们的中间件，编写`hello.js`:

中间件实现:~/middleware-tests/middleware/hello . js

现在，我们的中间件实现已经完成，让我们再次运行我们的测试:

```
~/middleware-tests$ **yarn test**PASS  middleware/hello.test.js
  GET /hello
    when query param "name" provided
      when "name" is blank
        ✓ responds with status code 500 (52ms)
      when "name" is not blank
        ✓ responds with status code 200 (9ms)
        response body
          ✓ is a JSON object with correct "name" and "message" (3ms)
    when query param "name" not provided
      ✓ responds with status code 500 (1ms)-----|---------|----------|---------|---------|-------------------|
File | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s |
-----|---------|----------|---------|---------|-------------------|
All  |       0 |        0 |       0 |       0 |                   |
-----|---------|----------|---------|---------|-------------------|Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        1.359s
Ran all test suites.
Done in 2.35s.
```

太好了。我们所有的测试都通过了。然而，你会注意到，测试**覆盖率**结果显示了一堆 0。这是因为当前的`jest`配置在计算代码覆盖率时没有考虑到我们的中间件文件。让我们通过给`jest.config.js`添加一行来改变这一点:

将中间件文件添加到~/jest.config.js 中的代码覆盖率配置中

我们添加了最后一行(上面要点中的第 20 行)来在收集覆盖数据时包含`middleware`子文件夹中的`.js`文件。现在，当我们运行测试时:

```
~/middleware-tests$ **yarn test
** PASS  middleware/hello.test.js
  GET /hello
    when query param "name" provided
      when "name" is blank
        ✓ responds with status code 500 (53ms)
      when "name" is not blank
        ✓ responds with status code 200 (9ms)
        response body
          ✓ is a JSON object with correct "name" and "message" (2ms)
    when query param "name" not provided
      ✓ responds with status code 500 (1ms)-------|---------|----------|---------|---------|------------------|
File   | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s|
-------|---------|----------|---------|---------|------------------|
All    |     100 |      100 |     100 |     100 |                  |
 hello.js |  100 |      100 |     100 |     100 |                  |
-------|---------|----------|---------|---------|------------------|
Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        1.439s
Ran all test suites.
Done in 2.27s.
```

你会注意到，我们的中间件文件包含在代码覆盖检查中，我们显示的是 100%。太好了！我们的第一个中间件有一个测试文件和一个让我们的测试通过的实现。但更重要的是，我们已经为中间件的可靠测试准备好了配置和基础设施。

简单回顾一下，以下是我们在本教程中编辑或创建的文件:

```
.
├── jest.config.js     *(added middleware files to code coverage)*
├── nuxt.config.js     *(registered middleware in serverMiddleware)*
├── package.json       *(added supertest package)*
testSupport/
└── serverSideApp.js   *(new)*
middleware/
├── hello.js           *(new)*
└── hello.test.js      *(new)*
```

让我们继续看一些其他的中间件及其测试的例子。

# 5.中间件和测试的其他示例

在本教程的最后一部分，我们将讨论一些在 NuxtJS 应用程序中使用的中间件的常见情况。对于每个中间件，我们将编写一个测试文件，然后是一个实现。我们将涵盖以下示例:

*   记录器
*   向请求添加时间戳
*   验证授权标题中的 JWT

同样，我们并不打算构建具有大量功能的超级复杂的中间件。我们只是想介绍一些基础知识，以便对测试有一个好的感觉。

## 示例 1:记录器中间件

对于这个例子，我们想要一个中间件，它将每个请求的`method`和`url`记录到控制台。我们希望这个中间件用于所有的路径，而不仅仅是特定的端点。

下面是我们的测试文件可能的样子:

记录器中间件的测试文件:~/middleware-tests/middleware/Logger . test . js

注意，我们利用 Jest 的`spyOn`功能来监视`console.log`。

有了我们的测试文件，我们需要做两件事来让这些测试变得绿色:

1.  创建我们的日志中间件实现文件。
2.  在`nuxt.config.js`中注册该中间件

我们的日志中间件实现非常简单:

记录器中间件实现:~/Middleware-tests/Middleware/logger . js

我们需要做的最后一件事是将这个中间件的条目添加到`nuxt.config.js`中的`serverMiddleware`数组中。因为我们希望我们的日志中间件用于所有的路径，我们简单地将它作为一个字符串添加，而不是作为一个带有 T3 和 T4 的对象。另外，`nuxt.config.js`将按照注册的顺序使用中间件，所以我们希望在注册我们的`hello`中间件之前*注册我们的日志中间件。修改`nuxt.config.js`如下所示:*

在~/nuxt.config.js 中注册记录器中间件

现在，当我们运行我们的测试时，我们应该看到所有的测试都通过了，并且 100%的代码覆盖率。

顺便说一下，由于我们将所有请求记录到`console`，您会注意到您的测试套件现在有了更多的`console.log`语句。为了消除这些问题，您可以将`--silent`标记添加到您的测试运行中:

```
~/middleware-tests$ **yarn test --silent
** PASS  middleware/hello.test.js
 PASS  middleware/logger.test.js
```

## 示例 2:请求时间戳

对于我们的第二个例子，我们想要获取传入的 HTTP 请求(`req`)并向其添加我们自己的`requestTime`属性。这个中间件应该在所有路径上使用。

首先，我们的测试文件:

请求时间戳中间件的测试文件:~/middleware-tests/middleware/Timestamp . test . js

这个测试有点棘手。我们假设这个时间戳中间件将在我们的请求通过的中间件处理程序管道的早期出现。此时，`req`被赋予了一个新的`requestTime`属性。但到目前为止，我们还没有任何端点使用或输出那个`requestTime`。那么，我们如何测试我们的时间戳中间件实际上做了它应该做的事情？

在我们的测试文件中，我们创建了一个返回`req.requestTime`的中间件`use`，使得这个中间件只适用于特定的路径。然后，我们向该路径发出请求，期望响应包含正确的时间戳。只有正确编写了`req.requestTime`,时间戳才会存在。请记住，我们在这里只`use`这个中间件*——*在这个测试文件中——作为一个临时的“窥视”请求的方法。

这是拥有来自`serverSideApp.js`的`app`的好处之一——当需要时，我们可以`use`额外的中间件来帮助我们探查`req`和`res`的状态，以便断言一些期望。

这个请求时间戳中间件的实现非常简单:

请求时间戳中间件实现:~/middleware-tests/middleware/timestamp . js

让我们记住在`nuxt.config.js`的`serverMiddleware`栈中注册这个中间件:

在 nuxt.config.js 中注册 timestamp.js 中间件

我们运行我们的测试，我们看到一切都通过了。让我们继续我们的最后一个例子。

```
~/middleware-tests$ **yarn test --silent**
 PASS  middleware/hello.test.js
 PASS  middleware/logger.test.js
 PASS  middleware/timestamp.test.js
```

## 示例 3:验证授权标题中的 JWT

对于我们的最后一个例子，让我们假设有一个受保护的路径(`/protected`)，它是一个 GET 请求端点，但是它需要一个有效的 [JSON Web 令牌](https://jwt.io/) (JWT)。如果 JWT 有效，响应正文应该包含 JWT 中编码的`userID`，响应将有一个状态代码`200`。否则，我们应该得到一个状态代码`401`。

JWT 的也应该用一个秘密签名，这样我们可以验证请求中提供的 JWT 确实来自一个可信的来源。

对于这个中间件，我们需要添加`[jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)`包:

```
~/middleware-tests$ **yarn add jsonwebtoken**
```

我们的测试文件可能是这样的:

JWT 授权中间件的测试文件:~/middleware-tests/middleware/protected . test . js

然后，我们在`protected.js`中的中间件的实现文件:

JWT 授权中间件的实现:~/middleware-tests/middleware/protected . js

关于上面两个文件中的`JWT_SECRET`,只需简单说明一下。显然，在两个不同的文件中定义`JWT_SECRET`是不明智的做法。应该有一个单一的真理来源——一个定义了一次`JWT_SECRET`的中心位置，任何其他需要它的代码都会引用它。此外，像`JWT_SECRET`这样敏感的信息根本不应该硬编码——它应该驻留在某个环境变量中，永远不要放入您的源代码库中。在上面的例子中，我这样做只是为了使本教程简单，并专注于手头的任务:学习如何测试中间件。

最后，不要忘记注册您的中间件:

我们整个测试套件的最后一次运行:

```
~/middleware-tests$ **yarn test --silent --verbose
** PASS  middleware/hello.test.js
  GET /hello
    when query param "name" provided
      when "name" is blank
        ✓ responds with status code 500 (34ms)
      when "name" is not blank
        ✓ responds with status code 200 (8ms)
        response body
          ✓ is a JSON object with correct "name" and "message" (8ms)
    when query param "name" not provided
      ✓ responds with status code 500 (8ms)PASS  middleware/protected.test.js
  GET /protected
    when header does not have authorization
      ✓ responds with status code 401 (7ms)
    when header has authorization
      when format not recognized
        ✓ responds with status code 401 (3ms)
      when format is Bearer token
        when JWT is invalid
          ✓ responds with status code 401 (3ms)
        when JWT is valid
          when JWT does not contain a userID
            ✓ responds with status code 401 (2ms)
          when JWT contains a userID
            ✓ responds with status code 200 (1ms)
            ✓ responds with userID as response text (2ms)PASS  middleware/logger.test.js
  Logger
    when request made to valid path
      ✓ logs request method and url to console (4ms)
    when request made to invalid path
      ✓ logs request method and url to console (4ms)PASS  middleware/timestamp.test.js
  Request Timestamp
    ✓ adds timestamp property to the original request (3ms)--------------|-------|--------|-------|-------|-------------------|
File          |% Stmts|% Branch|% Funcs|% Lines| Uncovered Line #s |
--------------|-------|--------|-------|-------|-------------------|
All files     |   100 |    100 |   100 |   100 |                   |
 hello.js     |   100 |    100 |   100 |   100 |                   |
 logger.js    |   100 |    100 |   100 |   100 |                   |
 protected.js |   100 |    100 |   100 |   100 |                   |
 timestamp.js |   100 |    100 |   100 |   100 |                   |
--------------|-------|--------|-------|-------|-------------------|
Test Suites: 4 passed, 4 total
Tests:       13 passed, 13 total
Snapshots:   0 total
Time:        1.899s
Done in 2.72s.
```

# 最终总结

我们的教程已经结束了。当我们开始时，我们知道我们想要利用 NuxtJS 的内置特性在服务器上容纳中间件，但是我们需要一个设置来帮助我们正确地测试我们的中间件。

我们经历了以下步骤:

1.  创建了一个新的 NuxtJS 应用程序。
2.  为我们的第一个中间件编写测试——一个基本 GET 请求的 API 端点(`hello.test.js`)。
3.  创建了`serverSideApp.js`，这是一个测试支持文件，它建立了一个 Connect 实例，该实例在`nuxt.config.js`中注册的所有中间件处理程序上调用`use`。
4.  修改了`jest.config.js`，将`middleware`子文件夹中的文件添加到我们的配置中，这样`jest`将在其代码覆盖检查中包含中间件文件。
5.  实现了我们的 GET endpoint 中间件(`hello.js`，确保我们在`nuxt.config.js`的`serverMiddleware`属性中注册了它。
6.  对于另外三个中间件示例，经历了相同的“编写测试、编写实现、注册中间件”过程:

*   一个日志记录中间件(`logger.js`)，它将请求的`method`和`url`记录到控制台。这个中间件在所有路径上运行。
*   一个请求时间戳中间件(`timestamp.js`)，它向`req`添加了一个`requestTime`属性，下游的其他中间件可以使用这个属性。这个中间件在所有路径上运行。
*   一个 JSON Web 令牌验证中间件(`protected.js`)，它检查请求是否包含带有有效签名的 JWT 的`authorization`报头。这个中间件运行在一个特定的路径上(`/protected`)。

有了这个中间件测试的基本框架，您就可以为下一个 NuxtJS 应用程序编写健壮且经过良好测试的中间件代码了。去改变世界吧。

*阿尔文·李*是亚利桑那州凤凰城的一名全栈开发人员和远程工作者。他专门从事 web 开发、技术咨询以及为初创公司和小型企业构建原型。他可以在 Moonlight 上找到，在那里你可以[查看他的个人资料](https://www.moonlightwork.com/app/users/2862/profile)或者[请求雇佣他提供服务](https://www.moonlightwork.com/r/2862)。