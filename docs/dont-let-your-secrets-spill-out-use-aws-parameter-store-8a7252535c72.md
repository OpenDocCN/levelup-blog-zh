# 不要让你的秘密泄露出去；使用 AWS 参数存储

> 原文：<https://levelup.gitconnected.com/dont-let-your-secrets-spill-out-use-aws-parameter-store-8a7252535c72>

## 在 AWS Lambda 中访问 API 密钥等秘密的简单方法

![](img/3e71a3a67486e0a932e398e812425e57.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Manja Vitolic](https://unsplash.com/@madhatterzone?utm_source=medium&utm_medium=referral) 拍摄的照片

使用 Lambda 开发函数时，您会遇到需要访问外部第三方应用程序(例如 Stripe)的情况。为此，您很可能需要由外部服务提供的 API 密钥。

第一反应是将这个 API 键保存为一个环境变量。查看 Terraform 中的代码:

```
resource "aws_lambda_function" "test" {  
  filename = "test.zip"
  function_name = "test"
  handler = "test.handler"
  source_code_hash = filebase64sha256("test.zip")
  runtime = "nodejs12.x" environment {
    variables = {
      API_KEY = "api-key"
    }
  }
}
```

尽管这对于快速实现来说很好，但它并不是 100%安全的，因为 API 密钥是通过 AWS 控制台以明文形式可见的。我知道如果你遵循 [AWS 最佳实践](https://aws.amazon.com/blogs/security/getting-started-follow-security-best-practices-as-you-configure-your-aws-resources/)，有人访问你的 AWS 账户的可能性很小，但安全总比后悔好。

好在 AWS 为这个用例提供了一个服务:[参数存储](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)。

我们可以像这样更新我们的 Terraform 代码:

```
resource "aws_ssm_parameter" "ssm_api_key" {
  name  = "ssm-api-key"
  type  = "SecureString"
  value = "api-key"
}resource "aws_lambda_function" "test" {
  filename = "test.zip"
  function_name = "test"
  handler = "test.handler"
  source_code_hash = filebase64sha256("test.zip")
  runtime = "nodejs12.x"environment {
    variables = {
      API_KEY = "ssm-api-key"
    }
  }
}
```

这样，您可以在环境变量中存储对 API 键的引用。在您的代码(例如 JS)中，您使用 AWS SDK 或另一个库(例如 [aws-param-store](https://www.npmjs.com/package/aws-param-store) )来访问它的值。

```
'use strict'import paramStore from 'aws-param-store'const API_KEY = process.env.API_KEY
const REGION = process.env.REGIONconst apiKey = paramStore.getParameterSync(API_KEY, { region: REGION })?.Value
```

请记住更新 Lambda 函数的 IAM 角色，以允许访问 SSM:

```
{
  "Effect": "Allow",
  "Action": ["ssm:GetParameter"],
  "Resource": [
   "arn:aws:ssm:${var.region}:${var.account_id}:parameter/ssm-api-key"
  ]
}
```

你的秘密现在安全了🎉

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)