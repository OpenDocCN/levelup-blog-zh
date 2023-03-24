# 网络安全:什么是 JWT？

> 原文：<https://levelup.gitconnected.com/web-security-what-is-a-jwt-a2598ff46c58>

![](img/6fac7621070fec58a0038cb4ee5c50ab.png)

杰斯温·托马斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JSON Web 令牌(jwt)是一种安全令牌，它使用 JSON 来编码关于实体的信息。它们用于授权请求和访问敏感数据。在这篇博文中，我们将讨论 jwt 是什么，以及如何使用它们来提高 web 安全性。我们还将提供如何实现 jwt 的例子。

## 编码、加密和散列信息

为了理解 jwt 如何工作，首先理解编码、加密和散列的概念是很重要的。

编码是将数据转换成易于阅读的格式的过程。加密是将数据转换成只有拥有私钥的人才能读取的格式的过程。私钥是将加密数据转换回其原始格式所必需的。哈希是将数据转换为不可逆的固定长度值的过程。哈希是传输数据最安全的技术，因为它不可逆转。

jwt 使用编码和散列来传输信息。散列是 JWTs 的关键部分，所以我们需要完全理解散列函数是如何操作的。

## 哈希函数的工作原理

哈希函数是一种数学算法，可以将任意大小的数据转换为哈希值。放入散列函数的相同输入/数据将总是产生相同的散列值。哈希函数是一种单向操作，这意味着不可能从其哈希值反转数据。这就是哈希如此安全的原因。

## JWTs 的实际使用案例

既然我们已经了解了 jwt 的基本工作原理，那么让我们来讨论一些实际的用例。

jwt 可以用来认证用户。当用户想要登录应用程序时，他们将提交他们的凭证(用户名和密码)。用户凭证的组合与只有应用程序知道的密钥配对。然后对这个配对进行哈希处理，得到的哈希值被用作用户的令牌。

JWT 将被发送给用户并存储在他们的浏览器中。当用户向应用程序发出请求时，他们会在请求中包含 JWT。然后，应用程序将解码并验证 JWT。如果 JWT 有效，用户将被授权访问所请求的资源。

## JWT 的结构

JWT 由三部分组成:报头、有效载荷和签名。这三部分由句点('.'分隔).

报头包含关于如何编码 JWT 的信息。哈希算法与 JWT 类型一起存储在报头中。

有效负载是存储数据的地方。这可能是关于用户的信息，例如他们的姓名、电子邮件地址等等。

该签名用于验证 JWT 未被篡改。报头和有效载荷被编码并与句点组合。然后，签名使用报头中定义的散列算法、密钥、编码的报头和有效载荷来创建散列值，该散列值将代表签名。

由于传递给哈希算法的相同数据将始终产生相同的哈希值，因此签名可用于验证数据未被篡改。

![](img/17fd6451d6bbd05eb609f4e3458e3402.png)

约翰·施诺布里奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## JWT 的例子

现在我们已经了解了 jwt 工作的基本原理，让我们看一个例子。

```
Encoded JWT:
eyJhbGciOiJIUzIcCIOPSUzI.eyJpcQiOjFwLCJuYmYiOjE0NDQ0Nzg0MDBafQ.WyJleHAiOjE0NDQ0Nzg0MDAsImlhdCI-c0NDQzg0MDBdHeader:
{
  “alg”: “HS256”,
  “typ”: “JWT”
}Payload:
{
  “sub” : “1234567890”,
  “name” : “John Doe”,
  “admin”: true
}Signature:
HMACSHA256(
  base64UrlEncode(header) + “.” +
  base64UrlEncode(payload),
  secretKey
)
```

编码 JWT 中的报头是第一周期之前的部分，有效载荷是第一和第二周期之间的部分。头和负载都是 JSON 对象。标头包含有关使用的哈希算法(HS256)和 JWT 类型(JWT)的信息。有效负载包含有关用户的信息，例如他们的姓名以及他们是否是管理员。

签名是第二个句点之后的部分。编码的 JWT 是 base64url 编码头和负载，然后用句点('.'连接它们的结果)之间。签名是通过将编码的报头和有效负载以及密钥传递给哈希算法来生成的。

这个密钥只有应用程序知道。生成的签名可用于检查数据是否被篡改，这提供了高级别的安全性。

## 结论

jwt 是认证用户和保护数据的一种安全有效的方法。它们由报头、有效载荷和签名组成。编码报头和有效载荷以及秘密密钥的组合产生签名。该签名可用于验证数据未被篡改。

希望这篇文章教会你一些东西！祝你的编码面试好运！

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)