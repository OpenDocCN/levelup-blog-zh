# 非对称加密:签名与加密

> 原文：<https://levelup.gitconnected.com/asymmetric-cryptography-signing-vs-encrypting-6ef042024fd9>

![](img/516384a33b11bf671b773e39b49a4fcd.png)

*照片由 Unsplash 上的@hocza 拍摄*

当我们谈论加密时，我们需要谈论 Alice 和 Bob 在示例部分扮演的角色。我希望你不会对它们感到厌烦，因为我也会用它们作为例子。这里，Alice 和 Bob 将使用 RSA 作为非对称密钥，SHA256 作为哈希函数，OAEP 用于加密/解密，PSS 用于签名/验证，Base64 用于编码。

# 加密

爱丽丝想安全地给鲍勃发送一条重要的消息，而爱丽丝不想让除了鲍勃之外的任何人阅读这条消息。这可能是爱情告白，一个核代码，或者只是私人垃圾邮件。由于他们的亲密关系，爱丽丝和鲍勃交换了他们的公钥，很浪漫，不是吗？于是爱丽丝想到了创建一个小的 Go 程序来创建加密的消息发送给鲍勃**用鲍勃的公钥**加密消息。

```
import (
  "crypto/rand"
  "crypto/rsa"
  "crypto/sha256"
  "encoding/base64"
  "fmt"
)func encrypt(key *rsa.PublicKey, plainText string) (string, error) {
  cipherText, err := rsa.EncryptOAEP(sha256.New(), rand.Reader, key, []byte(plainText), nil) if err != nil {
    return "", fmt.Errorf("encrypt error: %w", err)
  } return base64.StdEncoding.EncodeToString(cipherText), nil
}
```

爱丽丝的加密信息看起来像这样:

```
fUYx0yO6gkkCah9LmcX2e7puUkl0x4WsCl8UOajVG6sNse6ly6uGnXIXcRKY2R6khxHrPcvsuTaPK6b83QBgNmO0KU7C6kK2kYvah1/rkRK0WAiAfvA3Z+/i5CvUDJ2ZvbvCHjl9YH97qgUrXrZk7DrYMi+J8VIiF6h85ltLRBxAsTtE2zyYr5gZsWYBCp/NRV4i2kF5mBskbDMW6f/f6jm3jWl5zmaxcxF2NX14itK9VIoNUlFukx+5vR/y17ei7ClX4hgkF/Kdw8ruMpyxX74f9RpqK5KRHSjoJThOp2oDqdpK8r4T8wNGx/VfVcwRM8SyV+VMR91w37ppSCCm2E+XzZeFysKGG9Csbwgsh/KzsuJ3rZ30hYit0fDBqJ1PJTt3bNR05503xY7yaoUtQeDRzr+kfi0hdAYHZyiod/ZkUuphB7zYPS26Utn1synocQ82p1FlH8aAtSOREL9Pw9pNNNMi8Cq18Kcn0rmsjC+JFwlnEk5PkFY5ZLdSNMaXcwfh2kx6bH5d65GgRS1rbrRMBPwywkMmQgukjS9QN2R/GXqZlGeznrt/Pf4r0dV+ZLSgRPb0hSDRfEvjMBWLOvGFI/1dxx7AJhoGB/F9VveBHE6Ry5gMrgNs9Fr0cuMw8I651+GhpatwGVoX13WZaa5Q675RGaiVQaZW/W5bYrs=
```

当 Bob 收到来自 Alice 的加密消息时，Bob 希望立即阅读它。因此 Bob 创建了一个程序用他的私钥解密这个消息。

```
import (
  "crypto/rand"
  "crypto/rsa"
  "crypto/sha256"
  "encoding/base64"
  "fmt"
)func decrypt(key *rsa.PrivateKey, cipherText string) (string, error) {
  cipherBytes, err := base64.StdEncoding.DecodeString(cipherText) if err != nil {
    return "", fmt.Errorf("decode error: %w", err)
  }plainText, err := rsa.DecryptOAEP(sha256.New(), rand.Reader, key, cipherBytes, nil)if err != nil {
    return "", fmt.Errorf("decrypt error: %w", err)
  } return string(plainText), nil
}
```

最后，鲍勃能够阅读爱丽丝的信息。因为 **Bob 把他的私钥留给了自己，所以别人无法解密 Alice 的消息**包括我在内，所以我不能在这篇博文里把明文消息给你看。抱歉伙计们。

# 签署

收到爱丽丝的信息后，鲍勃非常高兴。鲍勃想回复爱丽丝的信息。但是鲍勃忘记把爱丽丝的公钥放在哪里了。鲍勃想宣布一个回复，让每个人都知道他收到爱丽丝的信息后有多高兴。但问题是， **Bob 怎么能保证回复不被别人修改，又能保证宣布回复的 Bob**。Bob 开始创建一个 Go 程序**用他的私钥签署消息，这样每个拥有 Bob 公钥的人都可以验证回复是由 Bob 宣布的**包括 Alice。

```
import (
  "crypto"
  "crypto/rand"
  "crypto/rsa"
  "crypto/sha256"
  "encoding/base64"
  "fmt"
)func sign(key *rsa.PrivateKey, plainText string) (string, error) {
  hash := sha256.New()
  hash.Write([]byte(plainText))
  digest := hash.Sum(nil) signature, err := rsa.SignPSS(rand.Reader, key, crypto.SHA256, digest, nil) if err != nil {
    return "", fmt.Errorf("sign error: %w", err)
  } return base64.StdEncoding.EncodeToString(signature), nil
}
```

鲍勃的回复是这样的:

```
Dear Alice, I'm so happy hearing that from you. Sincerely yours, Bob.
```

