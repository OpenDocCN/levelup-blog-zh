# 如何在 AWS 上存储 secretes？

> 原文：<https://levelup.gitconnected.com/how-to-store-secretes-on-aws-3e2f5881cd5b>

## AWS SSM 参数存储和机密管理器的使用快速概述

![](img/9db904a8692efb8cbcc1ddd535f4842d.png)

在 [Unsplash](https://unsplash.com/s/photos/secrets?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[nguyễn·普克](https://unsplash.com/@beemagic275?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

安全性是架构良好的云系统最重要的特性之一。将密码或其他机密数据直接存储在应用程序的代码中会产生严重的安全风险，并使机密管理更加困难。幸运的是，有一种更好的方法可以使用 AWS 轻松实现。机密数据可以在独立的系统中管理和加密，并通过 API 调用公开。有两种服务非常适合这项工作。两者都提供了一种可扩展且安全的数据管理方式，但在一些可用功能上有所不同。

第一个是 [AWS 系统管理器参数存储库](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)。它利用 KMS 等其他 AWS 服务来提供加密功能，并利用 IAM 来实现细粒度的访问控制。它允许在标准层存储最多 4096 个字符的明文或加密数据，在高级层存储最多 8192 个字符。参数名也可以加上前缀，以提供额外的上下文，如应用程序名、环境或版本。

让我们创建一些加密的秘密，允许我们的应用程序连接到数据库。

```
aws ssm put-parameter --cli-input-json \
   '{
        "Name": "/SampleApp/uat/db/postgres/password", 
        "Value": "secret-password", 
        "Type": "SecureString",
        "Tags": [
            {
            "Key": "env",
            "Value": "UAT"
            },
            {
            "Key": "app",
            "Value": "SampleApp"
            }
         ]
    }'

aws ssm put-parameter --cli-input-json \
   '{
        "Name": "/SampleApp/uat/db/postgres/endpoint", 
        "Value": "secret-enpoint.net", 
        "Type": "SecureString",
        "Tags": [
            {
            "Key": "env",
            "Value": "UAT"
            },
            {
            "Key": "app",
            "Value": "SampleApp"
            }
         ]
    }'// Fetch paramaters    
aws ssm get-parameters-by-path --path "/SampleApp/uat/db/postgres" --with-decryption
```

现在，让我们创建一个使用这些秘密的示例客户机

AWS Secrets Manager 就像服用了类固醇的 SSM 参数商店。它提供了非常相似的功能，但顾名思义，它是用来存储机密数据的，因此它实施了加密。此外，它内置了对许多数据库引擎的自动密码轮换的支持，或者可以触发我们自己的 Lambda 函数。它还可以自动生成安全密码。

让我们使用秘密管理器创建一些秘密。为了简单起见，自动密码轮换将被禁用

```
aws secretsmanager create-secret  \
    --name /SampleApp/uat/db/postgres/password \
    --secret-string "secret-password" \
    --tags Key=env,Value=UAT Key=app,Value=SampleApp

aws secretsmanager create-secret  \
    --name /SampleApp/uat/db/postgres/endpoint \
    --secret-string "secret-enpoint.net" \
    --tags Key=env,Value=UAT Key=app,Value=SampleApp

aws secretsmanager list-secrets \
    --filters Key=name,Values=/SampleApp/uat/db/postgres
```

使用秘密管理器也非常简单

如果我们想让一项服务为我们做更多繁重的工作，它必须付出代价。两者的价格都是每 10，000 个 API 调用 0.05 美元，但是秘密管理器也每月每个秘密收取 0.40 美元(SSM 参数商店中的高级层也每月每个秘密收取 0.05 美元)

> *完整的源代码可从这里获得:*

[](https://github.com/jkapuscik2/aws-secrets-demo) [## jkapuscik 2/AWS-秘密-演示

### 在 GitHub 上创建一个帐户，为 jkapuscik2/aws-secrets-demo 开发做贡献。

github.com](https://github.com/jkapuscik2/aws-secrets-demo) 

**资源:**

[](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) [## AWS 系统管理器参数存储

### AWS 系统管理器参数存储(参数存储)为配置数据提供安全的分层存储…

docs.aws.amazon.com](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) [](https://aws.amazon.com/secrets-manager/) [## AWS 机密管理器|旋转、管理、检索机密| Amazon Web Services (AWS)

### 在数据库凭证、API 密钥和其他机密的整个生命周期中，轻松地轮换、管理和检索它们…

aws.amazon.com](https://aws.amazon.com/secrets-manager/)