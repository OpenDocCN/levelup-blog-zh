# 在节点中实施双因素身份认证的 3 个简单步骤。射流研究…

> 原文：<https://levelup.gitconnected.com/3-easy-steps-to-implement-two-factor-authentication-in-node-js-559905530392>

## 2FA 实现起来没有你想的那么恐怖，我用 3 个步骤证明给你看。

![](img/0e40efb4d2ed186fe1f5186853beef79.png)

由[克里斯多夫·高尔](https://unsplash.com/@cgower?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

双因素身份验证(2FA)是第二层帐户保护，我强烈建议每个人都应该启用它。与传统的用户名/密码不同，2FA 要求用户输入与个人信息相关的附加信息。例如，最广为人知的 2FA 形式要求最终用户回答一个安全问题。如果最终用户是设置安全问题的人，他们回答问题应该没有问题。安全问题可以是:你母亲的娘家姓是什么？你的第一只宠物叫什么名字？你的家乡叫什么名字？

2FA 的最新形式是通过电子邮件或 SMS 接收令牌，或者通过认证器应用程序(如 Google Authenticator)获得令牌。服务器根据用户帐户生成密钥。秘密密钥有助于生成最终用户用来验证其身份的后续令牌。如果最终用户是帐户的持有者，那么他们应该知道在哪里寻找令牌(SMS、电子邮件或验证器应用程序)，并且令牌应该可以根据秘密进行验证。

有几种方法可以实现 2FA，但是本文将回顾使用 npm 包实现 2FA。

首先，让我们快速回顾一下 OTP。

## 什么是 OTP(一次性密码)？

一次性密码顾名思义，就是最终用户使用一次的密码。我喜欢称它们为代币。与终端用户创建和知道的传统密码不同，服务器或认证器应用程序会生成 OTP。一次性密码依靠两样东西:**一个秘密和一个移动因子。**

最初的 OTP 被称为 HOTP *(基于 HMAC 的一次性密码或基于哈希的一次性密码)*。HOTP 是由互联网工程任务组(IETF)发布的一种算法，命名为 RFC4226。这种类型的动态口令的移动因素是计数器。

生成的令牌是使用密钥*(也称为种子)*和计数器的 HMAC 散列。散列的输出很长，所以为了更适合作为令牌，将其缩短。每个令牌都有一个计数器，只要终端用户生成一个令牌，针对该令牌的计数器就会增加。服务器也有一个计数器，但是当令牌被验证时，它的计数器增加。

这种认证的关键是服务器上的*前瞻窗口*。服务器计数器和令牌计数器很容易不同步，因此为了重新同步这两个计数器，服务器使用大小为 *n 的先行窗口。*如果令牌计数器落在窗口内，则可以对其进行验证。然而，这个算法有一个问题。如果生成的令牌数量超过了前瞻窗口，您将最终锁定系统。换句话说，你将无法登录。我让您自己去探究这个安全问题。

TOTP 是基于时间的一次性密码，基于 HOTP，由 IETF[发布为](https://en.wikipedia.org/wiki/Internet_Engineering_Task_Force) [RFC6238](https://tools.ietf.org/html/rfc6238) 。与计数器的移动因子 HOTP 不同，TOTP 是一种时间移动因子的算法。生成的令牌在一段时间内有效，也称为时间步长。例如，如果您打开 Google Authenticator 应用程序，您会注意到一个计时器正在针对您的一些令牌运行。一旦定时器到期，认证器生成新的令牌。

`*speakeasy*`包支持这两种算法，但是在本文中，我将把重点放在 TOTP 上。

正如我之前提到的，您可以通过短信或电子邮件发送令牌，或者使用 Authenticator 应用程序获取令牌。如果您选择 Authenticator 应用程序路线，请为最终用户生成一个二维码，供其在初始设置 2FA 时扫描。然后使用验证者生成的令牌来验证秘密。如果您选择 SMS/电子邮件路由，请创建一个要发送的令牌，并根据该令牌进行验证。

我想强调所有这些都应该发生在服务器上！请不要在客户端传递秘密。它违背了安全的目的。客户端应该显示二维码，只需一次，供最终用户扫描。

## 产生一个秘密

`speakeasy`方法`generateSecret`返回一个带有几种编码的对象，比如 ASCII、十六进制、base32、otpauth_url 等。

```
import { generateSecret } from 'speakeasy'interface TwoFactorEntity {
  userId: number
  secret: string
  enabled: boolean // default value is false
}const generateUserSecret = 
(userRepo: Respository, twoFactor: TwoFactorEntity) => {const secret = generateSecret()
   twoFactor.secret = secret.base32
   userRepo.save(twoFactor)
   return secret}
```

生成密码后，将其中一种编码存储在数据库中。这个存储应该是一个临时的占位符，或者有一些指示该帐户还没有启用 2FA。

最终用户必须完成设置*(生成密码，验证令牌)*以启用 2FA。如果他们在设置时没有成功验证该密码，则该密码是无用的，并且不应该针对该密码生成令牌，因为最终用户将永远无法登录。

在我的实现中，我在一个与用户`TwoFactorEntity`有关系的实体上创建了一个`enabled`列。如果最终用户在设置期间成功验证了秘密，则该标志为*真*。在随后的验证中，该标志不应复位。拥有这个列意味着我可以在我的数据库中只存储一次`secret`(也就是说，我不需要一个`temporary`列和一个`permanent`列)。

还要注意，我返回了秘密对象；这要是为了下一步！

## 显示二维码

**如果你使用的是 Authenticator 应用程序路线，请继续阅读！**

**如果没有，跳到下一个标题。**

安装`qrcode`

使用 secret 对象，将`otpauth_url`编码传递给`toDataURL`方法，它将返回一个 PNG 数据 URL。在 IMG 标签中显示此 URL，供最终用户扫描。记住在服务器上生成这个数据 URL，并将 URL 传递回客户机。不要传递秘密！

```
import { toDataURL } from 'qrcode'interface SecretData {
   otpauth_url: string
}const generateQRCode = async (secret: SecretData) => {
    return await toDataURL(secret.otpauth_url)
}
```

## 生成令牌

**如果你选择短信/电子邮件，那么请继续阅读。**

**如果没有，跳到下一步。**

生成 TOTP 令牌很简单，但是您可能需要进行一些自定义。您可以更改用于创建令牌的算法、时间步长、令牌的位数等。我将在这里包含这些选项的文档的直接链接[。因此，请确保您阅读了`speakeasy`文档，并咨询您的团队，找出适合您的正确行动方案。现在，我将向您展示该做些什么。](https://github.com/speakeasyjs/speakeasy#totp)

要生成令牌，请使用保存在数据库中的编码。使用`totp`构造函数传入秘密、编码和任何其他选项。

```
import { totp } from 'speakeasy'interface SecretData {
   base32: string
}const generateToken = (secret: SecretData) => {
  const token = totp({ secret: secret.base32, encoding: 'base32'})
  return token
}
```

接下来，使用邮件或 SMS 客户端将令牌发送给最终用户。终端用户收到令牌后，请对其进行验证。下一步将教你如何编写验证函数。

## 验证令牌

最后一步！恭喜你，你走到了这一步。此时，您应该有一个生成秘密的方法，以及一些为最终用户创建令牌的策略。

该步骤依赖于终端用户向服务器提供令牌。令牌通过短信或电子邮件发送，或在验证器应用程序中生成。使用传递给服务器的令牌和保存在数据库中的密码，验证最终用户。

与生成令牌类似，`speakeasy`提供了不同的选项来传递给`verify`方法。这里的是更多选项的`verify`文档的链接。

```
import { totp } from 'speakeasy'const verifyToken = (userToken: string, serverSecret: string) => {
    const verified = totp.verify({ 
         secret: serverSecret, 
         encoding: 'base32',
         token: userToken
        })return verified
}const enableTwoFactor = (
      verified: boolean,
      repo: Respository, 
      twoFactor: TwoFactorEntity) => {if (!twoFactor.enabled) {
        twoFactor.enabled = verified
     }repo.save(twoFactor)
}
```

在我的实现中，我使用了不同的方法来获取我的令牌实体。这里我没有包括检索。如果用户令牌验证了秘密，则`verify`方法将返回`true`，否则返回`false`。验证过程的结果将有助于针对用户启用 2FA。

`enableTwoFactor`函数使用我的数据库实体来启用 2FA，当且仅当，two factor 以前没有启用，这应该只发生一次。

在您验证之后和启用之前，有明显的工作要考虑。例如，如果一个用户没有被验证，并且`enabled`标志是`false`，会发生什么呢？这对用户来说是什么样的？服务器应该返回什么样的响应？所有这些都取决于你的应用，所以我让你来填补空白。

就是这样；你已经通过三个简单的步骤成功实现了 2FA。集成 2FA 最困难的部分是中间的所有小步骤，这取决于您的应用程序(例如，设置端点，集成您的电子邮件或短信服务，计算前端部分，等等。).一旦最终用户启用 2FA，对于每次后续登录，您的服务器将生成一个令牌，或者最终用户将查看 authenticator 应用程序，然后将该令牌输入到您的应用程序中。

我希望这篇文章让您对什么是 2FA 以及如何在您的应用程序中实现它有一个很好的了解。

*快乐编码！*