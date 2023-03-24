# 使用 OpenID Connect 保护 Quarkus 中的微服务

> 原文：<https://levelup.gitconnected.com/securing-a-microservice-in-quarkus-with-openid-connect-505204d1c9a9>

![](img/6fb49c66d79742ed96c472ea435de593.png)

杰森·登特在 [Unsplash](https://unsplash.com/s/photos/safe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

这是用 Quarkus、Kotlin 和 Debezium 从头开始构建微服务系列的第四部分。该服务用于发送短信。

在第一部分中，我们构建了基本框架并添加了持久性。在[第二部分](https://medium.com/@changeant/implementing-the-transactional-outbox-pattern-with-debezium-in-quarkus-f2680306951)中，我们使用 CDC 从持久化的 SMS 消息中生成事件。我们构建了一个消息处理程序来处理发送到 Kafka 主题的消息。在[第三部分](/building-a-resilient-microservice-with-quarkus-and-wiremock-de59b2a4fac7)中，我们添加了几个 SMS 提供者和一个路由器，它根据简单的随机算法将消息发送给第三方提供者。测试使用 wiremock 来剔除来自提供者的响应。

服务的当前状态使端点对任何调用方完全开放。我们需要增加安全性，只允许授权用户或客户访问服务。将授权委托给第三方(如 Okta、Google、Auth0)或使用现成的解决方案(如 Keycloak 或 Gluu)越来越常见。我们将使用 OpenID Connect 来获取一个访问令牌，并确保只接受来自我们列入白名单的提供商的令牌。为了测试，我们将使用 Keycloak，为了运行真正的服务，我们将使用 Okta。

## OpenID 连接(OIDC)

O penID 是建立在 OAuth 2.0 之上的身份层。它允许用户向提供商进行身份验证，并以 JWT 的形式获得访问令牌。该协议允许客户端通过 ID 令牌和/或 userinfo 端点获取有关用户的信息。jwt 包含嵌入在访问令牌中的标准[声明](https://tools.ietf.org/html/rfc7519#page-9)，如 **sub** 和 **iss** ，它们标识了谁发布了令牌以及谁是令牌的持有者。

兼容的 OIDC 提供者必须提供一个[配置](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)发现端点，允许应用程序发现所有必要的端点和公钥位置信息。

以下代码片段显示了响应中可能出现的一些属性:

```
"issuer": "https://server.example.com",
"authorization_endpoint":
     "https://server.example.com/connect/authorize",
"token_endpoint":
     "https://server.example.com/connect/token",
"token_endpoint_auth_methods_supported":
     ["client_secret_basic", "private_key_jwt"],
"token_endpoint_auth_signing_alg_values_supported":
     ["RS256", "ES256"],
"userinfo_endpoint":
     "https://server.example.com/connect/userinfo",
"check_session_iframe":
     "https://server.example.com/connect/check_session",
"end_session_endpoint":
     "https://server.example.com/connect/end_session",
"jwks_uri":
     "https://server.example.com/jwks.json",
"registration_endpoint":
     "https://server.example.com/connect/register"
```

## 签署和验证 jwt

TOIDC 提供者负责使用非对称签名算法对 JWT 进行签名。私钥由提供商持有，公钥提供给客户，以便他们可以验证 JWT 的签名。

JWT 的报头部分包含查找公钥并对其进行验证所需的信息。即

```
{
  "kid": "T_B6HlPCcu8jZjbWVZDi8s2l6euVEFTewiZba_zSd4E",
  "typ": "JWT",
  "alg": "RS256"
}
```

通过使用 JSON Web Key Set (JWKS)可以获得公钥，这是 IETF [标准](https://tools.ietf.org/html/rfc7517)。密钥通常缓存在客户端以最小化延迟，并在服务器端定期轮换。

下面是来自 jwks 端点的典型响应，显示了用于签署 JWT 的算法以及公钥的模数和指数。

```
{
 "keys"[ 
  {
   "kty":"RSA",
   "alg":"RS256",
   "kid":"T_B6HlPCcu8jZjbWVZDi8s2l6euVEFTewiZba_zSd4E",
   "use":"sig",
   "e":"AQAB",
   "n":"osU_UWAPB27w4vqfy7c_iRqB3JpFUMnK3w34fU0hoRVeWnMYzk-lwEbOPeghndyaEpKGJZZ3Md7qmQ7gFmKaAcNxtbtyC0Bq4sKkPJlLc-QbZ46AfVv36zzUC_dWVLTUXEVGF8fAISG660WKzQqLgx0UQHtnR3ht4F0rgXOYZ5n4K8dHZt3q7kR-2V_j0ornNjiID9F_GF1XmeMSES4uRHlTYtTTKeV69y8c7-F8SKz97pnJdeZDsYjDM1jsG-UEeVk54GSfOuVe2hMkluIWnpoIzlH8-AheqVf6GVdL6E-gyjfErqG2oedojo1zN_9RTgs6vaMf0Sut7j5UjqExxQ"
  }
 ]
}
```

当我们的微服务接收到一个包含 JWT 的请求时，首先检查缓存的键，在头中找到 kid 的匹配项。如果没有匹配，那么它将需要向 JWKS 端点请求最新的密钥集。只有当它找到与孩子匹配的密钥时，它才能尝试验证 JWT 的签名。如果一个我们无法验证的 JWT 到达，那么访问将被拒绝。

## 增加 Quarkus 的安全性

为了跟随代码，你可以拉动分支

```
git clone [git@github.com](mailto:git@github.com):iainporter/sms-service.git
git checkout part_four
```

首先，我们需要向 POM.xml 添加 OIDC 扩展

```
<dependency>
  <groupId>io.quarkus</groupId>
  <artifactId>quarkus-oidc</artifactId>
</dependency>
```

现在已经添加了依赖项，我们可以将安全注释添加到我们希望保护的每个端点。我们现在应该预料到对这些端点的任何测试都会因 401 未授权而失败。

注意，对于这个实现，我们所关心的是调用者有一个有效的 JWT。我们不检查角色或权限。这是下一篇文章的主题。然而，为了审计的目的，最好能捕获谁调用了服务，所以让我们注入 JWT(第 2 行)并从 JWT 中提取子声明，并将其与消息一起保存(第 7 行)。

## 单元测试 JWT 验证

为了通过测试，我们必须生成一个应用程序可以验证的 JWT。我们需要做到以下几点:

*   生成 RSA 密钥对
*   使用密钥对的私钥生成 JWT
*   存根 OIDC 已知端点返回一个指向 wiremock 实例的 URI
*   在我们的 wiremock 实例中存根 JWKS 端点，以返回密钥对的公钥
*   包括 JWT 作为请求报头中的承载令牌

为了帮助完成这些任务，我们可以使用光轮库

```
<dependency>
  <groupId>com.nimbusds</groupId>
  <artifactId>nimbus-jose-jwt</artifactId>
  <version>8.20</version>
  <scope>test</scope>
</dependency>
```

利用 Quarkus 中的 QuarkusTestResourceLifecycleManager 类，我们可以设置 wiremock 服务，并在 Quarkus 完成启动之前添加必要的存根。

测试子类可以使用私有密钥(第 9 行)通过下面的方法生成一个 JWT

OIDC 配置存根是完整的，但是我们的服务在测试中唯一需要的部分是 jwks_uri 来获取公钥

被模仿的 jwks_uri 需要返回我们上面生成的密钥对的公钥部分。

我们需要的最后一部分是使用密钥对中的私钥生成一个令牌，以便在测试中使用。

现在，我们可以测试传递有效令牌会导致成功的 post，不传递令牌会导致 401，而传递来自未知提供者的令牌会导致 403。

## 使用 Keycloak 进行组件测试

在之前的文章中，我们在 testcontainers 的帮助下编写了组件测试，以确保所有部分(微服务、数据库、kafka、wiremock)能够协同工作。我们可以为 Keycloak 创建一个容器，并用为测试设置的领域播种实例。

通常，用户不会直接调用 sms 微服务。它将被其他微服务用作涉及发送 SMS 消息的工作流的一部分。对于这个微服务，我们可以使用客户端凭证授权来获取访问令牌。这意味着我们需要在 keycloak 中设置一个领域，并添加一个可以使用客户端凭证授权登录的客户端。完成后，我们可以导出该领域，并将其作为 docker 容器设置的一部分进行引用，从而为测试播种实例。

在运行测试之前，我们可以使用领域中已配置的客户端详细信息来获取一个可以在测试中使用的访问令牌

## 以 Okta 作为 OIDC 提供商运行服务

您可以使用任何 OIDC 提供商，但是我们将演示如何使用 Okta 作为提供商。

如果你还没有注册，你可以在[https://developer.okta.com/signup/](https://developer.okta.com/signup/)注册一个免费的开发者账户

注册后，在以下位置添加授权服务器

API ->授权服务器->添加授权服务器

有了这些属性

名称:短信授权服务器
受众:[https://api.porterhead.com/sms](https://api.porterhead.com/sms)

添加范围:sms(设为默认)

创建一个应用程序并设置一个客户端，以便使用客户端凭据登录

将服务器 url 和客户端 id 属性添加到 config/application.properties 文件中

```
quarkus.oidc.auth-server-url=<Your Okta server url>
quarkus.oidc.client-id=<your client-id>
```

确保其余属性设置为与 SMS 提供商对话。参见[自述文件](https://github.com/iainporter/sms-service)。

从根目录构建服务

```
mvn install
```

使用 docker-compose 启动一切

```
cd sms-service
docker-compose up -d
```

为 kafa-connect 安装 sms 连接器

```
 curl 'localhost:8083/connectors/' -i -X POST \
-H "Accept:application/json" -H "Content-Type:application/json" \
-d '{"name": "sms-connector", "config": {"connector.class": "io.debezium.connector.postgresql.PostgresConnector", "database.hostname": "postgres-db", "database.port": "5432", "database.user": "postgres", "database.password": "postgres", "database.dbname" : "sms", "database.server.name": "smsdb1", "table.whitelist": "public.outboxevent", "transforms" : "outbox","transforms.outbox.type" : "io.debezium.transforms.outbox.EventRouter", "transforms.OutboxEventRouter.event.key": "aggregate_id", "transforms.outbox.table.fields.additional.placement": "type:header:eventType"}}' 
```

登录到您在上面配置的 OIDC 提供商，并获取访问令牌。

```
curl -i --request POST \
  --url [<](https://dev-638098.okta.com/oauth2/ausnk7ixdxwQWONYR4x6/v1/token)your okta server url> \
  --header 'accept: application/json' \
  --header 'authorization: Basic <your client-id:secret>' \
  --header 'cache-control: no-cache' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'grant_type=client_credentials&scope=sms'
```

发送消息

```
curl 'http://localhost:8080/v1/sms' -i -X POST  \
   -H 'Content-Type: application/json'  \
    -H 'authorization: Bearer <your access token>'
   -d '{"text":"Foo Bar!", "fromNumber": "+1234567890", "toNumber": "+<your mobile number>"}'
```

期待这样的回应

```
HTTP/1.1 202 Accepted
Content-Length: 0
Location: [http://localhost:8080/v1/sms/e307458a-a0a8-4f13-9635-f9b27b4da0e5](http://localhost:8080/v1/sms/e307458a-a0a8-4f13-9635-f9b27b4da0e5)
```

我们可以使用位置头来获取消息细节

```
curl 'http://localhost:8080/v1/sms/[e307458a-a0a8-4f13-9635-f9b27b4da0e5](http://localhost:8080/v1/sms/e307458a-a0a8-4f13-9635-f9b27b4da0e5)' -i -X GET  \
   -H 'Content-Type: application/json'  \
   -H 'authorization: Bearer <your access token>'
```

如果消息成功传递，结果应该如下所示

```
HTTP/1.1 200 OK
Content-Length: 194
Content-Type: application/json
{
"createdAt":"2020-07-16T16:43:59.43561Z",
"fromNumber":"+1234567890",
"id":"b3a20fac-2d00-49d2-b3ef-b3a3e5ac02ca",
"status":"DELIVERED",
"text":"Foo Bar!",
"provider": "Twilio",
"principal": "backend-service",
"toNumber":"+1234567891",
"updatedAt":"2020-07-16T16:44:00.432926Z"
}
```

服务现已完成。我们发布了一个使用 OpenAPI 的 API。消息保存在 Postgres 数据库中。Debezium 连接器跟踪 Postgres WAL 日志，并将更改事件发送给 Kafka。消费者正在收听这些事件，并将它们路由到 SMS 提供商，以便将消息发送到用户的移动设备。该服务使用 OpenId Connect 进行保护。我们有适当的单元测试和组件测试，以确保一切连接正确，并能对故障做出适当的响应。本系列的最后一篇文章将把服务部署为 GraalVM 中的本机可执行文件。

这篇文章的源代码可以在[这里](https://github.com/iainporter/sms-service)找到

该系列的其他部分包括:

*   [第一部分:构建框架并添加持久性](/building-a-microservice-from-the-ground-up-with-quarkus-kotlin-and-debezium-83ae5c8a8bbc)
*   [第二部分:使用 Kafka Connect 和 Debezium 实现 CDC](/implementing-the-transactional-outbox-pattern-with-debezium-in-quarkus-f2680306951)
*   [第三部分:连接到第三方 API 并用 Wiremock 进行测试](/building-a-resilient-microservice-with-quarkus-and-wiremock-de59b2a4fac7)
*   [第五部分:使用 GraalVM 本机运行](https://medium.com/@changeant/running-a-microservice-in-quarkus-on-graalvm-52d6b42a5840)
*   [第六部分:用 Jib 封装你的微服务](https://medium.com/@changeant/containerizing-your-microservice-in-quarkus-with-jib-fae0f62bd57e)
*   [第七部分:使用 CircleCI 为微服务构建 CI 管道](/building-a-ci-pipeline-for-a-microservice-in-quarkus-with-circleci-11e9b679423f)