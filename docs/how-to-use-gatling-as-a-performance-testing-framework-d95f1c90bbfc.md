# 如何使用 Gatling 作为性能测试框架

> 原文：<https://levelup.gitconnected.com/how-to-use-gatling-as-a-performance-testing-framework-d95f1c90bbfc>

## 安装和使用加特林的初学者指南

![](img/de43beecaccfb7f38ba2ade472715ae1.png)

Kolleen Gladden 在 [Unsplash](https://unsplash.com/s/photos/performance?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Gatling 是一个基于 Scala 的高性能负载测试工具。它使用 Akka 演员来模拟巨大的负载。默认情况下，它支持以下功能:

本质上，Gatling 提供了对 HTTP 协议的强大支持，这使得它成为负载测试任何 HTTP 服务器的首选工具。它还支持 WebSockets、JMS 等。社区插件也支持其他协议。

加特林网站上的[入门文档相当不错。它包括下载一个 zip 文件，然后运行 BAT 或 SH 脚本来启动 Gatling。然后，您可以从列表中选择要运行的测试。如果您想将 Gatling 测试作为持续集成的一部分来运行，那么使用像 Gradle 或 Maven 这样的构建工具来做同样的设置会非常有帮助。](https://gatling.io/docs/current/)

在本文中，我将带您一步步地使用 Gradle 设置 Gatling，并运行一些测试。

# 使用 Gradle 设置加特林

加特林的 GitHub 组织有一个我们可以使用的 Gradle 插件。

## 克隆回购

```
git clone https://github.com/gatling/gatling-gradle-plugin-demo.gitCloning into 'gatling-gradle-plugin-demo'...
remote: Enumerating objects: 83, done.
remote: Counting objects: 100% (83/83), done.
remote: Compressing objects: 100% (54/54), done.
remote: Total 83 (delta 27), reused 61 (delta 14), pack-reused 0
Unpacking objects: 100% (83/83), done.
```

## 在某个 IDE 中打开 repo

一旦设置完成，我们需要在一些 IDE 中打开代码。我已经为此使用了 IntelliJ。

转到文件→打开→ <select directory="" where="" you="" cloned="" the="" plugin="">下面是 build.gradle 的样子plugins { // The following line allows to load io.gatling.gradle plugin and directly apply it id 'io.gatling.gradle' version '3.5.1'}gatling { // WARNING: options below only work when logback config file isn't provided logLevel = 'WARN' // logback root level logHttp = 'NONE' // set to 'ALL' for all HTTP traffic in TRACE, 'FAILURES' for failed HTTP traffic in DEBUG}开发模拟类要创建一个测试，我们需要做的第一件事是创建一个模拟类。在上面的例子中，回购已经附带了一个默认类—基本模拟。但是这里我们将从头开始编写一个新的模拟类创建一个新的 Scala 类右键单击 scala 目录并创建一个新的 scala 类。让我们将其命名为 PerfTestSample。这个 Scala 类应该扩展 Simulation 类。为模拟类添加导入语句import io.gatling.core.scenario.Simulation新的 Scala 模拟类。作者图片添加所有导入这可以在编写代码时完成，当错误出现时，但我只是提前分享它。这些是您将需要的导入:import io.gatling.core.Predef._import io.gatling.core.scenario.Simulationimport io.gatling.http.Predef._编写测试模拟类典型的模拟课程由三个主要部分组成HTTP 协议配置场景定义模拟定义我们将在下面逐一探讨。我们要做的第一件事是配置 HTTP 协议。下面是一个看起来像这样的例子:val httpConf = http.baseUrl("http://localhost:8082/druid/") .header("Content-Type", "application/json") .proxy(Proxy("localhost", 8082))此基本 URL 将被添加到不以http.开头的所有 URL 的前面默认情况下，Gatling 试图以尽可能真实的方式模拟 web 浏览器。您可以使用各种配置选项(应该添加在baseUrl()语句之后)来改变这种行为:.maxConnectionsPerHost(4):在同一主机上获取资源时，要更改每个虚拟用户的并发连接数(默认为 6)，.disableAutoReferer:禁用自动引用器 HTTP 头计算，.disableCaching:禁用响应缓存(加特林使用 Expires 、 Cache-Control 、 Last-Modified 和 ETag 头缓存响应)更多的配置可以在协议层进行管理: Gatling 文档。接下来，我们将研究如何定义一个场景。这是一个样品val scn = scenario("ScenarioName") .exec(http("Submit Query") .post("v2/sql").body(RawFileBody("<FileName>")))在这个例子中，我向 http://localhost:8082/druid/v2/SQL 提交一个 post 请求，并在一个文件中传递查询。正如您所看到的，前面部分中的基本 URL 是前置的。还有一个可选的暂停方法来模拟用户在连续请求之间的思考时间。接下来是模拟定义。这告诉加特林你想如何模拟你的测试。下面是一个样本的样子:setUp( scn.inject(atOnceUsers(10))).protocols(httpConf)这意味着有 20 个用户同时提交 post 请求。这是一个开放请求的例子，它无法控制并发用户的数量。开放式模型的其他一些例子有:rampUsers(10) during(60 seconds): to inject 10 users over 60 seconds,constantUsersPerSec(5) during(60 seconds): to inject 5 users every second during a total of 60sec封闭模型是预先定义并发用户数量的模型。一些例子是:constantConcurrentUsers(10) during(60 seconds): to inject 10 concurrent users during 60 seconds,rampConcurrentUsers(5) to(15) during(30 seconds) : to inject 5 concurrent users at the start of the test and up to 15 concurrent users after 30 seconds.运行模拟一旦我们定义了这三个部分，就该运行模拟了./gradlew gatlingRun-PerfTestSample输出通常是一个 HTML 文件Please open the following file: /Users/cinto/Desktop/gatling-gradle-plugin-demo/build/reports/gatling/perftestsample-20210208165203819/index.html查看输出您可以使用简单的 open 命令打开文件:open /Users/cinto/Desktop/gatling-gradle-plugin-demo/build/reports/gatling/perftestsample-20210208165203819/index.html下面是一个样本文件的样子加特林性能测试截图。作者图片HTML 页面上有更多的细节，如每秒请求数响应时间分布每秒响应等我希望你现在已经知道什么是加特林，以及如何建立一个基本的加特林测试模拟。我在上面分享了一个例子，说明了我如何使用它来测试我的 Apache Druid 安装。</select>