# 微软 RESTLer 的 REST API Fuzzing

> 原文：<https://levelup.gitconnected.com/rest-api-fuzzing-with-microsoft-restler-7184053e2205>

![](img/e4db31ad5ea62c222a6745d449cc6021.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [James Wainscoat](https://unsplash.com/@tumbao1949?utm_source=medium&utm_medium=referral) 拍摄

F resh 来自**微软研究院**,**RESTLer****fuzzer**是一个新的 REST fuzzing 工具，它依靠一个**open API**/**Swagger**规范来创建并执行对所选 API 的测试。

模糊化允许我们通过用随机输入测试我们的应用程序来发现错误。它帮助开发者提高程序的**健壮性**、**安全性**和**整体质量**。由于它基本上是自动化的，它让开发者在运行的时候可以自由地编写代码。RESTLer 给了我们所有这些好处。

有两个关键点表明了这个工具的好处:首先，它可以推断请求*的依赖关系，也就是说，它可以推断一个请求的资源作为另一个请求的输入参数是必要的；其次，它有一个 ***动态反馈*** *，*意为工具可以分析以前的结果来预测无效的请求流，即。:做请求 X 和 Y，会引起请求 z 的拒绝。*

*我们将介绍该工具的设置和执行，但第一步是获得一个应用程序来运行我们的测试，您可以使用您选择的应用程序，但在本教程中，我们将使用[**Swagger pet store**](https://github.com/swagger-api/swagger-petstore)，这是由 Swagger 团队提供的一个示例，用于演示 it 功能。*

*首先，如果您还没有设置 Java 环境，那么您需要设置 Java 环境。你需要一个 **Java SDK** ，如果你使用的是 Windows，你可以简单的使用[**Oracle Java SDK**](https://www.oracle.com/java/technologies/javase/jdk8-readme.html)。本教程将提供在 Windows 上运行的命令，因为 RESTLer 目前提供了更好的支持。*

*您还需要 [**Maven**](https://maven.apache.org/download.cgi) 来安装项目依赖项并运行服务器。记得设置好你的 **JAVA_HOME** 和 **maven** 环境变量，你可以用[](https://mkyong.com/maven/how-to-install-maven-in-windows/)****条来走完这个过程。*****

*****现在克隆上面提供的 **Swagger Petstore** GitHub 存储库，打开您的终端， ***cd*** 到存储库文件夹，并运行它:*****

```
*******mvn package jetty:run*******
```

*****下载所有的依赖项需要一些时间，但是之后你就有了一个可以访问的工作服务器。*****

*****现在我们将设置 **RESTLer** 本身。这要看 [**Python**](https://www.python.org/downloads/) 了，一定要有版本 **3.8.2** 以上，还有 [**。网芯 SDK**](https://dotnet.microsoft.com/download/dotnet-core?utm_source=getdotnetcorecli&utm_medium=referral) 版本 **3.1** 。*****

*****克隆[**RESTLer Fuzzer**](https://github.com/microsoft/restler-fuzzer)GitHub 库。我们需要编译 RESTLer，因为二进制 drops 还不可用。在**终端**在库文件夹**中打开的情况下，运行**:*****

```
*******mkdir restler_bin****python ./build-restler.py --dest_dir** <full path to restler_bin>*****
```

*****务必始终给**完整路径**，否则 RESTLer 可能无法正常工作。RESTLer 现在编译在***RESTLer _ bin****文件夹下。******

******接下来，是 API 的语法的编译，我们需要 **Swagger 规范**。在 Swagger Petstore webserver 运行的情况下，在浏览器上转到[**localhost:8080/API/v3/open API . JSON**](http://localhost:8080/api/v3/openapi.json)并下载文件。由于本地 web 服务器的目标端口是 8080，我们需要更新 JSON 中的“servers”属性。******

```
******{
"servers": [{"url":"localhost:8080/api/v3"}]
}******
```

******在***restler _ bin/restler****文件夹内**运行**:*******

```
*********./restler compile --api_spec** <full path to openapi.json>*******
```

*******它将使用我们的**语法**创建一个 ***编译*** 目录，下一步将使用 ***grammar.py*** ， ***dict.json*** ，***engine _ settings . JSON***文件。我们现在可以**测试**如果语法有效，测试命令将在所有端点上执行快速测试，并显示测试的覆盖范围。**运行**中的 *restler_bin/restler:* 中的***********

```
****.\restler test --grammar_file** <full path to grammar.py> **--dictionary_fil**e <full path to dict.json> **--settings** <full path to engine_settings.json> **--no_ssl****
```

****结果我们得到了一个**请求覆盖**，这表明了在测试运行中有效请求的**数量，如果你想在你的应用中改进它，在 [GitHub](https://github.com/microsoft/restler-fuzzer/blob/6b906d03ac13e12936d6ec6be21e8f5e00d77b8e/docs/user-guide/ImprovingCoverage.md) 上有一个用户指南。******

```
**Request coverage (successful / total): 9 / 19**
```

****说到精彩的部分，我们已经准备好模糊我们的应用程序。RESTLer 上有两个设置可供您使用。正如 GitHub 上的团队所描述的:****

******Fuzz-lean** :在编译好的 RESTler 语法中，每个 endpoint+方法执行一次，使用一组默认的检查器，看看是否可以快速找到 bug。****

******Fuzz**:bug hunting——在智能广度优先搜索模式(深度搜索模式)下探索 RESTler fuzzing 语法，以找到更多 bug。**警告**:这种类型的模糊更具攻击性，如果服务实现不佳，可能会导致测试中的服务中断(例如，模糊可能会导致资源泄漏、性能下降、后端损坏等)。).****

****因为我们是在本地运行一个样本代码，这个警告不会影响我们，但是**请小心**，如果你的目标是一个不属于你的部署应用，请不要运行 RESTLer。****

******模糊-倾斜**和**模糊**模式的命令分别为:****

```
****.\restler fuzz-lean --grammar_file** <full path to grammar.py> **--dictionary_file** <full path to dict.json> **--settings** <full path to engine_settings.json> **--no_ssl****.\restler fuzz --grammar_file** <full path to grammar.py> **--dictionary_file** <full path to dict.json> **--settings** <full path to engine_settings.json> **--no_ssl --time_budget** <time in hours to run>**
```

****当运行结束时，RESTLer 将打印**覆盖结果**，以及错误的总数。所有**被调试的请求序列**将被放置在: ***FuzzLean*** 或 ***Fuzz*** 文件夹中。我的 1 小时模糊运行给出了这个结果:****

```
**PayloadBodyChecker_500: 33main_driver_500: 10**
```

****那么，上面的***PayloadBodyChecker***是什么呢？**雷斯特勒的检查员完全按照他们说的去做。这些**与正常的模糊化一起运行，可以检查是否有更多的 bug**，每个都以不同的逻辑运行。在我们的例子中，它检测到 33 个 bug**直接模糊了我们请求的 **JSON 主体**。其他 **10 个 bug**是在正常的 fuzzing 运行中发现的，每次有 **500** **响应**就会被当作一个 bug 处理。********

**现在让我们来看看我们的 **bug buckets** ，在***Fuzz/restler results/experiment<…>/bug _ buckets***里面是所有被 bug 的请求以及它们各自的响应的集合。看看***main _ driver _ 500 _ 1 . txt***文件:**

```
****...**-> POST /api/v3/user HTTP/1.1\r\nAccept: application/json\r\nHost: localhost:8080\r\nContent-Type: application/json\r\n\r\n{\n    "id":0,\n    "username":"fuzzstring",\n    "firstName":"fuzzstring",\n    "lastName":"fuzzstring",\n    "email":"fuzzstring",\n    "password":"fuzzstring",\n    "phone":"fuzzstring",\n    "userStatus":0}\r\n! producer_timing_delay 0! max_async_wait_time 20PREVIOUS RESPONSE: 'HTTP/1.1 500 Internal Server Error\r\nDate: Sun, 22 Nov 2020 19:36:09 GMT\r\nAccess-Control-Allow-Origin: *\r\nAccess-Control-Allow-Methods: GET, POST, DELETE, PUT\r\nAccess-Control-Allow-Headers: Content-Type, api_key, Authorization\r\nAccess-Control-Expose-Headers: Content-Disposition\r\nContent-Type: application/json\r\nContent-Length: 110\r\nServer: Jetty(9.4.9.v20180320)\r\n\r\n{"code":500,"message":"There was an error processing your request. It has been logged (ID: 9b4a940aedfc92d9)"}'**
```

**正如您所看到的，上面的请求返回了一个**内部服务器错误**，RESTLer 甚至为您提供了使用**—replay _ log<path _ to _ the _ log>**命令重放请求的选项。调试错误非常有用。**

**正如你所看到的，在设置了 **RESTLer** 本身之后，很容易出错，给我们**自动化的错误报告**，这意味着我们的 REST 服务质量**更好**，可靠性**更高**和安全性**，并且**参与的工作很少**。****