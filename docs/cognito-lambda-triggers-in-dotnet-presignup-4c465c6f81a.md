# Cognito 触发深度潜水—预注册

> 原文：<https://levelup.gitconnected.com/cognito-lambda-triggers-in-dotnet-presignup-4c465c6f81a>

本文是 Dotnet 系列中 **Cognito Lambda 触发器的一部分，致力于理解和处理 Cognito Lambda 触发器。**

如果你想了解 Lambda 触发器，以及如何设计一个 Lambda 函数来更普遍地处理各种触发器源，请参考 Dotnet 中的相关母文章 [Cognito Lambda 触发器。如果你去那里，你也会发现处理其他触发源的链接。](https://medium.com/@oliver.schenk/cognito-lambda-triggers-in-dotnet-3bf13a55eda3)

在以下章节中，我们将关注**预启动**触发源。您将在 Cognito [文档](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-pre-sign-up.html)中找到技术细节，但是通常更重要的是理解它为什么存在以及为什么您可能想要使用它。

在这里你会找到一个解释，包括用例、有效负载示例、处理程序实现和测试脚本。

你会在 Github 上找到[源代码。这将随着时间的推移，更多的例子更新。](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet)

![](img/7624f5e4839d2e9c0305e103925d8356.png)

[regularguy.eth](https://unsplash.com/@moneyphotos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/password?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 预注册

`PreSignUp_*`触发器属于一类触发器，当收到新用户注册请求时，在 Cognito 注册新用户之前触发。

根据创建新用户的请求来自何处，有三种可能的触发源。

*   **预注册 _ 注册** —通过[托管 UI](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-app-integration.html) 或[注册](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_SignUp.html) API 调用的用户自助注册流程
*   **presign up _ AdminCreateUser**—用户由系统使用 [AdminCreateUser](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_AdminCreateUser.html) API 调用创建(比如由系统管理员创建)
*   **pre sign up _ external provider**—用户首次使用外部提供商登录，如社交登录(如脸书、谷歌等)、SAML(如 Active Directory)或 OIDC(如 Salesforce)。
    注意，设置起来有点复杂，因为它涉及到将第三方系统与 Cognito 连接起来。参见[通过第三方添加用户池登录](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-identity-federation.html)。

# 事件有效负载

传递给触发器处理函数的事件有效负载将由一组[公共参数](https://medium.com/@oliver.schenk/cognito-lambda-triggers-in-dotnet-3bf13a55eda3#ddd6)和特定于触发器源的请求和/或响应属性组成。

## 请求

预注册`request`参数由以下属性组成。

`ClientMetadata`和`ValidationData`属性是键值数据，当进行`AdminCreateUser`或`SignUpUser` API 调用时，这些数据被传递到 Lambda 触发器处理程序中。

`ClientMetadata`属性的目的是允许客户端将进一步的信息传递给预注册功能。`ValidationData`的目的是允许客户端传递关于用户验证的更多信息。您的函数可以使用这些额外的信息。

因为这些数据来自客户端，从安全的角度来看，只有当注册是在安全的后端环境中通过 API 调用而不是通过公共客户端完成时，这些数据才是真正可靠的。

使用元数据的一种方法是将信息从客户端传递到后端，然后在另一个触发器处理程序中重用该信息。

## 反应

预注册响应参数由以下属性组成。

**AutoConfirmUser** —将此项设置为 true 以自动将用户的确认状态设置为已确认，而不是发送验证电子邮件或短信。他们将能够立即登录。

**自动验证电话** —将此项设置为 true，以将电话号码标记为已验证。

**自动验证电子邮件** —将此项设置为 true 以将电子邮件地址标记为已验证。

# 用例

此触发器的目的是确定是否应该允许用户注册，如果是，他们是否需要验证他们的详细信息，或者用户或他们的电子邮件或电话号码详细信息是否可以被强制为已验证状态。

以下是您可以做的一些示例:

*   您可以通过引发异常来防止创建用户。您可以做一些简单的事情，如检查用户属性的长度，也可以做一些复杂的事情，如调用其他服务来检查该用户是否已经存在，电子邮件地址域是否在允许的域内，或者电话号码是否在特定的国家内，等等。
*   通常，新用户将处于未确认状态。但是，您可以通过将`AutoConfirmUser`参数设置为`true`来强制用户在创建后立即进入确认状态。例如，您可以根据企业客户端的域电子邮件地址来验证它们，而不需要它们验证自己。如果您这样做，将不会发送验证码。
*   您可以通过将`AutoVerifyEmail`或`AutoVerifyPhone`参数设置为`true`来强制自动验证电子邮件和/或电话属性。这通常与`AutoConfirmUser`配合使用。
*   如果您使用多个使用相同电子邮件地址的外部提供商，您可以使用预注册触发器将两个提供商链接到同一个 Cognito 用户。参见下面的 [stackoverflow post](https://stackoverflow.com/questions/59635482/aws-cognito-best-practice-to-handle-same-user-with-same-email-address-signing) 。

# 重要提示

有些事情你需要注意:

*   使用 AdminCreateUser API 调用时，Cognito 会忽略`AutoConfirmUser`、`AutoVerifyEmail`和`AutoVerifyPhone`参数。
*   通过 AdminCreateUser API 调用创建的用户会被自动确认，并且用户的状态会变为 FORCE_CHANGE_PASSWORD。然后，用户必须在登录后或首次登录时更改密码。
*   当将`AutoConfirmUser`设置为`true`并且不验证用户的电子邮件地址或电话号码时要小心。如果用户忘记了密码，而没有经过验证的电子邮件或电话号码，他们将无法恢复密码。

关于电子邮件和电话号码验证， [Cognito 文档](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-lambda-pre-sign-up.html)也提供了一些重要的提示。

> **注意:**如果已经存在相同电话号码的别名，该别名将被移动到新用户，之前用户的`phone_number`将被标记为未验证。电子邮件地址也是如此。为了防止这种情况发生，您可以使用用户池 [ListUsers API](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_ListUsers.html) 来查看是否有一个现有用户已经在使用新用户的电话号码或电子邮件地址作为别名。

# 处理程序示例

下面是每个预注册触发源的示例处理程序实现。模板可以在 [GitHub 库](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet/tree/main/lambda-triggers/src/CognitoLambdaTriggers/Handlers)中找到。

## 预注册 _ 注册

在这个例子中，触发处理器自动确认用户，但是假设不同的过程将处理电子邮件/电话验证。

## 预注册 _AdminCreateUser

在这个例子中，触发器处理程序抛出一个异常并拒绝用户注册。

## 预注册 _ 外部提供商

在这个例子中，触发器处理程序检查具有给定用户名的用户是否已经存在，如果存在，就将新的提供者链接到这个现有用户。

# 测试

为了简化初始测试，我提供了一些示例脚本，可以用来创建用户和清除用户。只是要小心删除用户的脚本，因为它会删除指定 Cognito 用户池中的所有用户！

*   [*admin-create-user . sh*](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet/blob/main/lambda-triggers/test/commands/admin-create-user.sh)—使用 AdminCreateUser API 调用创建用户
*   [*create-user-sign up . sh*](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet/blob/main/lambda-triggers/test/commands/create-user-signup.sh)—使用自行注册创建用户
*   [*delete-users . sh*](https://github.com/oliverschenk/cognito-lambda-triggers-dotnet/blob/main/lambda-triggers/test/commands/delete-users.sh)—删除给定用户池中的所有用户

在我下面的示例中，你会看到我已经将用户池 ID 和用户池客户端 ID 分别放入环境变量`USER_POOL_ID`和`USER_POOL_CLIENT_ID`中，以避免一直粘贴这些值。

## 注册处理程序

可以使用`aws cognito-idp sign-up` CLI 命令触发注册处理程序。为了简化事情，我创建了一个`create-user-signup.sh`脚本来简化创建一个新的随机用户。

要使用该脚本，请在您的用户池客户端 ID 后输入一个电子邮件地址。该脚本将使用`+`符号自动生成一个随机别名。在电子邮件世界中，这仍然会在你的基本电子邮件地址结束。其他的都是随机生成的。

这个脚本不需要任何 AWS 凭证，因为它应该由未经身份验证的用户调用。

```
$ export USER_POOL_CLIENT_ID=<your_user_pool_client_id>$ ./create-user-signup.sh $USER_POOL_CLIENT_ID your_email@account.comCreating a user with the following details:
[Email=your_email+3aedd2@account.com](mailto:Email=oliver.schenk+3aedd2@gmail.com)
Phone=+61437340252
Name=Susan6599
Password=0117e007b8cdce52dd98ABC!UserConfirmed: true
UserSub: 9ff6b3de-0f13-48b1-8e29-af4190a1cf2f
Finished.
```

您会注意到，如果您在 Lambda 触发器处理程序中将`AutoConfimUser`设置为`true`，您会看到 Cognito 调用将使用`UserConfirmed: true`进行响应，并且您不会收到任何确认电子邮件。但是，您的电子邮件地址仍将无法验证。

我们可以证明用户状态如下。

```
$ aws cognito-idp admin-get-user --user-pool-id $USER_POOL_ID --username 9ff6b3de-0f13-48b1-8e29-af4190a1cf2f
Enabled: true
UserAttributes:
- Name: sub
  Value: 9ff6b3de-0f13-48b1-8e29-af4190a1cf2f
- Name: email_verified
  Value: 'false'
- Name: name
  Value: Susan6599
- Name: phone_number_verified
  Value: 'false'
- Name: phone_number
  Value: '+61437340252'
- Name: email
  Value: [your_email+3aedd2@account.com](mailto:Email=oliver.schenk+3aedd2@gmail.com)
UserCreateDate: '2022-08-29T22:15:07.324000+08:00'
UserLastModifiedDate: '2022-08-29T22:15:07.324000+08:00'
UserStatus: CONFIRMED
Username: 9ff6b3de-0f13-48b1-8e29-af4190a1cf2f
```

如您所见，`UserStatus`是`CONFIRMED`，但是`email_verified`字段是`false`。这意味着用户可以使用用户名(在这种情况下是电子邮件或电话号码)和密码登录，但如果用户忘记了密码，则不可能恢复帐户。

我们可以试着重设密码。

```
$ aws cognito-idp forgot-password --client-id $USER_POOL_CLIENT_ID --username 9ff6b3de-0f13-48b1-8e29-af4190a1cf2f
CodeDeliveryDetails:
  AttributeName: email
  DeliveryMedium: EMAIL
  Destination: 9***@h***
```

你可能想知道为什么我们得到了成功的回应？如果你仔细观察，这实际上不是一个成功的回答，而是假装的。

这实际上是由于 Cognito 处理错误响应的方式。在用户池客户端的 Terraform 代码中,`prevent_user_existence_errors`参数被设置为`ENABLED`,这意味着 Cognito 将掩盖具有给定用户名的用户不存在或电子邮件和/或电话号码未被验证的事实。这是一个安全特性[，记录在这里](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pool-managing-errors.html)。

## AdminCreateUser 处理程序

可以使用`aws cognito-idp admin-create-user` CLI 命令触发 AdminCreateUser 处理程序。为了简化事情，我创建了一个`admin-create-user.sh`脚本来简化创建一个新的随机用户。

与注册调用不同，AdminCreateUser 调用需要 AWS 凭证，因为它是供管理 Cognito 用户池及其用户的用户或服务使用的。

下面的示例是处理程序引发异常时收到的响应。异常消息将包含在 CLI 错误消息中。

```
$ ./admin-create-user.sh $USER_POOL_ID [your_email@account.com](mailto:oliver.schenk@gmail.com)
Creating a user with the following details:
[Email=](mailto:Email=oliver.schenk+fb08c0@gmail.com)[your_email](mailto:Email=oliver.schenk+3aedd2@gmail.com)[+fb08c0@account.com](mailto:Email=oliver.schenk+fb08c0@gmail.com)
Phone=+61453330042
Name=Susan1348
Password=0ddad52a3ead8331ea6bABC!An error occurred (UserLambdaValidationException) when calling the AdminCreateUser operation: PreSignUp failed with error User could not be created..
```

让我们看看如果我们不在处理程序中抛出异常会发生什么(注释掉在处理程序中抛出异常的代码并重新部署 Lambda)。

```
./admin-create-user.sh $USER_POOL_ID [your_email@account.com](mailto:oliver.schenk@gmail.com)
Creating a user with the following details:
[Email=your_email+8ebc46@account.com](mailto:Email=oliver.schenk+8ebc46@gmail.com)
Phone=+61418115255
Name=Susan25233
Password=ef7dfcf02365818ee008ABC!User:
  Attributes:
  - Name: sub
    Value: 74740674-e7aa-4bf7-9bbf-74ff3d80ca98
  - Name: name
    Value: Susan25233
  - Name: phone_number
    Value: '+61418115255'
  - Name: email
    Value: [your_email+8ebc46@account.com](mailto:Email=oliver.schenk+8ebc46@gmail.com)
  Enabled: true
  UserCreateDate: '2022-08-31T20:47:00.770000+08:00'
  UserLastModifiedDate: '2022-08-31T20:47:00.770000+08:00'
  UserStatus: FORCE_CHANGE_PASSWORD
  Username: 74740674-e7aa-4bf7-9bbf-74ff3d80ca98
Finished.
```

这一次创建了用户。用户已被确认，但状态为`FORCE_CHANGE_PASSWORD`，这意味着用户必须在下次登录时更改密码。

AdminCreateUser API 调用有许多可能的参数，可以用来控制注册和验证流程。关键是触发响应不控制用户属性验证的行为。传递给 AdminCreateUser API 调用的参数决定了是否发送邀请消息、是否自动验证用户属性等等。

最好的学习方法是尝试不同的设置。

## 外部提供程序处理程序

ExternalProvider 处理程序仅在用户首次使用外部提供程序登录时调用一次。

我不打算在这里花太多时间讨论如何建立一个外部提供者，因为那将是另一整篇文章，而且已经有一些了。

触发器的关键点在于，当第一次使用外部提供者时，这使您有机会采取一些措施。

例如，您可以检查用户名或电子邮件是否已经存在于用户池中，如果已经存在，那么使用[AdminLinkProviderForUser](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_AdminLinkProviderForUser.html)API 调用来链接它。

您可能会自动验证电子邮件地址，但您可能需要手动询问并验证电话号码，因为这不是由外部提供商提供的。你会发现，如今许多应用程序将使用多步用户注册流程，而不仅仅是一个大型注册表单，特别是因为外部提供商可能只会给你电子邮件地址，而不会给你其他任何信息。

还是那句话，这种题目本身就是一个相当大的题目。

# 其他触发器实现

观看[这个空间](https://awstip.com/cognito-lambda-triggers-in-dotnet-3bf13a55eda3)中关于如何处理每种类型触发源的解释和例子的文章。

关注我或订阅以接收这些文章发布时的通知。

感谢您的宝贵时间！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)