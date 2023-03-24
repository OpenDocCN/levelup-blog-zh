# 使用节点 JS 派生、签名和验证 JWT (JSON Web 令牌)

> 原文：<https://levelup.gitconnected.com/deriving-signing-and-verifying-a-jwt-json-web-token-with-node-js-f3d0d12b1fc9>

![](img/1dd3971ba606c105218a79626df6c39c.png)

# JSON Web 令牌的组件(JWT)

在最基本的层面上，JSON Web 令牌(JWT)只是包含用户信息的一小段数据。它包含三个部分:

1.  页眉
2.  有效载荷
3.  签名

每个部分都以 Base64url 格式编码(比 JSON 对象更容易通过 HTTP 协议传输)。

这里有一个 JWT 的例子:

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0.POstGetfAytaZS82wHcjoTyoqhMyxXiWdR7Nn7A29DNSl0EiXLdwJ6xC6AfgZWF1bOsS_TuYI3OG85AmiExREkrS6tDfTQ2B3WXlrr-wp5AokiRbz3_oB4OxG-W9KcEEbDRcZc0nH3L7LzYptiy1PtAylQGxHTWZXtGz4ht0bAecBgmpdgXMguEIcoqPJ1n3pIWk_dUZegpqx0Lka21H6XxUTxiy8OcaarA8zdnPUnV6AmNP3ecFawIFYdvJB_cm-GvpCSbr8G8y_Mllj8f4x9nBH8pQux89_6gUY618iYv7tuPWBFfEbLxtF2pZS6YC1aSfLQxeNe8djT9YjpvRZA
```

请注意这段文字中的句号`.`。这些句点将报头、有效载荷和签名分开。让我们分离出头部:

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9
```

现在，让我们安装 NodeJS `base64url`库并对此进行解码。

```
npm install --save base64url# I am running this from Node consoleconst base64 = require('base64url');const headerInBase64UrlFormat = 'eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9';const decoded = base64.decode(headerInBase64UrlFormat);console.log(decoded);
```

如果我们如上所示解码这个头，它将给出下面的 **JSON** 对象(因此得名“JSON”Web 令牌):

```
{
    "alg":"RS256",
    "typ":"JWT"
}
```

我们稍后将了解这意味着什么，但是现在，让我们使用相同的方法解码有效载荷和签名。

上述代码的结果将是:

```
# Header
{
    "alg":"RS256",
    "typ":"JWT"
}# Payload
{
    "sub":"1234567890",
    "name":"John Doe",
    "admin":true,
    "iat":1516239022
}# Signature
Lots of gibberish like - ��e 宿���(�$[����4\e�'
```

现在，忽略 JWT 的签名部分。它不能被解码成有意义的 JSON 对象的原因是因为它比头部和有效载荷稍微复杂一些。我们将很快对此进行进一步的探索。

让我们看一下报头和有效载荷。

标题同时具有一个`alg`和`typ`属性。这两个都在 JWT，因为它们代表了解释那个乱糟糟的签名的“指令”。

有效负载是最简单的部分，它只是我们正在验证的用户的信息。

*   `sub`——“主题”的缩写，通常代表数据库中的用户 ID
*   `name` -只是一些关于用户的任意元数据
*   `admin` -更多关于用户的任意元数据
*   `iat`—“发行时间”的缩写，代表本 JWT 的发行时间

使用 jwt，您可能还会在有效负载中看到以下信息:

*   `exp`—“到期时间”的缩写，表示该 JWT 到期的时间
*   `iss` -“发行者”的缩写，通常在中央登录服务器发行许多 JWT 令牌时使用(在 OAuth 协议中也大量使用)

