# 以大摇大摆的方式同步 Angular 和 Spring Boot API 规范——只需按一下按钮

> 原文：<https://levelup.gitconnected.com/synchronize-backend-and-frontend-api-interfaces-the-swagger-way-at-the-push-of-a-button-e3b96dbf07cb>

![](img/b07101226380f1a1c9ff1b61f2db14e8.png)

FreeImages.com/Per·哈德斯坦

在本文中，我想介绍一种不费吹灰之力就能动态更新客户机服务和 dto 的方法。不管框架和编程语言如何，客户端都需要知道它所使用的端点的规范。关于端点的消费，首先必须澄清一些问题。这些可能是:

*   该服务支持哪种 HTTP 方法？
*   需要哪种内容类型？
*   出错时返回哪些 HTTP 状态，等等。

似乎这还不够，客户端必须适应服务端点的变化。否则，可靠的通信将不再可能。但是为什么端点要改变呢？这里有几个例子:

*   端点的重命名。
*   更改 HTTP 响应状态。
*   DTO 将通过附加属性进行扩展。
*   内容类型已更改。

我可以列出更多的可能性，但是现在问题应该很清楚了。基于 Swagger，服务和 dto 的生成可以通过最少的配置工作实现自动化。下面我想介绍一种解决方法。

为了简单说明，让我们考虑下面的用例。以前在 Angular 前端收集的用户数据应该通过 HTTP PUT 作为 DTO 传输到 RESTful 服务端点，这是在 Spring Boot 实现的。如果数据处理成功，端点返回 HTTP 状态 200，否则返回错误状态，例如 503。

让我们来看看哪些实现是确保这种交互所必需的:

*   Angular 前端中的客户端 HTTP 请求作为服务实现。
*   用于数据交互的 dto。
*   后端的 RESTful 端点。
*   当然还有 API 接口的文档。

我们现在已经创建了一个粗略的技术基础来深入研究代码。接下来，基于由 Angular 应用和 Spring Boot 后端服务组成的客户端-服务器模型来解释该过程。如果应用程序还没有安装好，下一章会有所帮助。

# 快速配置

如果客户端和后端程序已经存在，你可以跳过这一章。如果没有，下面的说明会有所帮助。请记住，在设置应用程序时，本文无法深入探讨。重点是 Java 类的配置和设置。

## 创建 Spring Boot 应用程序

基本框架可以通过 [start.spring.io](http://start.spring.io) 轻松创建。🚀到随时可用的[配置](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.2.7.RELEASE&packaging=war&jvmVersion=11&groupId=com.example&artifactId=demo&name=demo&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.demo&dependencies=web,lombok)。

## 设置角度应用程序

创建应用程序最简单的方法是使用 [Angular CLI](https://cli.angular.io/](https://cli.angular.io) 。

我们现在有一个后端和前端应用程序。前端和后端之间的交互仍然缺失。我们还得为此做些安排。

# Spring Boot 申请的准备

在 pom.xml 中，我们为 Swagger 增加了两个库:

Springfox 主要用于使用 Java 注释生成现成的 API 规范，例如 Swagger，尽管也支持其他工具。我将跳过处理 Springfox 配置的步骤。相反，我将参考详细记录此[的官方文档](http://springfox.github.io/springfox/docs/current/)。

我们从 Spring Boot 应用程序开始

```
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

我使用单独的*application-dev . properties*文件进行开发。当然你可以用任意一个。尽管如此，Spring Boot 应用程序现在应该正在运行！

下一步是根据开头描述的用例创建一个端点。下面实现的控制器操作应该接收数据有效负载并做进一步的处理，比如更新数据库条目(不在范围内)。

UserApiController.java

T 类`UserApiController.java`包含一个新的端点，它期望查询参数中的用户的 *ID* 和 HTTP 主体中的用户数据的有效负载。这里需要特别注意注释[*@ API operation*](http://docs.swagger.io/swagger-core/v1.5.0/apidocs/io/swagger/annotations/ApiOperation.html)，因为在生成客户端接口时会用到其中描述的信息。例如， *operationId* 中的值被用作 Angular 服务类中服务函数的名称，但稍后将对此进行详细介绍。

如果配置已经正确完成，并且控制器类已经创建，那么 swagger 文档应该可以通过端点获得

```
[http://localhost:8090/swagger-ui.html](http://localhost:8090/swagger-ui.html)
```

请确保输入了正确的端口。正如您将看到的，新的端点现在应该已经列在 API 文档中了。现在开始激动人心了。通过网址

```
[http://localhost:8090/api-docs](http://localhost:8090/api-docs)
```

你得到了开放 API 规范。在本例中，它被生成为 JSON(也支持 YAML)。

接下来，Angular 客户端接口和 DAO 应该基于 OpenApi JSON 自动生成。为此，应该在 Angular 项目中将 JSON 保存为 *swagger.json* 。我推荐 Angular 项目目录下的一个子目录，比如 *swagger-config* 。

# 为 Angular 应用程序生成客户端服务和 dto

要从 JSON Open API 规范生成客户端文件，需要[*swagger-codegen-CLI . jar*](https://github.com/swagger-api/swagger-codegen)[。](https://github.com/swagger-api/swagger-codegen](https://github.com/swagger-api/swagger-codegen)).)由于规范是在 OpenAPI 2.0 中创建的，所以您需要版本为 2.x.x 的 jar 文件。在文件夹 *swagger-config* 中，可以执行以下命令来下载该文件:

```
wget https://repo1.maven.org/maven2/io/swagger/swagger-codegen-cli/2.4.14/swagger-codegen-cli-2.4.14.jar -O swagger-codegen-cli.jar
```

因为您必须向 jar 文件传递一些参数，并且我们不想一次又一次地这样做，所以 shell 文件是合适的。为此，在文件夹中创建一个名为 *swagger-client-api.sh* 的文件，其内容如下:

```
java -jar ./swagger-codegen-cli.jar generate \
 -i./swagger.json.json \
 -l typescript-angular \
 -o ../src/app/client
```

这些参数的含义如下:

*   `-i`是我们之前放在文件夹中的 OpenAPI JSON 文件的名称。
*   `-l`是客户端服务和 DTO 文件的目的地。最棒的是生成器支持各种客户端框架。
*   `-o`最后是存放文件的目标文件夹。

现在用`sh swagger-client.api.sh`执行 shell 文件。Et voilà，我们应该手动编程的 angular 客户端和 DTO 已经在文件夹`../src/app/client`中生成。

这就是生成的 TypeScript 函数(它应该执行请求)对于 Angular 应用程序的样子。

以及相应的模型:

# 我们赢了什么？

我们现在能够对影响端点的变化做出快速反应。因此，只要“按下按钮”，客户端界面就可以更新。这使得我们可以专注于基本的任务，比如实现业务逻辑。

# 最后几句话

在本文中，OpenAPI 规范来自 java 类注释。然而，首先创建规范，然后生成后端和客户机文件，这并没有什么不好。对于后端类的初始创建，这可能仍然是可取的，但是随着开发的进展，应该重新考虑这一点。因为 Spring Boot 端点包含自包含的逻辑，比如通过实体 bean 类的数据持久性，所以您不希望用生成的类覆盖它们。

对于客户端文件，情况有所不同。生成的服务函数返回可在不同上下文中使用的可观察值。因此，附加逻辑不是在生成的客户端服务中实现的，而是围绕它们实现的。因此，完全可以覆盖它们。

然而，这并不意味着由 Swagger CLI 生成 Java 类没有用。这完全取决于项目需求和开发进度。

☕️，祝你体验愉快！