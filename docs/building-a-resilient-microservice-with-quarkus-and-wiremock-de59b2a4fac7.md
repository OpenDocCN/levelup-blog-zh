# 利用 Quarkus 和 Wiremock 构建弹性微服务

> 原文：<https://levelup.gitconnected.com/building-a-resilient-microservice-with-quarkus-and-wiremock-de59b2a4fac7>

![](img/739bad1a04e16b9fc397db685cd1c018.png)

由[马扬·格拉博斯基](https://unsplash.com/@knaggit?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/mobile-phone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

这是用 Quarkus、Kotlin 和 Debezium 从头开始构建微服务系列的第三部分。该服务用于发送短信。

在第一部分中，我们构建了基本框架并添加了持久性。在[第二部分](https://medium.com/@changeant/implementing-the-transactional-outbox-pattern-with-debezium-in-quarkus-f2680306951)中，我们使用 CDC 从持久化的 SMS 消息中生成事件。我们构建了一个消息处理程序来处理发送到 Kafka 主题的消息。

下面是消息处理程序的当前状态

在第二部分的末尾，消息处理程序只是将消息状态更新为 Delivered(第 11 行),这样我们就可以证明这个流了。为了完成这个流程，我们需要添加一个或多个 SMS 提供者，以便可以将消息传递给接收者。

如果有一个以上的提供者就好了，这样如果一个提供者不可用或者发生一些不可预见的事件，比如信用过期，我们可以有一个备份。我们需要挑选几个提供者，并编写客户端集成来与提供者 API 对话。然后，我们将编写一个路由器，随机选择一个提供者来发送消息。

该流程将如下所示

![](img/968c27d7e9d104de89f6db432cedde9c.png)

为了跟随代码，你可以拉动分支

```
git clone [git@github.com](mailto:git@github.com):iainporter/sms-service.git
git checkout part_three
```

首先要做的是挑选几个提供商，并添加客户端集成。在本例中，我们将使用[点击发送](https://www.clicksend.com/gb/)和[两种方式。](http://twilio.com)

首先，我们需要一个面向所有提供商的公共接口

注册 ClickSend 后，我们会收到一组凭据和一定数量的免费信用点数，这将允许我们进行测试。

点击发送查看开发者文档这里是一个发送短信到点击发送的例子

```
curl --include \
     --header "Authorization: Basic ZXJuYW1lOmFwaS1wYXNzd29yZA=="  \
     --request POST \
     --header "Content-Type: application/json" \
     --data-binary "    {
        \"messages\":[
            {
                \"source\":\"php\",
                \"body\":\"Jelly liquorice marshmallow candy \",
                \"to\":\"+61411111111\"
            }
        ]
    }" \
'https://rest.clicksend.com/v3/sms/send'
```

向 ClickSend API 发送消息的客户端实现

分解代码:

**第 6–13 行** - >设置我们在注册点击发送时获得的属性，这些属性在应用程序中。属性
**第 17 行** - >使用 [kohttp](https://github.com/rybalkinsd/kohttp) 客户端进行 REST 通信
**第 36–38 行** - >捕捉超时异常并将消息设置为失败
**第 44 行** - >点击发送返回所有响应，即使失败也作为 200 Http 我们需要从响应中提取 status 属性，并检查它是否等于“成功”(第 82–91 行)

## 科特林密封类

在处理客户端对 ClickSend API 的调用时，人们倾向于使用类似 java 的异常处理。然而，Kotlin 不支持使用检查异常，这是有充分理由的。通过使用异常，我们迫使任何使用该类的人理解可能会抛出什么异常，并适当地处理它们。

也就是说，这是第 33-61 行的外观，但有例外

我们可以通过使用一个密封的类来消除所有这些混乱的异常处理

现在，我们可以通过返回 ProviderResponse 并允许调用者决定如何处理它来使实现的意图更加清晰。这是 Twilio 的实现。

## 使用 Wiremock 测试客户端集成

在我们的单元和集成测试中，我们不想向真正的 SMS 提供者发送消息。我们需要用 Wiremock 把这些 REST 调用剔除掉。Wiremock 是一个模仿和阻止对外部服务的 http 调用的重要工具。它是一个 HTTP 模拟服务器，我们可以在其中设置期望值，然后验证结果。通过设置与来自外部 API 的预期响应相匹配的数据，我们可以编写测试来验证我们的代码行为是否如预期的那样工作。

这里有一个测试，用于验证点击发送集成在以下场景中的行为:

*   Http 200 成功，消息的状态为“成功”
*   Http 200 成功，但消息的状态不是“成功”
*   Http 401 授权失败
*   复制读取超时故障

## 路由到短信提供商

现在我们已经编写了客户端集成，我们需要添加一些路由逻辑。这是提供商路由器的接口。

实现细节并不重要。我们可以选择许多不同的方式来实现这一点。保持简单，路由器随机选择一个提供商。如果出现服务器故障，我们将使用不同的提供商再试一次。

注意第 9 行，如果没有配置提供者，那么我们关闭服务器，因为如果没有地方可以发送 SMS 消息，那么继续是没有意义的。

## 组件测试

在第二部分中，我们向组件测试添加了额外的 docker 容器，以允许我们验证消息传递是否按预期工作。为了在我们的组件测试中测试 SMS 客户端集成，我们需要添加一个 wiremock 容器，我们可以用我们需要的存根填充它。借助 Dockerfile 的魔力，这很容易实现

然后，我们可以将其连接到 sms 服务中的构建执行中

然后将其添加到在 BaseComponentTst.kt 类中启动的容器列表中

注意我们用 config/wiremock 目录中的存根填充 mappings 目录

![](img/81f5d2c38882b5babe56e73588613f75.png)

现在，我们可以验证当一个或多个提供者返回成功和不成功状态时的行为

## 运行服务

在运行这项服务之前，你需要通过[点击发送](https://www.clicksend.com/gb/)和/或 [Twilio](http://twilio.com) 注册一个账户。这两种服务都有免费试用，可以给你足够的积分来发送大量的测试信息。

获得 SMS 提供程序的凭据后，添加 config/application.properties 文件并添加您的属性。

application.properties.sample 提供了一个示例

```
## Sign up for ClickSend (www.clicksend.com) and get username and apikey
sms.provider.clicksend.enabled=true
sms.provider.clicksend.username=
sms.provider.clicksend.apiKey=
sms.provider.clicksend.endpoint=https://rest.clicksend.com/v3/sms/send

## Sign up for Twilio (www.twilio.com) and get the values to access the API
sms.provider.twilio.enabled=true
sms.provider.twilio.account.sid=
sms.provider.twilio.endpoint=https://api.twilio.com/2010-04-01/Accounts/<your account.sid>/Messages.json
sms.provider.twilio.auth.token=
sms.provider.twilio.from.number=
```

确保 docker-compose.yml 文件中引用了您添加的文件。在本例中，我将属性文件放在相对于 docker-compose.yml 文件的父目录中:../config/应用程序.属性

```
volumes:
  - ../config/application.properties:/deployments/config/application.properties
```

构建服务，然后用 docker-compose 运行它

```
mvn clean install
cd sms-service
docker-compose up -d
```

向 kafka connect 注册连接器

```
curl 'localhost:8083/connectors/' -i -X POST -H "Accept:application/json" \
-H "Content-Type:application/json" \
-d '{"name": "sms-connector", "config": {"connector.class": "io.debezium.connector.postgresql.PostgresConnector", "database.hostname": "postgres-db", "database.port": "5432", "database.user": "postgres", "database.password": "postgres", "database.dbname" : "sms", "database.server.name": "smsdb1", "table.whitelist": "public.outboxevent", "transforms" : "outbox","transforms.outbox.type" : "io.debezium.transforms.outbox.EventRouter", "transforms.OutboxEventRouter.event.key": "aggregate_id", "transforms.outbox.table.fields.additional.placement": "type:header:eventType"}}'
```

现在，您可以开始发送消息了。这是一个样本卷曲

```
[http://localhost:8080/v1/sms'](http://localhost:8080/v1/sms') -i -X POST    -H 'Content-Type: application/json'     -d '{"text":"Foo Bar!", "toNumber": "+1234567890", "fromNumber="+1234567890"}'
```

期待这样的回应

```
HTTP/1.1 202 Accepted
Content-Length: 0
Location: [http://localhost:8080/v1/sms/e307458a-a0a8-4f13-9635-f9b27b4da0e5](http://localhost:8080/v1/sms/e307458a-a0a8-4f13-9635-f9b27b4da0e5)
```

我们可以使用位置头来获取消息细节

```
curl -i [http://localhost:8080/v1/sms/](http://localhost:8080/v1/sms/c75e551a-b11c-4703-bbe7-5c6bb38b001c)e307458a-a0a8-4f13-9635-f9b27b4da0e5
```

如果消息成功传递，结果应该如下所示

```
HTTP/1.1 200 OK
Content-Length: 194
Content-Type: application/json{
"createdAt":"2020-07-29T15:10:49.36597Z",
"id":"e307458a-a0a8-4f13-9635-f9b27b4da0e5",
"status":"DELIVERED",
"text":"Foo Bar!",
"toNumber":"+1234567890",
"updatedAt":"2020-07-29T15:10:57.558555Z"
}
```

如果其中一个提供者返回一个错误，那么我们应该在日志中看到对另一个提供者的重试

```
2020-07-29 15:26:21,183 DEBUG [com.por.sms.pro.cli.ClickSendProvider] (ForkJoinPool.commonPool-worker-3) Sending SMS via ClickSend Service to +1234567890
2020-07-29 15:26:21,199 DEBUG [com.por.sms.pro.cli.ClickSendProvider] (ForkJoinPool.commonPool-worker-3) serialised messages to json: {"messages":[{"body":"Foo Bar!","to":"+1234567890","schedule":0}]}
2020-07-29 15:26:26,624 DEBUG [com.por.sms.pro.cli.ClickSendProvider] (ForkJoinPool.commonPool-worker-3) API response from ClickSend, statusCode: 200
2020-07-29 15:26:26,627 DEBUG [com.por.sms.pro.cli.ClickSendProvider] (ForkJoinPool.commonPool-worker-3) ClickSend failed to send the message due to: INSUFFICIENT_CREDIT
2020-07-29 15:26:26,629 DEBUG [com.por.sms.pro.RandomProviderRouter] (ForkJoinPool.commonPool-worker-3) retrying message with different provider Twilio
2020-07-29 15:26:26,629 DEBUG [com.por.sms.pro.twi.TwilioProvider] (ForkJoinPool.commonPool-worker-3) Sending SMS via Twilio Service to +1234567890
2020-07-29 15:26:27,431 DEBUG [com.por.sms.pro.twi.TwilioProvider] (ForkJoinPool.commonPool-worker-3) API response from Twilio, statusCode: 201
2020-07-29 15:26:27,433 DEBUG [com.por.sms.eve.SmsMessageCreatedHandler] (ForkJoinPool.commonPool-worker-3) Message has been processed
```

我们现在有一个全功能的微服务在 Quarkus 中运行，它将接受 SMS 请求并将其转发给一个已配置的提供商。除了一个明显的漏洞:安全性之外，它几乎可以投入生产了！！

目前这项服务对所有人开放。任何人都可以通过 API 发送消息。通常，像这样的服务将由其他微服务在内部使用，不会暴露在 API 网关之外。但是，我们应该保护端点，并确保只有经过授权的用户或客户端才能访问。下一篇文章将讨论如何使用 OAuth 保护端点。

这篇文章的代码库可以在[这里](https://github.com/iainporter/sms-service/tree/part_three)找到

该系列的其他部分包括:

*   [第一部分:构建框架并添加持久性](/building-a-microservice-from-the-ground-up-with-quarkus-kotlin-and-debezium-83ae5c8a8bbc)
*   [第二部分:使用 Kafka Connect 和 Debezium 实施 CDC](/implementing-the-transactional-outbox-pattern-with-debezium-in-quarkus-f2680306951)
*   [第四部分:使用 OpenID Connect 保护服务](/securing-a-microservice-in-quarkus-with-openid-connect-505204d1c9a9)
*   [第五部分:使用 GraalVM 本地运行](https://medium.com/@changeant/running-a-microservice-in-quarkus-on-graalvm-52d6b42a5840)
*   [第六部分:用 Jib 封装你的微服务](https://medium.com/@changeant/containerizing-your-microservice-in-quarkus-with-jib-fae0f62bd57e)
*   [第七部分:使用 CircleCI 为微服务构建 CI 管道](/building-a-ci-pipeline-for-a-microservice-in-quarkus-with-circleci-11e9b679423f)