你可以在这个链接看到 JWT 规范[的所有“标准声明”。](https://tools.ietf.org/html/rfc7519#section-4.1)

# 逐步创建签名

虽然我告诉过你不要担心我们在试图解码 JWT 的`signature`部分时收到的乱码，但我确信它仍然很烦人。在本节中，我们将学习它是如何工作的，但是首先**，你需要阅读[我写的这篇文章，这篇文章解释了公钥加密是如何工作的](https://medium.com/@zach.gollwitzer/whats-the-point-of-public-key-cryptography-and-how-does-it-work-bd1ab46550ed)(根据你对这个主题的熟悉程度，应该需要 10-20 分钟)。**

**即使你对题目很熟悉，你也应该略读文章。如果您对公钥密码学没有扎实的理解，这一节将毫无意义。**

**不管怎样…**

**JWT 的签名实际上是“T1”和“T2”的组合。它是这样创建的(下面是伪代码):**

```
// NOTE: This is pseudocode!!// Copied from the original JWT we are using as an example aboveconst base64UrlHeader = 'eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9';const base64UrlPayload = 'eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0';// We take a one-way hash of the header and payload using the SHA256 hashing algorithm.  We know to use this algorithm because it was specified in the JWT headerconst hashedData = sha256hashFunction(base64UrlHeader + '.' + base64UrlPayload);// The issuer (in our case, it will be the Express server) will sign the hashed data with its private keyconst encryptedData = encryptFunction(issuer_priv_key, hashedData);const finalSignature = convertToBase64UrlFunction(encryptedData);
```

**尽管`sha256hashFunction`、`encryptFunction`和`convertToBase64UrlFunction`是由伪代码组成的，上面的例子解释了创建签名的过程。**

**现在，让我们使用 NodeJS `crypto`库来实际实现上面的伪代码。下面是我用来生成这个示例 JWT 的公钥和私钥(我们将需要它们来创建和解码 JWT 的签名)。请注意，在正常情况下，您永远不会像这样共享私钥。**

```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnzyis1ZjfNB0bBgKFMSv
vkTtwlvBsaJq7S5wA+kzeVOVpVWwkWdVha4s38XM/pa/yr47av7+z3VTmvDRyAHc
aT92whREFpLv9cj5lTeJSibyr/Mrm/YtjCZVWgaOYIhwrXwKLqPr/11inWsAkfIy
tvHWTxZYEcXLgAXFuUuaS3uF9gEiNQwzGTU1v0FqkqTBr4B8nW3HCN47XUu0t8Y0
e+lf4s4OxQawWD79J9/5d3Ry0vbV3Am1FtGJiJvOwRsIfVChDpYStTcHTCMqtvWb
V6L11BWkpzGXSW4Hv43qa+GSYOD2QU68Mb59oSk2OB+BtOLpJofmbGEGgvmwyCI9
MwIDAQAB
-----END PUBLIC KEY----------BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAnzyis1ZjfNB0bBgKFMSvvkTtwlvBsaJq7S5wA+kzeVOVpVWw
kWdVha4s38XM/pa/yr47av7+z3VTmvDRyAHcaT92whREFpLv9cj5lTeJSibyr/Mr
m/YtjCZVWgaOYIhwrXwKLqPr/11inWsAkfIytvHWTxZYEcXLgAXFuUuaS3uF9gEi
NQwzGTU1v0FqkqTBr4B8nW3HCN47XUu0t8Y0e+lf4s4OxQawWD79J9/5d3Ry0vbV
3Am1FtGJiJvOwRsIfVChDpYStTcHTCMqtvWbV6L11BWkpzGXSW4Hv43qa+GSYOD2
QU68Mb59oSk2OB+BtOLpJofmbGEGgvmwyCI9MwIDAQABAoIBACiARq2wkltjtcjs
kFvZ7w1JAORHbEufEO1Eu27zOIlqbgyAcAl7q+/1bip4Z/x1IVES84/yTaM8p0go
amMhvgry/mS8vNi1BN2SAZEnb/7xSxbflb70bX9RHLJqKnp5GZe2jexw+wyXlwaM
+bclUCrh9e1ltH7IvUrRrQnFJfh+is1fRon9Co9Li0GwoN0x0byrrngU8Ak3Y6D9
D8GjQA4Elm94ST3izJv8iCOLSDBmzsPsXfcCUZfmTfZ5DbUDMbMxRnSo3nQeoKGC
0Lj9FkWcfmLcpGlSXTO+Ww1L7EGq+PT3NtRae1FZPwjddQ1/4V905kyQFLamAA5Y
lSpE2wkCgYEAy1OPLQcZt4NQnQzPz2SBJqQN2P5u3vXl+zNVKP8w4eBv0vWuJJF+
hkGNnSxXQrTkvDOIUddSKOzHHgSg4nY6K02ecyT0PPm/UZvtRpWrnBjcEVtHEJNp
bU9pLD5iZ0J9sbzPU/LxPmuAP2Bs8JmTn6aFRspFrP7W0s1Nmk2jsm0CgYEAyH0X
+jpoqxj4efZfkUrg5GbSEhf+dZglf0tTOA5bVg8IYwtmNk/pniLG/zI7c+GlTc9B
BwfMr59EzBq/eFMI7+LgXaVUsM/sS4Ry+yeK6SJx/otIMWtDfqxsLD8CPMCRvecC
2Pip4uSgrl0MOebl9XKp57GoaUWRWRHqwV4Y6h8CgYAZhI4mh4qZtnhKjY4TKDjx
QYufXSdLAi9v3FxmvchDwOgn4L+PRVdMwDNms2bsL0m5uPn104EzM6w1vzz1zwKz
5pTpPI0OjgWN13Tq8+PKvm/4Ga2MjgOgPWQkslulO/oMcXbPwWC3hcRdr9tcQtn9
Imf9n2spL/6EDFId+Hp/7QKBgAqlWdiXsWckdE1Fn91/NGHsc8syKvjjk1onDcw0
NvVi5vcba9oGdElJX3e9mxqUKMrw7msJJv1MX8LWyMQC5L6YNYHDfbPF1q5L4i8j
8mRex97UVokJQRRA452V2vCO6S5ETgpnad36de3MUxHgCOX3qL382Qx9/THVmbma
3YfRAoGAUxL/Eu5yvMK8SAt/dJK6FedngcM3JEFNplmtLYVLWhkIlNRGDwkg3I5K
y18Ae9n7dHVueyslrb6weq7dTkYDi3iOYRW8HRkIQh06wEdbxt0shTzAJvvCQfrB
jg/3747WSsf/zBTcHihTRBdAv6OmdhV4/dD5YBfLAkLrd+mX7iE=
-----END RSA PRIVATE KEY-----
```

**首先，让我们创建头部和有效载荷。为此我将使用`base64url`库，所以请确保您已经安装了它。**

**createJWT.js**

**嘣！您刚刚创建了 JWT 的前两个部分。现在，让我们将签名的创建添加到这个脚本中。为此，我们将需要内置的 NodeJS `crypto`库和私钥。**

**createJWT.js**

**在上面的代码中，我重复了之前运行的脚本，并添加了创建签名的逻辑。在这段代码中，我们首先通过一个`.`字符将头和有效负载(base64url 编码)附加在一起。然后，我们将这些内容写入我们的签名函数，这是内置的 NodeJS 加密库的`RSA-SHA256`签名类。虽然听起来很复杂，但这一切告诉我们的是:**

1.  **使用 RSA 标准 4096 位公共/私有密钥对**
2.  **对于`base64Url(header) + '.' + base64Url(payload)`的散列，使用`SHA256`散列算法。**

**在 JWT 标题中，您会注意到这是由`RS256`表示的，这只是`RSA-SHA256`的一种缩写方式。**

**一旦我们将内容写入这个函数，我们需要从一个文件中读取将要签名的私钥。我已经将本文前面显示的私钥存储在一个名为`id_rsa_priv.pem`的文件中，该文件位于当前工作目录中，以`.pem`格式存储(相当标准)。**

**接下来，我将对数据进行“签名”,首先用`SHA256`散列函数对数据进行散列，然后用私钥对结果进行加密。**

**最后，由于 NodeJS 加密库以`Base64`格式返回我们的值，我们需要使用`base64Url`库将其从 Base64 转换为 Base64Url。**

**一旦完成，你将有一个 JWT 标题，有效载荷和签名，匹配我们原来的 JWT 完美！**

# **逐步验证签名**

**在上一节中，我们看了如何创建 JWT 签名。在用户身份验证中，流程如下所示:**

1.  **服务器接收登录凭证(用户名、密码)**
2.  **服务器执行一些逻辑来验证这些凭证是有效的**
3.  **如果凭证是有效的，服务器发布并*签署*一个 JWT 并将其返回给用户**
4.  **用户使用颁发的 JWT 在浏览器中验证未来的请求**

**但是，当用户向应用程序的受保护路由或受保护 API 端点发出另一个请求时，会发生什么呢？**

**您的用户向服务器提供了一个 JWT 令牌，但是您的服务器如何解释这个令牌并决定用户是否有效呢？以下是基本步骤。**

1.  **服务器收到一个 JWT 令牌**
2.  **服务器首先检查 JWT 令牌是否过期，以及过期日期是否已过。如果是，服务器拒绝访问。**
3.  **如果 JWT 没有过期，服务器将首先把`header`和`payload`从 Base64Url 转换成 JSON 格式。**
4.  **服务器在 JWT 的`header`中寻找解密签名所需的散列函数和加密算法(我们假设在这个例子中，JWT 使用`RSA-SHA256`作为算法。**
5.  **服务器使用一个`SHA256`散列函数来散列`base64Url(header) + '.' + base64Url(payload)`，这给服务器留下一个散列值。**
6.  **服务器使用存储在其文件系统中的`Public Key`来解密`base64Url(signature)`(记住，私钥加密，公钥解密)。因为服务器既创建签名又验证签名，所以它应该在文件系统中存储公钥和私钥。对于较大的用例，通常会将这些任务分离到完全独立的机器上。**
7.  **服务器比较步骤 5 和步骤 6 的值。如果它们匹配，这个 JWT 就是有效的。**
8.  **如果 JWT 有效，服务器将使用`payload`数据来获取用户的更多信息，并对该用户进行身份验证。**

**使用我们在这篇文章中一直使用的**相同的 JWT** ，下面是这个过程在代码中的样子:**

**verifyJWT.js**

**这段代码中有几项值得注意。首先，我们把 Base64Url 编码的 JWT 分成 3 部分。然后我们使用内置的 NodeJS `createVerify`函数创建一个新的`Verify`类。就像创建签名的过程一样，我们需要将`base64url(header) + '.' + base64url(payload)`传递给`Verify`加密类使用的流。**

**下一步很关键——您需要将`jwtSignature`从其默认编码 Base64Url 转换为> Base64。然后，您需要传递公钥，即签名的 Base64 版本，并向 NodeJS 表明您正在使用 Base64。如果你不指定编码，它将默认为一个缓冲区，你将总是得到一个错误的返回值。**

**如果一切顺利，你应该得到一个真的返回值，这意味着这个签名是有效的！**

# **缩小:JWT 签名的真正价值**

**如果你阅读了上面的两节，你就知道如何使用`RSA-SHA256` JWT 算法来签署和验证 JWT 签名(其他算法的工作非常相似，但是这个算法被认为是更安全和“生产就绪”的算法之一)。**

**但这一切意味着什么呢？**

**如果您考虑其他身份验证方法(例如，使用 Cookies 和会话对用户进行身份验证)，您会知道为了这样做，您的应用程序服务器必须有一个跟踪会话的数据库，并且每当用户想要访问服务器上受保护的资源时，都必须调用该数据库。**

**使用 JWT 认证，唯一需要验证用户是否通过认证的是一个公钥！！**

**一旦颁发了 JWT 令牌(由您的应用程序服务器、身份验证服务器甚至第三方身份验证服务器颁发)，该 JWT 就可以安全地存储在浏览器中，并可用于验证任何请求，而无需使用任何数据库。应用服务器只需要发行者的公钥！**

**如果你推断这个概念，并思考 JWT 的更广泛的含义，就会清楚它是多么强大。您不再需要本地数据库。您可以在整个 web 上传输身份验证！**

**比方说，我登录到一个流行的服务，如谷歌，我从谷歌的认证服务器收到一个 JWT 令牌。唯一需要验证我正在浏览的 JWT 的是与 Google 签名的私钥相匹配的公钥。通常，这个公钥是*公开*可用的，这意味着互联网上的任何人都可以验证我的 JWT！如果他们信任谷歌，并且他们相信谷歌正在提供正确的公钥，那么我没有理由不使用谷歌发布的 JWT 来认证进入我的应用程序的用户。**

**我知道我说过我们不会在这篇文章中涉及所有 OAuth 的东西，但这是委托认证的本质(即 OAuth2.0 协议、OpenIDConnect、访问令牌、授权令牌等。)!**

# **更简单的解决方案**

**到目前为止，我们已经了解了解构一个 JWT，用 NodeJS `crypto`库签名它，并用 NodeJS `crypto`库验证它的完整过程。**

**但是，如果你可能已经注意到，我们必须写很多代码来做到这一点！此外，我们还必须在各种格式(JSON、base64、base64urlencoded)之间进行转换。**

**难道在应用程序中没有更简单的方法来做到这一点吗？**

**要理解 jwt，使用 NodeJS `crypto`库是一个很好的工具。在实践中，我们想要抽象出很多细节的东西。这就是为什么在大多数情况下，您将使用`jsonwebtoken` NPM 库来签署和验证 jwt。此外，如果您使用 Passport JS 框架中的`passport-jwt`策略(参见本文)，您需要编写的代码将会更少！**

**下面，我写了和上面我们用 NodeJS `crypto`库做的完全一样的东西，但是使用了`jsonwebtoken`库。看一看，我们将审查它。**

**signandverifywithjsonwebtoken . js**

**有了 [jsonwebtoken 库](https://www.npmjs.com/package/jsonwebtoken)，您不需要对 base64/base64urlencoded 编码或向 NodeJS 流传递数据来签署数据感到着迷。你所要做的就是传递带有几个参数的`jsonwebtoken.sign()`和`jsonwebtoken.verify()`方法，你就完成了！**

**通常，一个教程从`jsonwebtoken`库开始，并试图解释其内部。希望从内部开始，您可以更清晰地理解这个通用语法。如果你有任何问题，请在下面的评论中提出！**