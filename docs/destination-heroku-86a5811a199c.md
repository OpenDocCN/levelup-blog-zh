# 目的地 Heroku

> 原文：<https://levelup.gitconnected.com/destination-heroku-86a5811a199c>

在“[从 AWS 迁移到 Heroku](/moving-away-from-aws-and-onto-heroku-d84852b9884d) ”一文中，我介绍了我想从亚马逊流行的 AWS 解决方案迁移到 Heroku 的应用程序。虽然 AWS 确实满足了我的客户(我的岳母)的需求，但我希望有一个解决方案，让我有限的时间能够专注于提供业务解决方案，而不是跟上开发运维流程。

# 快速回顾

作为一个 TL；(博士太长；没看)到原文章，我给婆婆开的小企业建了一个 Angular 客户端和一个 Java API。在 Elastic Beanstalk 和 S3 上运行应用程序一年后，我想看看是否有更好的解决方案，让我能够更加专注于编写功能和增强功能，而不必担心学习、理解和执行 AWS 生态系统中固有的类似 DevOps 的方面。

# 创建新帐户

我需要做的第一件事是在 [Heroku](https://www.heroku.com/) 创建一个新账户。这个过程非常简单，只需要最少的信息就可以开始:

![](img/5a7301e405475714ce379c4437a048c4.png)

一旦我的账户被创建并登录，我就可以使用“**创建新应用**”按钮正式进入隐喻的 Heroku 池:

![](img/f46ff79c511c3b4c134e34656d28f755.png)

新的申请流程要求输入名称(我选择了“amhs”)和地区(美国):

![](img/82c2ca96645746036a9729d457414777.png)

单击创建应用程序按钮后，我被重定向回 Heroku 主页:

![](img/c673a186c2e79fad5a2032de47af08d5.png)

有了现在创建的“amhs”应用程序，我现在可以更深入地研究这个过程了。

# 准备我的本地机器

为了准备我的本地 MacBook Pro 与 Heroku 交互，我继续使用 Homebrew 在终端中使用以下命令安装了 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) :

```
brew install heroku/brew/heroku
```

安装完成后，我发出以下命令，允许 Heroku CLI 使用我的信息:

```
heroku login
```

登录命令将与您的默认 web 浏览器交互，以完成身份验证过程。

```
heroku: Press any key to open up the browser to login or q to exit:
Opening browser to [https://cli-auth.heroku.com/auth/cli/browser/key-goes-here](https://cli-auth.heroku.com/auth/cli/browser/key-goes-here)
```

