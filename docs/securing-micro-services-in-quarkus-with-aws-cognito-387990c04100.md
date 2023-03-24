# 通过 Amazon Cognito 保护 Quarkus 中的微服务

> 原文：<https://levelup.gitconnected.com/securing-micro-services-in-quarkus-with-aws-cognito-387990c04100>

![](img/92a327bfffc8cbbc05450c63bc6e3d06.png)

在我之前写的关于用 Quarkus 和 Kotlin 从零开始构建一个微服务的系列文章中，这个服务是用 T2 OpenID Connect 保护的。前提是该服务将位于 API 网关之后，并且将被另一个使用 OIDC 提供商发布的 jwt 的微服务调用。对于组件测试，我们使用 Keycloak 作为提供者，这也是托管您自己的 OIDC 服务器的一个不错的选择。我们还演示了如何将 Okta 用作发行 jwt 的外部 OIDC 提供者。在本文中，我们将探索如何使用 Amazon Cognito。

# 什么是亚马逊认知

[Cognito](https://docs.aws.amazon.com/cognito/index.html) 是一款基于云的身份和访问管理解决方案。它支持 OAuth2 和 OpenId 连接。这是为您的平台管理大量用户的一种经济有效的方式。它由两个主要部分组成:

## 用户池

Cognito 中的一个用户目录，它提供了管理用户所需的所有安全服务，如注册、登录、MFA、社交登录、用户管理等。Cognito 将对通过联合登录收到的任何令牌进行标准化，并返回 Cognito 用户池(CUP)令牌。这些只是标准化的 jwt，所以你不必担心每种令牌的原始格式。

## 身份池

允许通过联合身份访问 AWS 服务。使用身份池，您可以获得临时访问令牌来与 AWS 服务进行交互。下图中的步骤 2 和步骤 3 对此进行了说明。

![](img/efb0d2fc306045e9d7a50c3732b81011.png)

# 保护服务对服务的访问

微服务的一个共同特征是它们需要相互通信。这可以是基于事件的，其中一个服务发出一个事件，一个或多个服务订阅接收该事件。另一种方法是使用 REST，在这种情况下，调用服务需要提供一个访问令牌。此令牌是使用客户端凭据流获得的。我们可以使用 Cognito 来发行这些令牌。对于这个场景，我们只需要用户池组件，如下所示。

![](img/51256b40321c1b37bb8d3bdfde4dd6f8.png)

# 设置 AWS CLI

如果您已经熟悉使用 CLI 设置，您可以跳过这一部分。使用 CLI，我们可以设置用户池和颁发令牌所需的其他组件。

如果您尚未安装 CLI，您可以按照这里的说明[操作](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

在使用 CLI 之前，我们需要在 AWS Web dashboard 中设置一个用户帐户。导航至 IAM ->用户->添加用户

创建用户，选择“程序化访问”并分配相关权限。

使用返回的凭据将它们添加到位于

```
~/.aws/credentials[foo_profile]
aws_access_key_id = AK*********
aws_secret_access_key = it**********
```

接下来，在以下位置为这些凭据添加新的配置文件

```
~/.aws/config[profile foo_profile]
region = us-west-2
output = json
```

调用 CLI 时，您可以通过使用配置文件选项来引用配置文件。即

```
> aws <command> --profile foo_profile
```

或者您可以设置一个环境变量

```
$ export AWS_PROFILE=foo_profile
```

# 创建用户池

Cognito 用户池 API 可以在[这里](https://docs.aws.amazon.com/cli/latest/reference/cognito-idp/index.html)找到

要创建新的用户池，请执行以下命令

```
$ aws cognito-idp create-user-pool --pool-name backend-client
```

这将创建用户池并返回一个 json 有效负载，列出模式属性和其他信息。我们需要拿到身份证，以便在其他地方使用。要获得简短的摘要，您可以使用以下命令

```
$ aws cognito-idp list-user-pools --max-results 20
```

这将返回类似如下的结果

```
{
    "UserPools": [
        {
            "CreationDate": 1601651956.335,
            "LastModifiedDate": 1601651956.335,
            "LambdaConfig": {},
            "Id": "us-west-2_Knm7CFToH",
            "Name": "backend-client"
        }
    ]
}
```

# 创建资源服务器

接下来，我们必须创建一个资源服务器，它代表我们希望用户池中的用户访问的资源，以及任何范围。对于这个用例，我们有一个简单的[短信服务](https://github.com/iainporter/sms-service)。该标识符可以是资源服务器的任何唯一值。我们只分配一个范围。

```
$ aws cognito-idp create-resource-server --name sms-service --identifier sms.porterhead.com --user-pool-id us-west-2_Knm7CFToH --scopes ScopeName=sms, ScopeDescription=send_sms
```

响应看起来像这样

```
{
    "ResourceServer": {
        "Scopes": [
            {
                "ScopeDescription": "send_sms",
                "ScopeName": "sms"
            }
        ],
        "Identifier": "sms.porterhead.com",
        "Name": "sms-service",
        "UserPoolId": "us-west-2_Knm7CFToH"
    }
}
```

# 创建客户端

我们需要创建将用于调用 sms 服务的客户端。客户端将使用客户端凭据授权。

```
$ aws cognito-idp create-user-pool-client --user-pool-id us-west-2_Knm7CFToH --allowed-o-auth-flows client_credentials --client-name backend-service --generate-secret --allowed-o-auth-scopes porterhead.com/sms --allowed-o-auth-flows-user-pool-client
```

从回答中我们可以提取秘密

```
{
    "UserPoolClient": {
        "ClientSecret": "16a8************************",
        "AllowedOAuthScopes": [
            "porterhead.com/sms"
        ],
        "UserPoolId": "us-west-2_*******",
        "AllowedOAuthFlowsUserPoolClient": true,
        "LastModifiedDate": 1601899598.238,
        "ClientId": "3e58***************",
        "AllowedOAuthFlows": [
            "client_credentials"
        ],
        "RefreshTokenValidity": 30,
        "CreationDate": 1601899598.238,
        "ClientName": "backend-service"
    }
}
```

# 创建域

我们必须创建一个域服务器来颁发令牌。您可以选择委托给自己的定制域或使用 Amazon Cognito 托管的域。域名在亚马逊应该是唯一的。

```
aws cognito-idp create-user-pool-domain --domain porterhead --user-pool-id us-west-2_Knm7CFToH
```

一旦域名被创建并准备就绪，它将可在一个独特的网址与您的域名前缀

```
[https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/token](https://porterhead.auth.us-east-1.amazoncognito.com/oauth2/token)
```

# 配置夸库

正如我们在[之前的帖子](/securing-a-microservice-in-quarkus-with-openid-connect-505204d1c9a9)中发现的，设置 Quarkus 使用 OIDC 非常简单。事实上，我们不需要对应用程序做任何事情，我们只需要修改运行时属性来指向新的 OIDC 提供者。

Quarkus 将负责回电以验证 JWT。我们需要告诉它从哪里获得 OIDC 配置。

在配置文件中，我们指向一个 Okta 认证服务器

```
quarkus.oidc.auth-server-url=[https://dev-****.okta.com/oauth2/*](https://dev-638098.okta.com/oauth2/ausnk7ixdxwQWONYR4x6)********
```

我们可以将它改为指向 Cognito 服务器

```
quarkus.oidc.auth-server-url=[https://cognito-idp.us-west-2.amazonaws.com/](https://cognito-idp.us-west-2.amazonaws.com/us-west-2_Knm7CFToH)us-west-2_Knm7CFToH
```

夸尔库斯会追加。此 URL 的“已知/OpenID-配置”路径

```
[https://cognito-idp.us-west-2.amazonaws.com/](https://cognito-idp.us-west-2.amazonaws.com/us-west-2_Knm7CFToH/.well-known/openid-configuration)us-west-2_Knm7CFToH[/.well-known/openid-configuration](https://cognito-idp.us-west-2.amazonaws.com/us-west-2_Knm7CFToH/.well-known/openid-configuration)
```

我们可以检查这个 URL 来查看返回的属性

```
{
 "authorization_endpoint": "[https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/authorize](https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/authorize)",
 "id_token_signing_alg_values_supported": ["RS256"],
 "issuer": "[https://cognito-idp.us-west-2.amazonaws.com/](https://cognito-idp.us-west-2.amazonaws.com/us-west-2_Knm7CFToH)us-west-2_Knm7CFToH",
 "jwks_uri": "[https://cognito-idp.us-west-2.amazonaws.com/](https://cognito-idp.us-west-2.amazonaws.com/us-west-2_Knm7CFToH/.well-known/jwks.json)us-west-2_Knm7CFToH[/.well-known/jwks.json](https://cognito-idp.us-west-2.amazonaws.com/us-west-2_Knm7CFToH/.well-known/jwks.json)",
 "response_types_supported": ["code", "token"],
 "scopes_supported": ["openid", "email", "phone", "profile"],
 "subject_types_supported": ["public"],
 "token_endpoint": "[https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/token](https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/token)",
 "token_endpoint_auth_methods_supported": ["client_secret_basic", "client_secret_post"],
 "userinfo_endpoint": "[https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/userInfo](https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/userInfo)"
}
```

让我们也检查一下 jwks_uri 是否返回了我们期望的结果

```
{
 "keys": [{
  "alg": "RS256",
  "e": "AQAB",
  "kid": "0x5vxTf0YCGHCkEGxgrU3lY/I9H0+DbhRfyq3249mt8=",
  "kty": "RSA",
  "n": "i9UJRoeUrm5ErIbO73K6rFAo4lgsKSj6LF1VrUqIn38UzXSl0pFA5chluMfjPaBSddgdbOA7EOD4ws3LknQy-aDWq3BvN1FSUgTjQZitejgshJboxv2fweh-gRMh1mIqxrL3cFCg_6OFknmWPLPdlxAR8LLBrdFkq5YrpIPNrF1wzgoQ-yy29IE0gvOsEMJ8Y3FZ7F65kieU-IiSzC1YEvVzSNtDMDzf0bDdwkSoFvQ2wj7-6F04g7zgJIolsuxzR1kTefVBEoNB8Du-byAWdcPm3GMEmXQngoFTmGatHwsrIiHXxDvRNj9UBt8uf2dpqkcGVpzKcYlCQjyXIQAmUQ",
  "use": "sig"
 }]
}
```

一旦修改了属性，我们就可以使用这里的指令[启动一切，并在获得访问令牌后调用服务。](https://github.com/iainporter/sms-service)

# 获取访问令牌

对于客户端凭证，我们需要对凭证进行 Base64 编码，并在标头中使用它。

```
echo -n '<clientId>:<secret>' | openssl base64 -Acurl -X POST \
  [https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/token](https://porterhead.auth.us-west-2.amazoncognito.com/oauth2/token) \
  -H 'authorization: Basic M2U1OHMwO******************' \
  -H 'content-type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&scope=porterhead.com%2Fsms'
```

我们应该取回一个有效的令牌

```
{"access_token":"eyJraWQiOiIweDV2eFRmMFlDR0hDa0VHeGdyVTNsWVwvSTlIMCtEYmhSZnlxMzI0OW10OD0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiIzZTU4czA5YnJvamRvbHNlcjRsMDd0dmY0ayIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoicG9ydGVyaGVhZC5jb21cL3NtcyIsImF1dGhfdGltZSI6MTYwMTkwMjQwNSwiaXNzIjoiaHR0cHM6XC9cL2NvZ25pdG8taWRwLnVzLXdlc3QtMi5hbWF6b25hd3MuY29tXC91cy13ZXN0LTJfS25tN0NGVG9IIiwiZXhwIjoxNjAxOTA2MDA1LCJpYXQiOjE2MDE5MDI0MDUsInZlcnNpb24iOjIsImp0aSI6IjNjZjRjOGFlLWFjYTctNDQ3Yy04MmIzLWMwYjY3ZjhjNzM4ZCIsImNsaWVudF9pZCI6IjNlNThzMDlicm9qZG9sc2VyNGwwN3R2ZjRrIn0.dZR9tNAqqx3eDWTufM6ZAtG30c7LljqmvcVG-mLx21ivaYteqktdWitRfXZYLiU_y85ya4oqUy7y4C8Yu_UIfq6SxIUiilm_JyQ4WkZ_iKfsYV1UKmQl7V_ppSwyAleYW-L2b1YDnlNaUBhXQZLr0S_fzHJuAruHWLUzEDch6unv2XEp63SAtZb0tcUlkiPZ5LzaSKlPqFX-SWGF_abilUggrRGbxc6Z35Rwh6UGx1Q2A8JEgs3Zwbh8CTSKquLKf6Dw7KUEz6GXkc0J1EMlvQkjs8fLOi09hVe9cGn8yJ9j1eIasZm17LoIbo34nMa9ocpIAC0ThGzH6ZxuMqGT6Q","expires_in":3600,"token_type":"Bearer"}
```

如果我们把它放在 jwt.io 中，我们可以看到有效负载，它也使用了我们期望的公钥

![](img/3136ee725870170c563b0c857fee5a96.png)

我们可以使用这个令牌来调用服务

```
curl 'http://localhost:8080/v1/sms' -i -X POST  \
   -H 'Content-Type: application/json'  \
   -H 'authorization: Bearer <your access token>'
   -d '{"text":"Foo Bar!", "toNumber": "<your SMS number"}'
```

这只是您如何使用 Amazon Cognito 来管理您的微服务平台中的 AuthZ 和 AuthN 的皮毛。对于越来越多的可用即时消息，它是一个有价值的竞争对手。