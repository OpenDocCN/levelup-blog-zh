# 使用 JSON Web 令牌和 Twilio Authy 保护 Node.js 上的 JavaScript 微服务

> 原文：<https://levelup.gitconnected.com/protecting-javascript-microservices-on-node-js-with-json-web-tokens-and-twilio-authy-90d8ed780f5e>

![](img/5c0ca65a62dba270d4b3b36b02b39807.png)

使用微服务架构在 Node.js 上构建 JavaScript 应用程序可以让您扩展应用程序:您可以通过添加和删除服务实例来响应不同的负载水平。当一个服务的多个实例访问相同的数据时，您的应用程序可以使用一个公共的持久层(数据库)在实例之间共享信息，并保持它们之间的一致性。

在许多应用程序中，您还希望限制特定用户对某些数据和功能的访问。虽然您可以直接在您的服务 API 中这样做，但是有一个更好的方法。

这篇文章将向您展示如何使用 Twilio Authy 和一个使用 [JSON web 令牌](https://jwt.io/introduction/) (JWTs)的授权 Web 服务添加双因素身份验证(2FA)，这是一个安全存储和传输敏感数据的标准。

# 使用 Twilio Authy 进行身份验证

[Twilio Authy](https://www.twilio.com/authy) 使用推送认证来验证用户的身份。推送认证优于密码，因为它们要求用户能够访问已知与用户相关联的第二因素设备。Authy 还可以配置为使用通过 SMS 或语音传输的软令牌和一次性密码。

# 使用 JSON Web 令牌(jwt)进行授权

jwt 使您的服务能够使用公钥/私钥加密来传输关于用户和其他数据对象的信息，以提供数据*安全性*和令牌签名来确保数据*完整性*。因为 jwt 可以携带关于用户及其授权声明的信息，所以它们可以通过启用服务来避免重复调用授权服务，从而提高应用程序的性能。授权服务可以将用户的授权声明存储在 JWT 的有效负载中。接收带有令牌的 API 调用的服务可以对令牌进行评估，从而确定调用者是否可以访问服务的数据或方法。

在这篇文章中，你将学习如何使用一个*签名的* JWT，它由 JSON 对象结构的三部分组成:

*   标头—用于加密令牌的算法的标识
*   有效负载—存储在令牌中的信息
*   签名—报头和有效负载的加密签名

这三个部分是 Base64Url **编码的**，由点分隔符(`.`)分隔，这使得令牌可以作为字符串值与其他系统和应用程序轻松交换，如下所示:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

如果能够访问令牌的公钥，其他系统可以轻松解码令牌并读取其内容。如果服务或应用程序可以访问公钥(由用于标记令牌的私钥生成)，它可以通过检查签名是否匹配来验证内容是否被修改。

拥有*公钥*的实体不能对令牌进行签名，因此不能改变令牌内容；如果有效负载内容发生变化，签名将不会匹配。签名证明只有持有私钥的一方签署了令牌。JWT 规范的完整描述可以在 [RFC 7519](https://tools.ietf.org/html/rfc7519) 文件中找到。

这篇文章中的代码使用了 [OpenSSL](https://www.openssl.org/) 中的密码库来生成加密和解密的密钥，但是使用它并不需要你对这个库或者密码有太多的了解。您也不需要执行复杂的安装或构建过程来使用加密技术。

# 先决条件

为了完成这篇文章中的任务，你需要:

*   [Node.js 和 npm](https://nodejs.org/)(node . js 安装也会安装 NPM。)
*   [Git](https://git-scm.com/downloads) (对于 Windows 用户，Git 安装还会安装一个 OpenSSL 可执行文件。)
*   [Java SE 运行时环境](https://www.java.com/en/download/)
*   [MongoDB Atlas 账户](https://cloud.mongodb.com/user)(选择自由层集群。)
*   Twilio 账户(有一个免费的开发者账户。)
*   一个 [Twilio Authy](https://www.twilio.com/authy) API 密钥和一个注册用户(也是免费的，参见下面的说明。)

要从这篇文章中最有效地学习，你应该具备以下条件:

*   JavaScript、Node.js 和数据库的工作知识
*   对 HTTP 协议的一些暴露
*   [微服务架构的基础知识](https://medium.com/swlh/building-javascript-microservices-with-node-js-d88bf0bb2b92)

这篇文章是建立在关于使用 Node.js 微服务构建生产就绪型应用程序的前三篇文章的代码和概念之上的:

[用 Node.js 构建 JavaScript 微服务](https://medium.com/swlh/building-javascript-microservices-with-node-js-d88bf0bb2b92) —介绍微服务的概念，并向您展示如何在 Node.js 上用 JavaScript 构建微服务应用

[在 Node.js 上运行的 JavaScript 微服务中为服务发现和动态路由实现 Eureka 和 Zuul](https://medium.com/javascript-in-plain-english/implementing-eureka-and-zuul-for-service-discovery-and-dynamic-routing-in-javascript-microservices-45a7ac18837a)—演示如何将网飞 Eureka 服务注册中心和网飞 Zuul 的动态路由和负载平衡集成到微服务应用中。

[Scaling Node.js JavaScript 微服务共享一个 MongoDB Atlas Cloud 持久层](https://medium.com/javascript-in-plain-english/scaling-node-js-javascript-microservices-on-shared-mongodb-atlas-cloud-persistence-layers-620bf975ebfd) —展示如何跨多个服务实例共享数据以实现一致性和同步。

如果您已经阅读了之前的帖子并遵循了项目的构建，您可以继续使用您现有的代码，或者从 GitHub 上的[配套库](https://github.com/maciejtreder/introduction-to-microservices)重新开始。

如果您不熟悉这些文章标题中的概念，您可能希望阅读它们并一步一步地构建项目。

如果您熟悉微服务，并且希望专注于学习实现授权服务和 JSON Web 令牌，那么配套的资源库中有您开始这篇文章所需的代码。下面的说明将向您展示如何编写本系列前三篇文章中的代码。

# 微服务项目的架构

案例研究项目由四个服务和一个作为持久层的 MongoDB 数据库组成。Node.js 上运行着两个 JavaScript 服务:

维护超级英雄的列表、他们的属性以及他们的忙/闲状态。

`threats-service`–维护一个危险情况列表，以及负责应对各种威胁的英雄。

这些服务使用一个助手函数`registerWithEureka`，这使得它们能够方便地连接到服务发现系统。这些组件共同构成了应用程序的功能方面。

还有两个 Java 服务为 Node.js 服务提供基础设施支持:

**网飞尤里卡** —是一个服务注册和发现工具，使服务能够找到其他服务，而不需要知道它们在哪里运行。

网飞·祖尔是一个负载平衡器和动态路由服务，它从尤里卡获得每个服务的可用实例列表，在服务之间路由请求，并在服务器实例之间平衡负载。

这些 Java 服务是在 Java 归档文件中提供的。jar)文件:一旦安装了 Java SE 运行时环境，您需要做的就是运行它们。它们是打包的，所以您可以在自己的项目中使用它们，无需修改。

该项目的架构如下所示:

![](img/6674e7ddb3d3ce33676b6d819f9eb35f.png)

# 设置微服务项目

通过在您想要创建项目目录的目录中执行以下命令，从[伴随库](https://github.com/maciejtreder/introduction-to-microservices)中克隆代码:

```
git clone [https://github.com/maciejtreder/introduction-to-microservices.git](https://github.com/maciejtreder/introduction-to-microservices.git)
cd introduction-to-microservices
git checkout step5
cd heroes-v2
npm install
cd ../threats-v2
npm install
cd ../eureka-helper
npm install
cd ..
```

请注意，这些步骤包括在适当的目录中安装所需的 npm 软件包。

如果您没有按照上一篇文章中的步骤构建项目，那么您需要创建 MongoDB 数据库实例并向其中插入数据。如果你*一直在关注此事——并为此感到高兴——你可以跳过以下部分。*

# 设置 MongoDB 持久层数据库

导航至[https://cloud.mongodb.com/user](https://cloud.mongodb.com/user)并登录您的账户。要注册新集群，请点击*构建新集群*，然后选择最合适的地区和云基础设施提供商。(您可以选择“可用的免费层”区域，以避免运营费用。)

当您的集群准备就绪时，单击 **Connect** 按钮，并按照 *Connect to Cluster()* 面板上的提示:1)将您的连接 IP 地址列入白名单，2)创建一个 MongoDB 用户。

**提示:**创建密码时，避免使用在系统命令行中输入时必须进行 URL 编码的字符。请务必将您的 MongoDB 用户名和密码保存在安全的地方。

在*选择连接方式*步骤中，选择*与 Mongo Shell* 连接。按照适当的说明下载、安装和配置 MongoDB Shell，以便在您的系统上运行。

当您将 MongoDB Shell 配置为在您的系统上运行时，将提供的命令行指令复制到一个安全的地方。在*连接到集群()*对话框打开的情况下，保持该选项卡打开；你很快就会回来。

在控制台窗口中执行命令行指令来启动 shell。您需要输入刚刚创建的用户密码。

**Windows 用户提示:**在 PowerShell 窗口中执行下面的 MongoDB 命令行指令；这将使您能够在一个步骤中粘贴并执行整个指令块。

成功连接到 MongoDB 实例后，通过执行以下 MongoDB 命令行指令向其添加数据:

让这个 MongoDB 控制台窗口保持打开，您稍后将会使用它。

回到你的 MongoDB Atlas 账户控制台，点击集群旁边的**连接**按钮。在*选择连接方法*步骤中，选择**连接您的应用程序**。在*选择您的驱动程序版本*下，选择的值应该是:

驱动程序:Node.js

版本:3.0 或更高版本

仅选择**连接字符串**并点击**复制**按钮。保存连接字符串；稍后您将使用它将 Node.js 服务连接到 MongoDB Atlas 数据库。

根据您选择的云服务，连接字符串应该如下所示:

```
mongodb+srv://<userid>:<password>@cluster0-se0aa.azure.mongodb.net/test?retryWrites=true
```

在代码编辑器中打开*/微服务简介*目录。找到 *threats-v2/threats.js* 和 *heroes-v2/heroes.js* 文件，并在每个文件中查找以下代码行:

用从 MongoDB Atlas*Connect to Cluster*对话框中获得的连接字符串的 URL 部分替换`dbUrl`赋值的占位符文本。

将`<password>`占位符替换为与您在 MongoDB Atlas 控制面板中创建的用户名相对应的密码([https://cloud.mongodb.com](https://cloud.mongodb.com/))。请注意，您不应该硬编码密码。这里这样做是为了简单起见，并使您的调试更容易。

最佳实践是将密码存储为环境变量。使用这种技术，您的 MongoDB 连接 URL 将类似于:

# 启动微服务应用程序

通过在应用程序的根目录中执行以下命令行指令来启动 Eureka。这将是*微服务简介*目录，除非您在克隆存储库时更改了目标目录。

Windows 用户应该在 Windows 命令提示符(cmd.exe)窗口而不是 PowerShell 窗口中执行以下指令以及所有后续指令，除非另有说明。

```
java -jar eureka-service-0.0.1-SNAPSHOT.jar
```

在应用程序的根目录中打开第二个控制台窗口，通过执行以下命令行指令启动 Zuul 服务。这将是您打开的第二个控制台窗口:

```
java -jar zuul-0.0.1-SNAPSHOT.jar --eureka.instance.leaseRenewalIntervalInSeconds=30
```

注意启动 Zuul 时使用的参数。它减少了 Zuul 从 Eureka 检索服务信息的时间间隔。这在开发环境中很有用，但是在生产环境中，您应该坚持使用默认设置。Spring 文档提供了为什么注册服务的过程相对[缓慢](http://projects.spring.io/spring-cloud/spring-cloud.html#_why_is_it_so_slow_to_register_a_service)的背景。

在项目根目录中打开第三个控制台窗口，并执行以下命令行指令:

```
node threats-v2/threats.js 5000
```

在项目根目录中打开第四个控制台窗口，并执行以下命令行指令:

```
node heroes-v2/heroes.js 5001
```

通过在一个新的控制台窗口(第五个窗口)中从项目根目录执行以下 curl 命令，检查是否一切正常:

```
curl -i --request POST --header "Content-Type: application/json" --data "{\"heroId\": 1, \"threatId\": 1}" localhost:8080/threats-service/assignment
```

如果服务运行正常，您应该会看到类似于 curl 的以下控制台输出的结果:

```
HTTP/1.1 202 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"79-ER1WRPW1305+Eomgfjq/A/Cgkp8"
Date: Fri, 05 Apr 2019 18:05:54 GMT
Content-Type: application/json;charset=utf-8
Transfer-Encoding: chunked{"id":1,"displayName":"Pisa tower is about to collapse.","necessaryPowers":["flying"],"img":"tower.jpg","assignedHero":1}
```

# 实现基于 JWT 的授权服务

应用程序的*授权*部分决定一个经过身份验证的用户是否有权访问一个 API 的端点。

要在这个应用程序中实现授权服务，您需要向数据库添加几个管理员。在下一节将要学习的身份验证和授权过程中，您将检索到关于它们的信息。

使用您之前打开的 MongoDB CLI 控制台窗口，执行以下命令:

让此控制台窗口保持打开，您稍后将会使用它。

第一个记录`id:1`的密码数据元素是字符串`abc1234!`的阿沙-3 散列，它是`admin1`用户的密码，第二个记录的密码数据元素是密码`1234!abc`的散列。不要在数据库或其他地方以纯文本的形式存储密码。相反，应将密码存储为哈希值。您可以使用 [SHA-3 散列生成器](https://www.browserling.com/tools/sha3-hash)来生成散列。

回到你的代码编辑器(或者，如果你是现代人，回到你的 IDE)。

在项目的根目录*/微服务简介*下，新建一个目录 *auth* ，并在其中新建一个文件 *auth.js* 。

在 */auth* 目录中打开一个控制台窗口，通过执行以下命令行指令初始化 npm 项目并安装必要的依赖项:

```
npm init -y
npm install body-parser express mongodb jsonwebtoken authy
```

因为您将使用 JSON Web 令牌在服务之间交换关于授权用户的数据，所以您需要一对私有和公共密钥。要使用 OpenSSL 生成它们，请在项目根目录中执行以下命令。

Windows 用户注意:如果你已经安装了 Git，你可以在*C:\ Program Files \ Git \ usr \ bin*目录中找到*openssl.exe*可执行文件。注意，这个目录可能不包含在您的路径中，所以您需要将它添加到命令行指令中。

```
openssl genrsa -out ./auth/private.key 2048
openssl rsa -pubout -in ./auth/private.key -out ./public.key
```

您将`private.key`放在了 */auth* 目录中，因为它将只被`auth-service`用来签署令牌。`public.key`文件存储在根目录中，因为它将被所有其他需要用这个公钥检查令牌签名的服务使用。

通过将以下 JavaScript 代码插入到 *auth.js* 文件中来实现授权服务:

`auth-service`有几个方面需要特别注意。还有一行你需要修改。

在文件的顶部，就在导入所需的模块之后，常量`port`被分配给这个服务将要运行的端口号，这个端口号是在服务启动时在命令行上设置的。

接下来，`app`常量初始化 [Express](https://expressjs.com/) 应用程序。

`auth-service`使用持久层来获取管理员组中用户的信息，因此它需要访问云数据库。MongoDB 连接的初始化方式与其他服务相同。

将下面代码行中的占位符替换为您在其他服务代码中相同位置使用的连接字符串，并将`<password>`占位符替换为您在 MongoDB Atlas 控制面板中为用户创建的密码:

连接字符串用于创建 MongoDB 客户端的新实例，如果有连接问题，就会抛出一个错误，如下所示:

`auth-service`的本质是 */auth* 端点:

常量`privateKey`被设置为您用 OpenSSL 创建的文件中的值。它是用来签署代币的。

代码使用由用户提交并在 HTTP POST 请求中传递的`userId`和`password`在数据库中查找用户。如果值不匹配，服务将返回 HTTP 响应代码 403 未授权。否则，服务将构建一个包含三个声明的 JSON Web 令牌:

`exp`(expiration)——这是一个*注册的*声明，表明令牌应该有效多长时间(上面的代码将其设置为 10 分钟有效)。)

`privileges`–这是一个*私有*声明，用一个字符串数组表示用户被授予了什么特权

`iat`(**I**issued**at**)–这是由库自动生成的*注册的*声明，用于标识令牌生成的时间。

您可以在 [jwt.io 简介](https://jwt.io/introduction/)页面上对声明类型之间的差异进行简要概述，或者在 RFC 7519 的[第 4 节中了解更多关于注册声明和公开声明的确切定义。](https://tools.ietf.org/html/rfc7519#section-4)

在`auth-service`代码中有两个最后的步骤:

1.  通过调用`eureka-helper`服务向 Eureka 服务注册中心注册服务，这是项目基线代码的一部分，以及
2.  启动服务时，在命令行上指定的端口上启动 Express 应用程序:

在这一点上，你应该仍然有`heroes-service`和`threats-service`运行。您之前在单独的控制台窗口中启动了它们。

打开一个新的控制台窗口，在项目根目录下执行以下命令行指令启动`auth-service`:

```
node auth/auth.js 5004
```

通过向 */auth* 端点发送以下否定测试来测试它(该测试将导致授权失败):

```
curl -i --request POST --header "Content-Type: application/json" --data "{\"userId\": \"admin2\", \"password\": \"bad_pass\"}" localhost:8080/auth-service/auth
```

卷曲输出应类似于:

```
HTTP/1.1 401 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"f-gnkbM9TfQtU2OeaokCx158AlciA"
Date: Fri, 10 May 2019 13:10:42 GMT
Content-Type: text/html;charset=utf-8
Transfer-Encoding: chunkedUnauthorized.
```

HTTP 401 响应代码表明`userId:admin2`和`password:bad_pass`未能通过数据库验证。如果测试产生了预期的结果，则服务处于活动状态，并且正在检查凭据。

使用存储在 MongoDB 数据库中的值执行正面测试:

```
curl -i --request POST --header "Content-Type: application/json" --data "{\"userId\": \"admin2\", \"password\": \"6015ee55ebac72d737fcec8327e40445dbf6c4cb2dde092e00c72becfa8bb827bdfe09d0490ed58666f098eb925f5aa64cf2676ca39758f445356c39b6964e45\"}" localhost:8080/auth-service/auth
```

卷曲输出应类似于:

```
HTTP/1.1 200 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"1e1-0CxrLFqDII6JbKBtGcpBZtNElbU"
Date: Thu, 02 May 2019 20:05:37 GMT
Content-Type: application/json;charset=utf-8
Transfer-Encoding: chunked{"jwtToken":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NTY4MzExMzcsInByaXZpbGVnZXMiOlsiQVNTSUdOX0hFUk8iXSwiaWF0IjoxNTU2ODI3NTM3fQ.AixOGTxHvL503gselDc9SF4NNXp_7Ypg1D65dyMa__z54dzoQ3-G8Dw83DcDK_7SVM8P0mY24pceumM9JanlFaYVgUSwJpue1waCG1E_i-0bsOjHlMYIaW9LoHDLT8_d_3THOX4IFgDoXpBj0kIyRUIjGSNI1QKted-zPy2V0mOKAgHvTyUg5xq_5ziS8I2W8pjILjJRfpb_a9Djuv9h0qIkHoyaFX11sANrfF68B8Ha3SOIzDLjgd31LR-8ymFODMpQv79XP6JU7nbeMnyothCG-CFLxnuPxSoefSAoAs2O_fA9GddqUntLZdzQcSdRzk-h_vkJorvuM_vovGcVOA"}
```

HTTP 200 响应代码表示在数据库中成功找到了用户的凭据。端点返回一个 JWT，其中包含提供用户信息的声明以及两个标准声明。

您可以解码令牌并使用 [jwt.io 调试器](https://jwt.io/#debugger-io)检查其内容。将`jwtToken`有效负载的内容(JSON 数据元素的 value 部分中双引号之间的所有内容，但不包括双引号)粘贴到 Encoded textbox 中。您应该在解码部分看到令牌的内容，如下面的屏幕截图所示:

![](img/76dc9897afff39001542291c1055e730.png)

您可以通过粘贴项目根目录中的 *public.key* 文件的全部内容，在 VERIFY SIGNATURE 下的第一个文本框中粘贴*introduction-to-micro services*来验证令牌的签名。用户界面应自动更新以显示“签名已验证”。

如果您想使用 GitHub 上的配套资源库将您的*代码*更新到这一步，请在您想要创建项目目录的目录中执行以下命令:

```
git clone [https://github.com/maciejtreder/introduction-to-microservices.git](https://github.com/maciejtreder/introduction-to-microservices.git)
cd introduction-to-microservices
git checkout step6
cd eureka-helper
npm install
cd ../threats-v2
npm install
cd ../heroes-v2
npm install
cd ../auth
npm install
cd ..
```

注意，您仍然需要设置 MongoDB 数据库，并根据上面的说明向其中添加数据，包括每个数据操作命令，以使应用程序正常工作。如果替换正在运行的服务的和的代码，则需要停止并重新启动该服务。

# 用 JWTs 保护 API 端点

现在，您已经成功地利用 JSON web 令牌实现了授权服务，您可以使用它来保护应用程序的其他服务。有了`auth-service`，你将能够保护`heroes-service`和`threats-service`免受未经授权的访问，帮助超级英雄战胜黑暗势力。

从给`heroes service`添加授权开始。在可用的控制台窗口中，切换到 *heroes-v2* 目录，并安装所需的依赖项:

```
npm install jsonwebtoken
```

现在可以将新的依赖项和公钥导入到 */heroes-v2/heroes.js* 文件中。

在*heroes-v2*/*heroes . js*文件顶部的现有`const`声明的正下方添加以下 JavaScript 代码:

实现一个新的端点， */hero* ，它将侦听 POST 请求，请求的有效负载包含对新英雄的描述。

在 */hero/*** 端点的`app.post`方法上方的 *heroes-v2/heroes.js* 文件中添加以下 JavaScript 代码。请注意，以下代码实现了不同的端点，并且**而非**不会替换 */hero/id* ( `hero/**`)端点的代码:

修改更新现有英雄的 */hero/:id* 端点(`/hero/**`)，以使用 JWT 授权令牌。在 *heroes-v2/heroes.js* 文件中，在方法的开头添加以下代码:

*hero/:id* ( `/hero/**`)端点被`threats-service`用来给一个威胁分配一个英雄，所以你也需要修改 *threats-v2/threats.js* 文件。

找到下面所示的`app.post`方法的现有代码:

用下面一行替换`headers:`参数:

处理赋值是基于`heroes-service`的响应，所以不需要给`threats-service`增加 JWT 验证；通过`heroes-service`检查授权。如果授权失败，该方法的最后一行将返回一个错误。

# 测试修改后的端点

您可以通过添加新的 hero 来测试对端点的修改。

在项目根目录下创建一个 *new-hero-payload.json* 文件，并插入以下 json 代码:

```
{
   "type": "flying-dog",
   "displayName": "Rex",
   "powers": [1, 2, 3]
}
```

在运行 */threats-v2/threats.js* 和*/heroes-v2/heroes . js*node . js 应用程序的控制台窗口中，从项目根目录停止并重新启动应用程序。这些命令与您之前使用的命令相同，您可以在相同的控制台窗口中重新执行它们:

```
node threats-v2/threats.js 5000
node heroes-v2/heroes.js 5001
```

您的 JSON Web 令牌可能已经过期，因此可以通过在可用的控制台窗口中执行以下 curl 命令来获取一个新的令牌:

```
curl -i --request POST --header "Content-Type: application/json" --data "{\"userId\": \"admin2\", \"password\": \"6015ee55ebac72d737fcec8327e40445dbf6c4cb2dde092e00c72becfa8bb827bdfe09d0490ed58666f098eb925f5aa64cf2676ca39758f445356c39b6964e45\"}" localhost:8080/auth-service/auth
```

将结果复制并粘贴到文本编辑器中。您将在接下来的步骤中使用它。

将以下 curl 命令复制到您的文本编辑器中:

```
curl -i --request POST --header "auth:token-obtained-from-auth-service" --header "Content-type: application/json" --data @new-hero-payload.json localhost:8080/heroes-service/hero
```

用先前复制到文本编辑器的`jwtToken`元素中的数据替换`token-obtained-from-auth-service`。它是下面示例中“jwtToken”的值(您的实际数据会有所不同):

```
{"jwtToken":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NTgzNzA2MDcsInByaXZpbGVnZXMiOlsiQVNTSUdOX0hFUk8iXSwiaWF0IjoxNTU4MzY3MDA3fQ.fPvDrn8UZ1no5NGBEVvRgOV9LxoCSQtHsf-K609928haKDR76jNuUqidcbFwZ07uBxxDTOa4p9GYl-PSCcoS3aU98GmYOgNMS0jUyOkuvjXI4LV-x1QqJR-u-5ESVpk3L6gs1Iky6Uc4AjAmB2TnofQF5nVeXRx_woJ26mcWbCxLcnU7jf13RcHWFTVd2cS2JIhs-pqr482jViufZrT-sGorpbRWkuqB94xvQ6ctu3vfalQ21Djaq07FZQbY59GIbqSDUROSMHAF6FItkGM_J3zC7S6InIbJQ2tkhE9i5AesVoe4_8Z3x7Xqe-TkGWp_gqO4q58PSFjlirk0UPj_5A"}
```

生成的 curl 命令将如下所示(您的实际 JWT 数据会有所不同):

```
curl -i --request POST --header "auth:eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NTgzNzA2MDcsInByaXZpbGVnZXMiOlsiQVNTSUdOX0hFUk8iXSwiaWF0IjoxNTU4MzY3MDA3fQ.fPvDrn8UZ1no5NGBEVvRgOV9LxoCSQtHsf-K609928haKDR76jNuUqidcbFwZ07uBxxDTOa4p9GYl-PSCcoS3aU98GmYOgNMS0jUyOkuvjXI4LV-x1QqJR-u-5ESVpk3L6gs1Iky6Uc4AjAmB2TnofQF5nVeXRx_woJ26mcWbCxLcnU7jf13RcHWFTVd2cS2JIhs-pqr482jViufZrT-sGorpbRWkuqB94xvQ6ctu3vfalQ21Djaq07FZQbY59GIbqSDUROSMHAF6FItkGM_J3zC7S6InIbJQ2tkhE9i5AesVoe4_8Z3x7Xqe-TkGWp_gqO4q58PSFjlirk0UPj_5A" --header "Content-type: application/json" --data @new-hero-payload.json localhost:8080/heroes-service/hero
```

在项目根目录中执行这个 HTTP POST 请求，*微服务简介*(或者您已经找到的 *new-hero-payload.json* 文件)。您还可以用 [Postman](https://www.getpostman.com/) 或 PowerShell[Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-6)来执行这个请求，方法是使用示例 curl 命令中的数据在这些工具中构造一个适当的查询。

该命令的输出应该类似于:

```
HTTP/1.1 403 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"e-HWewxoOBFxgXa6ofj0mOIS8mvQc"
Date: Thu, 02 May 2019 14:14:31 GMT
Content-Type: text/html;charset=utf-8
Transfer-Encoding: chunkedAccess Denied.
```

您收到了 HTTP 403 响应，因为用户 admin2 没有创建新英雄的权限。您可以在运行 *heroes-v2/heroes.js* 的控制台窗口的`heroes-service`输出中验证这一点。输出应该如下所示:

```
Heroes v2: Adding new hero
{ type: 'flying-dog', displayName: 'Rex', powers: [ 1, 2, 3 ] }
Heroes v2: Token decoded, privileges:
[ 'ASSIGN_HERO' ]
Heroes v2: Requesting user does not have the CREATE_HERO privilege.
```

尝试通过向 */assignment* 端点发送 HTTP POST 请求，为 threat 分配一个英雄。如上所述，用之前获得的 JWT 令牌替换下面 curl 命令中的令牌占位符:

```
curl -i --request POST --header "auth:token-obtained-from-auth-service" --header "Content-Type: application/json" --data "{\"heroId\": 1, \"threatId\": 1}" localhost:8080/threats-service/assignment
```

该命令的输出应该类似于下面的代码片段:

```
HTTP/1.1 202 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"79-ER1WRPW1305+Eomgfjq/A/Cgkp8"
Date: Thu, 02 May 2019 14:23:38 GMT
Content-Type: application/json;charset=utf-8
Transfer-Encoding: chunked{"id":1,"displayName":"Pisa tower is about to collapse.","necessaryPowers":["flying"],"img":"tower.jpg","assignedHero":1}
```

# 验证 JSON Web 令牌验证

您可以验证授权服务是否保护应用程序免受格式错误的 jwt 的影响。为此，您可以更改 JWT 中包含授权数据的部分，将其替换为一个不同的值，赋予 admin1 用户更高的权限，然后重新提交请求。

执行以下步骤来测试对提升权限攻击的防护:

拷贝您一直使用的 JWT 的有效负载部分，该部分之前已存储到文本编辑器窗口中。在下面的示例中，有效负载是第一个点(`.`)之后和第二个点之前的字符串(您的实际数据会有所不同):

```
{"jwtToken":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NTgzNzA2MDcsInByaXZpbGVnZXMiOlsiQVNTSUdOX0hFUk8iXSwiaWF0IjoxNTU4MzY3MDA3fQ.fPvDrn8UZ1no5NGBEVvRgOV9LxoCSQtHsf-K609928haKDR76jNuUqidcbFwZ07uBxxDTOa4p9GYl-PSCcoS3aU98GmYOgNMS0jUyOkuvjXI4LV-x1QqJR-u-5ESVpk3L6gs1Iky6Uc4AjAmB2TnofQF5nVeXRx_woJ26mcWbCxLcnU7jf13RcHWFTVd2cS2JIhs-pqr482jViufZrT-sGorpbRWkuqB94xvQ6ctu3vfalQ21Djaq07FZQbY59GIbqSDUROSMHAF6FItkGM_J3zC7S6InIbJQ2tkhE9i5AesVoe4_8Z3x7Xqe-TkGWp_gqO4q58PSFjlirk0UPj_5A"}
```

用浏览器从 Google 页面导航到[编码/解码工具，将字符串粘贴到文本框中，选择 *Base64 解码*并点击**提交**。您应该会看到类似如下的文本:](https://toolbox.googleapps.com/apps/encode_decode/)

```
{"exp":1557865493,"privileges":["ASSIGN_HERO"],"iat":1557861893}
```

用解码后的字符串替换文本框中的编码字符串，用`"ASSIGN_HERO","CREATE_HERO"`替换`"ASSIGN_HERO"`。选择 *Base64 编码*并点击**提交**。

将结果复制到文本编辑器窗口。

如果您花了将近 10 分钟才到达这一步，您可能需要在继续之前生成一个新令牌。使用与上面相同的过程，将内容放在文本窗口中。如果你速度很快，你可以使用你已经存储在文本窗口中的令牌。

用从 Base64 编码器中检索到的文本替换有效令牌的有效负载部分，即第一个点(`.`)之后的部分。现在你有一个畸形的 JWT。

![](img/77b9d5fa437978b10c0c5190f8abcec8.png)

使用你新的畸形 JWT，尝试用 admin1 用户 ID 创建一个新的英雄，它没有创建用户的权限。在下面的 curl 命令中，用您的畸形 JWT 替换占位符:

```
curl -i --request POST --header "auth: malformed-token-goes-here" --header "Content-type: application/json" --data @new-hero-payload.json localhost:8080/heroes-service/hero
```

您应该会收到一个 HTTP [401 未授权](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401)响应，因为`auth-service`检测到一个格式错误的令牌。仔细看看`heroes-service`控制台输出:您应该看到应用程序检测到了格式错误的 JWT 并拒绝了它，如 *heroes-v2/heroes.js* 控制台窗口中输出的最后一行所示:

```
Heroes v2: Adding new hero
{ type: 'flying-dog', displayName: 'Rex', powers: [ 1, 2, 3 ] }
{ JsonWebTokenError: invalid signature
    at Object.module.exports [as verify] (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/jsonwebtoken/verify.js:75:17)
    at app.post (/Users/mtreder/introduction-to-microservices/heroes-v2/heroes.js:45:27)
    at Layer.handle [as handle_request] (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/layer.js:95:5)
    at next (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/route.js:137:13)
    at Route.dispatch (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/route.js:112:3)
    at Layer.handle [as handle_request] (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/layer.js:95:5)
    at /Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/index.js:281:22
    at Function.process_params (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/index.js:335:12)
    at next (/Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/express/lib/router/index.js:275:10)
    at /Users/mtreder/introduction-to-microservices/heroes-v2/node_modules/body-parser/lib/read.js:130:5 name: 'JsonWebTokenError', message: 'invalid token' }
```

因为 */auth* 服务检测到包含用户权限声明的 JWT 有效负载部分与签名不匹配，所以您的应用程序不会被坏人操纵。

如果您想使用 GitHub 上的配套资源库中的代码将您的*代码*捕获到这一步，您可以在您想要创建项目目录的目录中执行以下命令:

```
git clone [https://github.com/maciejtreder/introduction-to-microservices.git](https://github.com/maciejtreder/introduction-to-microservices.git)
cd introduction-to-microservices
git checkout step7
cd eureka-helper
npm install
cd ../threats-v2
npm install
cd ../heroes-v2
npm install
cd ../auth
npm install
cd ..
```

如果您没有按照创建和填充数据库的说明进行操作，那么您需要构建数据库并向其中添加数据，这样项目才能完全正常运行。如果你替换正在运行的服务的任何代码，你必须停止并重新启动它们。

# 实施双因素身份认证(2FA)

应用程序现在有一个安全的授权系统。但是它依赖于一个单一的认证因素，即用户拥有的用户 ID 和密码。这是用户*知道*的一个因素。众所周知，这些凭证很容易受到各种方式的破坏和滥用，那么如何使应用程序更加安全呢？

最好的方法之一是实现第二认证因子。

除了用户*知道*的东西，双因素认证还需要用户*拥有*(占有)或者*是*(继承)的东西。

你可以使用 [Twilio Authy](https://www.twilio.com/authy) 提供第二个因素，通过*拥有*，通过几乎每个用户*都有的东西*:手机(或其他设备)。如果你想了解更多关于用 Twilio Authy 实现 2FA 的信息，你可以参考本系列的前一篇文章:[用 Twilio Authy 在 Angular 中构建双因素认证](https://www.twilio.com/blog/two-factor-authentication-angular-twilio-authy)。

该项目的这一部分将向您展示如何使用 Authy 快速将 2FA 添加到 Angular 应用程序。您需要一个 Twilio 帐户来完成这些步骤。几分钟就可以注册一个[免费试用账号](https://www.twilio.com/try-twilio)。

拥有 Twilio 帐户后，登录并导航至 [Twilio 控制台](https://www.twilio.com/console)的 Authy 部分，然后完成以下步骤。(几分钟就好了。)

1.  在 Twilio 控制台的 Authy 部分，创建一个新的应用程序
2.  将应用程序的**生产 API 密钥**复制到安全的地方。(如果您放错了，可以在应用程序的设置中找到密钥。)
3.  在您创建的应用程序中，使用您首选的电子邮件地址和手机号码将自己注册为新用户。
4.  将您刚刚创建的用户的**授权 ID** 复制到一个安全的地方。
5.  在手机上安装 Authy 应用程序。您应该会收到一条文本通知，其中包含获取代码以完成安装的链接。

成功完成上述步骤后，您可以实施双因素身份认证。

将 Authy ID 添加到用户 admin1 的数据库记录中。

返回到您之前打开的 MongoDB CLI 控制台窗口，执行以下指令，用您刚刚获得的 Authy ID 替换`AUTH_ID`占位符:

如果更新操作成功，MongoDB 控制台应该返回以下结果:

```
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

需要更新`auth-service`代码以使用双因素身份验证因子。

用以下 JavaScript 代码替换 */auth/auth.js* 文件的内容:

分别用您的 MongoDB 连接字符串和 Twilio Authy API 键替换`dbUrl`和`API_KEY`常量的占位符。

新代码包括三个新功能:

`authenticateWithAuthy(authyId)`–使用数据库中给定的认证 ID 向 Twilio Authy API 发送认证请求，并返回一个`authyToken`。

`checkAuthyStatus(authyToken)`*–检查特定`authyToken`的身份验证请求的状态，如果请求成功通过身份验证，则返回 null 或与`authyToken`的值相关联的身份验证 ID。*

*`retrieveUserByAuthyId(authyId)`–使用授权 ID 从数据库中检索用户。*

*新代码还修改了 */auth* 端点。如果用户将标志 *enabledSecondFactor* 设置为 true，基于数据库中的值，端点将使用 Authy 令牌而不是 JSON Web 令牌进行响应。然后，Authy 令牌与新的端点 */auth/status* ，一起使用，如果用户在他们的二次元设备上使用 Authy 应用程序验证了身份验证请求，该端点将返回一个 JSON Web 令牌。*

*要运行修改后的`auth-service`代码，您需要停止并重启服务。您可以使用相同的控制台窗口来启动修改后的服务，使用您之前在应用程序根目录中使用的相同命令行指令，*微服务简介*:*

```
*node auth/auth.js 5004*
```

*下图说明了整个应用程序的实现:*

*![](img/0bf4dbb649ba7131659895d331c6d44c.png)*

# *测试双因素身份验证*

*您可以通过尝试认证`admin1`来验证 2FA 是否正常工作。*

*在控制台窗口中执行以下 curl 命令:*

```
*curl -i --request POST --header "Content-Type: application/json" --data "{\"userId\": \"admin1\", \"password\": \"fa786e94a24b297105a09f9a5673c4b2df9a11b6d9dfe229fde94124995b499f89cfbaa4974f55b0efeb6dfad6be6d9ecac591dd317e139a618e4217f728af78\"}" localhost:8080/auth-service/auth*
```

*输出应该类似于以下内容:*

```
*HTTP/1.1 200 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"35-fSJXazE8F/LKWVqPBvpYn/6l5DI"
Date: Thu, 02 May 2019 19:26:34 GMT
Content-Type: application/json;charset=utf-8
Transfer-Encoding: chunked{"authyToken":"1ec53af0-4f3e-0137-eed6-1212da1336e0"}*
```

*上面显示的成功请求返回了一个 Authy 令牌。如果您遇到错误，请检查`auth-service`的控制台输出，并检查您用于授权生产 API 密钥(`API_KEY`)和您用户的授权 ID 的值，这些值可在 [Twilio 授权控制台](https://www.twilio.com/console/authy)中找到。*

*通过执行以下 curl 命令，使用`authyToken`的值向 */auth/status* 端点发出请求，用执行上一个命令时返回的实际`authyToken`值替换`authyToken-goes-here`占位符的值:*

```
*curl -i --request GET --header "authytoken: authyToken-goes-here" localhost:8080/auth-service/auth/status*
```

*响应应该是 HTTP [401 未授权](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401)响应，如下所示:*

```
*HTTP/1.1 401 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"f-gnkbM9TfQtU2OeaokCx158AlciA"
Date: Fri, 10 May 2019 13:06:45 GMT
Content-Type: text/html;charset=utf-8
Transfer-Encoding: chunkedUnauthorized.*
```

*您还应该在安装了 Authy 应用程序的设备上收到 Authy 身份验证请求。*

*接受身份验证请求。*

*注意:根据您的设备及其通知设置，您可能不会收到通知消息；如果您没有收到设备通知，请检查应用程序。要检查身份验证请求，请打开应用程序，选择您在身份验证控制台中创建的身份验证应用程序的名称，选择钟形图标，然后选择 Pending 选项卡。任何未回答的身份验证请求都会出现在那里。*

*使用在之前的 API 请求中使用的相同的`authyToken`值，重复对*/auth-service/auth/status*的请求。这一次，API 应该返回一个 HTTP 200 OK 响应以及 JSON Web 令牌，如下所示:*

```
*HTTP/1.1 200 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"1f3-BIwlgrvtXgzgfN+ZvMJgccDH95g"
Date: Fri, 10 May 2019 13:07:27 GMT
Content-Type: application/json;charset=utf-8
Transfer-Encoding: chunked{"jwtToken":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NTc0OTcyNDcsInByaXZpbGVnZXMiOlsiQVNTSUdOX0hFUk8iLCJDUkVBVEVfSEVSTyJdLCJpYXQiOjE1NTc0OTM2NDd9.pMHL37_Vmk2kNsZ4pAf9WIoN1mVEFCYBezT5AVYuOWCBQpLjauUCfKEfd4nZtzJ5LqTdCOuxkvE5q6d2hYXuTmkv4EFFG78tgb36mqVJwfxZ1U9KdB6Kp4RN1HtjREXYzICv14h9pVWZ6NGl-n1zF1VZsxEnJU-MaeqyRvxYNbMk2k3KPEekjzhCyw8kRltmYDk6X_vzG3O6NmuHpN4xSQk5ZO7cuniDyc6Z9zlsanwbtJupi2pgcwGsMo9YE0uswrHCED5W9b-lp3hXBptDom1mgeButsleZv1vS6mFQoYUOrvIdzfcmrOpb4yCH0B6bejL_tJmz9PBt8K_7uNPYg"}*
```

*尝试通过执行以下 curl 命令添加一个新的英雄，用上面返回的`jwtToken`的值替换`token-obtained-from-auth-service`占位符:*

```
*curl -i --request POST --header "auth: token-obtained-from-auth-service" --header "Content-type: application/json" --data @new-hero-payload.json localhost:8080/heroes-service/hero*
```

*API 应该返回一个 HTTP 201 创建的响应，如下所示:*

```
*HTTP/1.1 201 
X-Application-Context: application:8080
X-Powered-By: Express
ETag: W/"6f-0nI8erL/vblCtFwnl+JDjQeOQKM"
Date: Fri, 10 May 2019 13:09:37 GMT
Content-Type: application/json;charset=utf-8
Transfer-Encoding: chunked{"id":6,"type":"flying-dog","displayName":"Rex","powers":[1,2,3],"busy":false,"_id":"5cd5781159491ad121e0a1c4"}*
```

*该响应包括 hero 6 的新分配，表明您成功使用 Authy 对用户进行了身份验证，使用用户的 Authy 身份验证令牌获得了包含用户安全声明的 JWT，并使用 JWT 访问微服务 API 对持久层进行了更改。*

*对于一个教程来说，这是一个很好的工作。祝贺您成功完成项目。*

*如果您想进行更多的试验，可以通过在 MongoDB CLI 中执行以下命令来恢复您在测试双因素身份验证时所做的数据库更改:*

*如果您想要将您的*代码*赶上这一步，您可以通过在您想要创建项目目录的目录中执行以下命令，从 GitHub 存储库中克隆代码:*

```
*git clone [https://github.com/maciejtreder/introduction-to-microservices.git](https://github.com/maciejtreder/introduction-to-microservices.git)
cd introduction-to-microservices
git checkout step8
cd eureka-helper
npm install
cd ../threats-v2
npm install
cd ../heroes-v2
npm install
cd ../auth
npm install
cd ..*
```

*请注意，您还需要根据上面的说明来设置数据库和 Twilio Authy，以便项目能够完全正常运行。*

# *摘要*

*在这篇文章中，您学习了如何使用授权服务来保护 Node.js 微服务，以控制哪些*已验证的*和*已授权的*用户可以访问特定的资源。您了解了如何使用 JSON Web 令牌减少授权服务的流量，JSON Web 令牌是一种以易于解码和阅读的编码字符串形式发送信息的标准方式，但受到加密签名的保护。公钥所有者可以验证签名，但只有私钥所有者可以更改令牌值并再次签名。当令牌有效负载与最初从有效负载生成的签名不匹配时，令牌无效。*

*您还了解了如何使用 Twilio Authy 实现双因素身份验证。您已经看到了如何将 Authy 和 jwt 结合起来，形成一个健壮的系统来验证和授权您的 JavaScript APIs。*

# *额外资源*

*[架构风格和基于网络的软件架构的设计](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)，罗伊·托马斯·菲尔丁，2000——菲尔丁的博士论文描述了表述性状态转移(第五章)和其他架构风格。*

*[微服务](https://en.wikipedia.org/wiki/Microservices) —尽管有缺陷，维基百科的文章是一个很好的起点，可以找到更多关于微服务架构和实现的信息。*

*[Node.js](https://nodejs.org/en/docs/) —为应用服务器提供参考文档。*

*[RFC 7519](https://tools.ietf.org/html/rfc7519) — JSON Web Token 文档。*

**我是 Maciej Treder，请通过*[*【contact@maciejtreder.com】*](mailto:contact@maciejtreder.com)*[*https://www.maciejtreder.com*](https://www.maciejtreder.com/)*或@ maciejtreder on*[*GitHub*](http://github.com/maciejtreder)*[*Twitter*](https://twitter.com/maciejtreder)*和* [*LinkedIn*](https://www.linkedin.com/in/maciej-treder/)***

***本帖原载于* [*Twilio 博客*](https://www.twilio.com/blog/protecting-javascript-microservices-node-js-json-web-tokens-twilio-authy) *。***