鲍勃的签名看起来像这样:

```
Jsl61bOJIzBs+Ccv8jkrVIeyCBPEU9Ps75ig/GnrP7aUncD3BbIP6AJ8x2jR0UK7aGSE6M/MhRN8zWIgwz8qthcACrec+fz99TGF9CRPn+R9cMezdOzZEMT00unO9u6DppNlQeNHLiCEVfVzvZrRP3GnLBYUzmFNM7LdySbwWmeUE/uOWYQT86FM0i1Tr4DXaVJwyVkIURgRIcmqFAYovQM4m+9Br93+SpnPmEiA4P8eWZ8E+Y5qzA4Hv0HXUHYLnGKUVVsVNhM4o8iL7CVgHr5Fd5JWCmGQPbrNUIOzGRiYOV0BQi/uDRkOW0yGbHtjPHjcuykXeHjgAFE1vVZCT1HwMdsJNOKruuXxoeD43UaoJ/h9ac+8sPKwuWEV476oN2Pm9df0E/JRytGYU7/7MDjs2yEuShhKjGWfj2gWCgJzTbx4IVYs+lwmfcODTkM5b4T+CjINzRXFX73INNWP67g3KxgL4k/3ys7i64HIn3ApMli8aZEvAwjkWyh9JHN7xAeE1TtMN3K3zKXqpRNyfg98kazsV7ViOdP7+oGap9z+22B2SIXgUC4B36UBhk+0chcKJv8fFkowQS0lNLwLM1kRwx69SBEgQpy2KV1ia6X81Q3twEz0nQSiy0iJ5/fN7Wllh+F088SLuyOLo7uK1Ieh+DKJda9R+BsgMC+xBG8=
```

阅读公告**的 Alice 想要确保该消息没有被 Bob** 修改和发送。于是爱丽丝**用签名和鲍勃的公钥**验证了这条消息。

```
import (
  "crypto"
  "crypto/rand"
  "crypto/rsa"
  "crypto/sha256"
  "encoding/base64"
  "fmt"
)func verify(key *rsa.PublicKey, message, signature string) (bool, error) {
  hash := sha256.New()
  hash.Write([]byte(message))
  digest := hash.Sum(nil) signatureBytes, err := base64.StdEncoding.DecodeString(signature) if err != nil {
    return false, fmt.Errorf("decode error: %w", err)
  } err = rsa.VerifyPSS(key, crypto.SHA256, digest, signatureBytes, nil) return err == nil, err
}
```

而且核实是 Bob 公布的回复！总结一下，`Encrypt`是你在`you don't want anyone to read the message except the recipient`时要用的方法，而`Sign`是你在`you want to make sure the message hasn't been changed and it can be verified that only you who sent it`时要用的方法

你可以在这里阅读全部 Golang 代码:

```
package mainimport (
  "crypto"
  "crypto/rand"
  "crypto/rsa"
  "crypto/sha256"
  "encoding/base64"
  "fmt"
)func hash(message string) []byte {
  hash := sha256.New()
  hash.Write([]byte(message))
  return hash.Sum(nil)
}func encode(message []byte) string {
  return base64.StdEncoding.EncodeToString(message)
}func decode(message string) ([]byte, error) {
  return base64.StdEncoding.DecodeString(message)
}func encrypt(publicKey *rsa.PublicKey, plainText string) (string, error) {
  cipherText, err := rsa.EncryptOAEP(sha256.New(), rand.Reader, publicKey, []byte(plainText), nil) if err != nil {
    return "", fmt.Errorf("encrypt error: %w", err)
  } return encode(cipherText), nil
}func decrypt(key *rsa.PrivateKey, cipherText string) (string, error) {
  cipherBytes, err := decode(cipherText) if err != nil {
    return "", fmt.Errorf("decode error: %w", err)
  } plainText, err := rsa.DecryptOAEP(sha256.New(), rand.Reader, key, cipherBytes, nil) if err != nil {
    return "", fmt.Errorf("decrypt error: %w", err)
  } return string(plainText), nil
}func sign(key *rsa.PrivateKey, plainText string) (string, error) {
  digest := hash(plainText) signature, err := rsa.SignPSS(rand.Reader, key, crypto.SHA256, digest, nil) if err != nil {
    return "", fmt.Errorf("sign error: %w", err)
  } return encode(signature), nil
}func verify(key *rsa.PublicKey, message, signature string) (bool, error) {
  digest := hash(message) signatureBytes, err := decode(signature) if err != nil {
    return false, fmt.Errorf("decode error: %w", err)
  } err = rsa.VerifyPSS(key, crypto.SHA256, digest, signatureBytes, nil) return err == nil, err
}func getBobKey() (*rsa.PrivateKey, *rsa.PublicKey) {
  privateKey, _ := rsa.GenerateKey(rand.Reader, 4096)
  return privateKey, &privateKey.PublicKey
}func main() {
  bobPriv, bobPub := getBobKey()
  cipher, _ := encrypt(bobPub, "Dear Bob, <REDACTED>")
  plain, _ := decrypt(bobPriv, cipher)
  fmt.Println("cipher text from Alice:", cipher)
  fmt.Println("plain text from Alice:", plain) bobReply := `Dear Alice,
I'm so happy hearing that from you.
Sincerely yours, Bob.` replySignature, _ := sign(bobPriv, bobReply)
  fmt.Println("reply from Bob:", bobReply)
  fmt.Println("reply signature from Bob:", replySignature) isVerified, _ := verify(bobPub, bobReply, replySignature)
  fmt.Println("Was it Bob who sent the message?", isVerified)
}
```

感谢您的阅读！

*最初发布于*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/asymmetric-cryptography-signing-vs-encrypting/)*。*