打开浏览器到[https://cli-auth.heroku.com/auth/cli/browser/key-goes-here](https://cli-auth.heroku.com/auth/cli/browser/key-goes-here)

验证后，将出现一个类似于下面所列的屏幕:

![](img/f38ac720d3291e115bb7c32108930b88.png)

终端会话将包含类似于以下注释的文本:

```
Logging in... doneLogged in as name@example.com
```

正如所料，使用 Heroku CLI 时也需要 git。因为我的其他开发工作，我已经安装了 git，但是这里有关于如何安装 git 的说明。

如果你的本地机器上有自制软件，你也可以使用`brew install git`。

# 设置 amhs 应用程序

在 Heroku 中打开 amhs 应用程序可以进一步配置应用程序:

![](img/e08b3c12fba7757c0d3fd9103367a568.png)

我首先知道我需要的是一个数据库。对于这个项目，我决定使用 MySQL。搜索插件并使用搜索字符串“MySQL”提供了以下选项:

![](img/94746ed01aa8d064bc5bef0532dd16ec.png)

通过一些快速研究，我决定坚持使用标准化的数据库，并选择了 ClearDB MySQL 选项。此时，我也选择了 Ignite-Free 选项:

![](img/59822c9ff658c00e5517b61eacb6aa4a.png)

*请注意——在成功提供附加服务之前，您需要有一张信用卡，该附加服务用于在出现问题时可靠地识别和联系客户。*

使用 MySQL 选项后，amhs 屏幕更新如下所示:

![](img/d6d60840d8bcc19785a322847310a55c.png)

也可以使用以下命令来完成这些相同的步骤:

```
heroku addons:create cleardb:ignite
```

Heroku CLI 实际上有一个实用程序，可以使用以下命令复制现有的 MySQL 数据库:

```
heroku addons:create cleardb:ignite --fork=mysql://user:password@myhostname.com/database
```

在这种情况下，我决定手动设置数据库并插入一些测试记录，这将在下一节中描述。

# 准备数据库

在 Heroku 中创建和运行了一个空的 MySQL 实例后，下一步是发送必要的 DDL 来创建表。

但是，我需要确定数据库的 URL。Heroku CLI 具有以下命令来完成此任务:

```
heroku config --app amhs
```

其中 Heroku CLI 的响应类似于:

```
=== amhs Config Vars
CLEARDB_DATABASE_URL: mysql://name:password@somehost.cleardb.net/heroku_someId?reconnect=true
```

有了这些信息，我就能够利用 DbVisualizer(我选择的 SQL 工具)来设置预期的表和基本配置数据。

在 Java API 端，我使用连接信息来更新 spring.datasource 值。

仍然在本地模式下，我重启了 Spring Boot Java API 和 Angular 应用程序的一个本地实例。登录后，我可以访问系统配置，查看我插入的测试值:

![](img/58cde577c91c4a308d618fc726fde9ab.png)

此时，Java API 在本地运行，但是在 Heroku 中访问新的 ClearDB 数据库。

# 为 Heroku 准备好 Java API

Java API 现在使用 ClearDB MySQL 数据库，下一步是更新基于 Spring Boot 的 API 以在 Heroku 上运行。

由于 AMHS 应用程序已经在 git 存储库中，我只需要执行下面的 Heroku CLI 命令，就可以让 API 将更新推送到在 Heroku 中创建的 amhs 应用程序中:

```
heroku git:remote -a amhs
```

Heroku CLI 的响应输出如下:

```
set git remote heroku to [https://git.heroku.com/amhs.git](https://git.heroku.com/amhs.git)
```

然后，我通过发出以下命令来验证远程配置:

```
git remote -v
```

git CLI 响应了以下信息:

```
heroku [https://git.heroku.com/amhs.git](https://git.heroku.com/amhs.git) (fetch)heroku [https://git.heroku.com/amhs.git](https://git.heroku.com/amhs.git) (push)origin git@gitlab.com:my-repo/amhs-api.git (fetch)origin git@gitlab.com:my-repo/amhs-api.git (push)
```

接下来，我创建了一个 Procfile，并将该文本文件放在 AMHS 存储库的根目录下。内容很简单:

```
web: java -jar target/amhs-api-2.0.0.jar
```

重要的值在“target/”的右边，它表示我的项目的 artifactId 和 POM 版本的连接。

一切准备就绪后，下一步是签入我的项目中的变更。由于我在一个名为 feature/heroku_demo *的分支中工作，*我执行了以下命令将我的代码(位于 feature/hero_demo 分支 *)* 推入 Heroku git 远程(目标是主分支):

```
git push heroku feature/heroku_demo:master
```

Heroku CLI 响应了以下信息。

```
Enumerating objects: 737, done.Counting objects: 100% (737/737), done.Delta compression using up to 16 threadsCompressing objects: 100% (586/586), done.Writing objects: 100% (737/737), 163.43 KiB | 5.11 MiB/s, done.Total 737 (delta 244), reused 0 (delta 0)remote: Compressing source files... done.remote: Building source:remote:remote: -----> Java app detectedremote: -----> Installing JDK 1.8... doneremote: -----> Installing Maven 3.6.2... doneremote: -----> Executing Mavenremote: $ mvn -DskipTests clean dependency:list installremote: [INFO] Scanning for projects...remote: [INFO] ------------------------------------------------------------------------remote: [INFO] BUILD SUCCESSremote: [INFO] ------------------------------------------------------------------------remote: [INFO] Total time: 47.107 sremote: [INFO] Finished at: 2020-04-18T18:31:39Zremote: [INFO] ------------------------------------------------------------------------remote: -----> Discovering process typesremote: Procfile declares types -> webremote:remote: -----> Compressing...remote: Done: 91.2Mremote: -----> Launching...remote: Released v5remote: [https://amhs.herokuapp.com/](https://amhs.herokuapp.com/) deployed to Herokuremote:remote: Verifying deploy... done.To [https://git.heroku.com/amhs.git](https://git.heroku.com/amhs.git)* [new branch] feature/heroku_demo -> master
```

# 验证 API

回顾 Heroku Overview 屏幕，我可以看到我的构建不仅成功了，而且代码也部署好了！

![](img/28a5c720254e7cdd69082813037896d5.png)

然后，我可以使用 [Postman](https://www.postman.com/) 发出以下 GET 请求:

```
[https://amhs.herokuapp.com/staff?sortColumn=lastName&sortDirection=asc&status=active](https://amhs.herokuapp.com/staff?sortColumn=lastName&sortDirection=asc&status=active)
```

它返回了 200 HTTP 状态代码和以下数据:

```
[{"id":1,"firstName":"Amy","lastName":"Admin","nickName":"","role":{"id":3,"name":"Admin"},"manager":null,"active":true,"created":"2020-04-18T13:31:06.000+0000","updated":"2020-04-18T13:31:16.000+0000","initials":"AA"},{"id":3,"firstName":"Alice","lastName":"Agent","nickName":"","role":{"id":2,"name":"Agent"},"manager":{"id":2,"firstName":"Marty","lastName":"Manager","nickName":"","role":{"id":1,"name":"Manager"},"manager":null,"active":true,"created":"2020-04-18T13:31:20.000+0000","updated":"2020-04-18T13:31:28.000+0000","initials":"MM"},"active":true,"created":"2020-04-18T13:31:35.000+0000","updated":"2020-04-18T13:31:44.000+0000","initials":"AA"},{"id":2,"firstName":"Marty","lastName":"Manager","nickName":"","role":{"id":1,"name":"Manager"},"manager":null,"active":true,"created":"2020-04-18T13:31:20.000+0000","updated":"2020-04-18T13:31:28.000+0000","initials":"MM"}]
```

接下来，我更新了 Angular 客户端中的`environment.ts`文件，以使用相同的 Java API，并且我能够看到相同的数据，现在我的本地 Spring Boot Java API 不再运行:

![](img/00fd3e98239331de2452177a713bb4b5.png)

# 添加安全和环境变量

以确保我没有处理 Okta 集成的安全问题。我已经移除了 Java API 的安全性。现在，我想包含必要的更新以确保 Java API 再次安全。

在 REST 控制器级别，我只需包含以下注释:

```
@PreAuthorize("hasAuthority('AmhsUser') || #oauth2.hasScope('openid')")
```

接下来，我在 Heroku 配置中为 amhs 应用程序设置必要的键/值对:

![](img/cf67370f18dc9a9a5eae876313d34daf.png)

最后，我必须在`application.yml`中引用这些变量:

```
security:oauth2:client:clientId: ${CLIENT_ID}clientSecret: ${CLIENT_SECRET}resource:tokenInfoUri: ${TOKEN_INFO_URI}spring:datasource:url: ${CLEARDB_DATABASE_URL}server:port: ${PORT}
```

虽然我的`application.yml`的其他值被隐藏了，但是我也想包含`server.port`引用，因为记住这一点很重要。

现在，查看 Heroku 中的概览屏幕，您可以看到所做的更新:

![](img/1a893cc7b91f0b6f03904ae611f6afc9.png)

要从 Java API 查看日志，只需发出以下命令:

```
heroku logs --tail
```

它产生以下输出:

```
2020-04-19T14:12:15.000000+00:00 app[api]: Build started by user name@example.com2020-04-19T14:12:41.789903+00:00 heroku[web.1]: Restarting2020-04-19T14:12:41.793282+00:00 heroku[web.1]: State changed from up to starting2020-04-19T14:12:41.526199+00:00 app[api]: Deploy 6d365207 by user name@example.com2020-04-19T14:12:41.526199+00:00 app[api]: Release v11 created by user name@example.com2020-04-19T14:12:48.587250+00:00 app[web.1]: Setting JAVA_TOOL_OPTIONS defaults based on dyno size. Custom settings will override them.2020-04-19T14:12:48.591623+00:00 app[web.1]: Picked up JAVA_TOOL_OPTIONS: -Xmx300m -Xss512k -XX:CICompilerCount=2 -Dfile.encoding=UTF-82020-04-19T14:12:50.722260+00:00 app[web.1]:2020-04-19T14:12:50.722261+00:00 app[web.1]: :: AMHS API :: Running Spring Boot 2.0.3.RELEASE :: Port #9095 ::2020-04-19T14:12:50.722406+00:00 app[web.1]:2020-04-19T14:12:49.000000+00:00 app[api]: Build succeeded2020-04-19T14:12:50.982144+00:00 app[web.1]: 2020-04-19 14:12:50.978 INFO 4 --- [ main] com.amhs.Application : Starting Application v2.0.0 on 3d90674b-0075-4d18-a6e0-91b6ca68bc3f with PID 4 (/app/target/amhs-api-2.0.0.jar started by u6224 in /app)
```

看到我运行的是 Spring Boot 2.0.3，我的任务清单上肯定增加了一个 Spring Boot 更新。:)

# 部署 Java API 的新方法

我想研究 Heroku 而不是继续使用 AWS 的主要原因之一是它能够专注于我客户的业务需求。做全职工作和从事自由写作项目并没有给我提供太多的额外时间——尤其是家里有一个蹒跚学步的孩子。基本上，我想找到一个解决方案，不需要花费太多的精力来为我的岳母部署变更。

一切就绪后，amhs 应用程序的部署已简化为以下 git 命令:

```
git push heroku master
```

这很容易记住，因为我通常将我的更改合并到 master 中。

# 结论

当我回顾我之前的说明(对于 AWS)时，以下步骤不再需要:

*   手动构建供 Elastic Beanstalk 使用的 JAR 文件
*   导航到弹性 Beanstalk UI
*   手动定位并上传在上述步骤中创建的 JAR 文件
*   验证版本匹配(`pom.xml`和 JAR 版本)
*   手动按下部署按钮

虽然这些步骤并不十分痛苦，但每次我想发布新代码供我岳母使用时，它们都需要我花费时间来执行。然而，使用 Heroku，我需要做的只是执行一个我已经熟悉使用的简单 git 命令。还有快速“回滚”到早期版本的能力——我将在以后的文章中详述。对我来说似乎是个胜利。

成本并不是这个实验的核心焦点，但是我觉得我应该指出，这里部署的所有东西都不会产生任何月成本。不错，当你考虑到我的 Heroku 解决方案在日常基础上需要的工作更少时。

在下一篇文章中，我将重点介绍 Angular 客户端，以及如何使用 Heroku 代替 AWS S3 来存放 Angular 和相关库使用的静态文件。

祝你今天过得愉快！