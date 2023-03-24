# JavaScript window.crypto 对象指南

> 原文：<https://levelup.gitconnected.com/introducing-the-javascript-window-object-cryptography-7316d60fd1ef>

## 了解如何通过 JavaScript 在浏览器中使用加密功能

![](img/714719e2fb883cbe96f138efb24558dd.png)

[Franck V.](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`window`对象是一个全局对象，它提供了对 DOM 的 JavaScript 访问。它还包含一个标准函数库，我们可以在 web 应用程序的任何位置访问它。

在本文中，我们来看一下`window.cryoto`对象。

# windows . crypto

`window.crypto`属性返回一个与全局对象相关联的`Crypto`对象。该对象允许网页在浏览器端运行各种加密操作。它有一个属性，那就是`subtle`属性。

`Crypto.subtle`属性返回一个`SubtleCrypto`对象，它允许我们在客户端进行微妙的加密。`SubtleCrypto`对象有 5 种方法对数据进行加密和解密。`sign`方法用于创建数字签名。

存在一个`verify`方法来验证由`sign`方法创建的数字签名。

`encrypt`方法用于加密数据，`decrypt`方法用于解密由`encrypt`方法产生的加扰数据。`digest`方法用于创建一些数据的固定长度、抗冲突的摘要。

我们还可以使用`SubtleCrypto`对象分别通过`generateKey`和`deriveKey`方法生成和导出密钥。

`generateKey`方法在我们每次调用它的时候都会生成一个新的不同的键值，而`deriveKey`方法从一些初始的材料中派生出一个键。如果我们向两个不同的`deriveKey`调用提供相同的材料，我们将获得相同的潜在价值。

`deriveKey`方法对于导出相同的加密和解密密钥很有用。我们还可以使用`importKey`和`exportKey`方法分别导入和导出密钥。

还有一种`wrapKey`方法，导出密钥，然后用另一个密钥加密。

还提供了一个`unwrapKey`方法来解密由`wrapKey`方法完成的加密密钥，并导入解密的密钥。

例如，我们可以使用`sign`方法创建一个数字签名。它需要三个参数。第一个是`algorithm`，它是一个字符串或对象，指定用于创建数字签名的签名算法。可能的值有:

*   RSASSA-PKCS1-v1_5 —传入字符串 `“RSASSA-PKCS1-v1_5”`或形式为`{ “name”: “RSASSA-PKCS1-v1_5” }`的对象
*   RSA-PSS —传递一个`RsaPssParams` 对象。一个`RsaPssParams`对象具有`name`属性，它应该是`RSA-PSS`和`saltLength`，它是要使用的随机 salt 的长度，以字节为单位。`saltLength`的最大值为`Math.ceil((keySizeInBits - 1)/8) - digestSizeInBytes - 2`
*   ECDSA —传递一个`EcdsaParams` 对象。一个`EcdsaParams`对象具有`name`属性，该属性应该是字符串`'ECDSA'`和`hash`属性，该属性可以是具有可能值`SHA-256`、`SHA-384`或`SHA-512`的字符串
*   HMAC —传入字符串`“HMAC”`或形式为`{ “name”: “HMAC” }`的对象

第二个参数是`key`，它是一个 CryptoKey 对象，拥有用于创建签名的私钥。第三个参数是`data`，它是一个`ArrayBuffer` 或`ArrayBufferView` 对象，包含要签名的数据。

`sign`方法返回一个承诺，这个承诺通过一个有签名的`ArrayBuffer`对象来实现。

同样，`verify`方法接受与`sign`方法相同的第一个`algorithm`、`key`和`data`参数作为第一个、第二个和第四个参数。从`sign`方法生成的`signature`是第三个参数。它返回一个承诺，如果签名有效，则用值`true`实现，否则用值`false`实现。

为了使用`sign`和`verify`方法，我们可以编写类似下面的代码:

```
const enc = new TextEncoder();
const encodedMessage = enc.encode('hello');
const keyPair = window.crypto.subtle.generateKey({
    name: "RSASSA-PKCS1-v1_5",
    modulusLength: 4096,
    publicExponent: new Uint8Array([1, 0, 1]),
    hash: "SHA-256"
  },
  true,
  ["sign", "verify"]
);(async () => {
  const {
    privateKey,
    publicKey
  } = await keyPair;
  const signature = await window.crypto.subtle.sign(
    "RSASSA-PKCS1-v1_5",
    privateKey,
    encodedMessage
  );
  const signatureValid = await window.crypto.subtle.verify("RSASSA-PKCS1-v1_5", publicKey, signature, encodedMessage);
  console.log(signatureValid);
})()
```

我们首先用`generateKey`生成密钥对，因为我们使用的是非对称 RSA 算法有一个私有和公共密钥。`generateKey`方法将算法作为第一个参数，其中可能的值为:

*   为了使用 [RSASSA-PKCS1-v1_5](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/sign#RSASSA-PKCS1-v1_5) 、 [RSA-PSS](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/sign#RSA-PSS) 或 [RSA-OAEP](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt#RSA-OAEP) ，我们传递一个`[RsaHashedKeyGenParams](https://developer.mozilla.org/en-US/docs/Web/API/RsaHashedKeyGenParams)`对象。
*   为了使用 [ECDSA](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/sign#ECDSA) 或 [ECDH](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/deriveKey#ECDH) ，我们传递一个`[EcKeyGenParams](https://developer.mozilla.org/en-US/docs/Web/API/EcKeyGenParams)`对象。
*   为了使用 [HMAC](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/sign#HMAC) ，我们传递一个`[HmacKeyGenParams](https://developer.mozilla.org/en-US/docs/Web/API/HmacKeyGenParams)`对象。
*   为了使用 [AES-CTR](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt#AES-CTR) 、 [AES-CBC](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt#AES-CBC) 、 [AES-GCM](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt#AES-GCM) 或 [AES-KW](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/encrypt#AES-KW) ，我们传递一个`[AesKeyGenParams](https://developer.mozilla.org/en-US/docs/Web/API/AesKeyGenParams)`对象。

第二个参数是布尔`extractable`属性，它指示是否可以使用`SubtleCrypto.exportKey()`或`SubtleCrypto.wrapKey()`方法导出一个键。第三个参数是`keyUsages`数组，它指示哪些方法可以与生成的密钥一起使用:

*   `encrypt`
*   `decrypt`
*   `sign`
*   `verify`
*   `deriveKey`
*   `deriveBits`
*   `wrapKey`
*   `unwrapKey`

然后我们用算法名、私有密钥和从`TextEncoder`生成的编码消息调用`sign`方法。这将生成签名。然后，我们用算法名、私有密钥、从`sign`方法实现的生成签名和相同的编码消息调用`verify`方法。如果我们运行上面的代码，我们应该得到`console.log`日志`true`。

![](img/b58b30aaaf01f3536a0710bedad844d7.png)

马修·汉密尔顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`encrypt`方法有 3 个参数。第一个是`algorithm`，它是一个具有以下可能值的对象:

*   为了使用 RSA-OAEP 算法，我们传入一个`RsaOaepParams` 对象。该对象属性的完整详细信息位于[https://developer . Mozilla . org/en-US/docs/Web/API/rsoaepparams](https://developer.mozilla.org/en-US/docs/Web/API/RsaOaepParams)
*   为了使用 AES-CTR 算法，我们传入一个`AesCtrParams`对象。该对象属性的完整详细信息位于[https://developer . Mozilla . org/en-US/docs/Web/API/aesctparams](https://developer.mozilla.org/en-US/docs/Web/API/AesCtrParams)
*   为了使用 AES-CBC 算法，我们传入一个`AesCbcParams` 对象。该对象属性的全部细节位于[https://developer . Mozilla . org/en-US/docs/Web/API/AesCbcParams](https://developer.mozilla.org/en-US/docs/Web/API/AesCbcParams)
*   为了使用 AES-GCM 算法，我们传入一个`AesGcmParams` 对象。该对象属性的完整详细信息位于[https://developer . Mozilla . org/en-US/docs/Web/API/AesGcmParams](https://developer.mozilla.org/en-US/docs/Web/API/AesGcmParams)

第二个参数是我们用来加密的`CryptoKey`对象。第三个参数是一个`BufferSource`对象，它包含要加密的数据，也称为纯文本。它返回一个承诺，这个承诺通过一个包含加密纯文本的`ArrayBuffer`对象来解决。

同样，`decrypt`方法采用与`encrypt`方法相同的前两个参数，除了第三个参数是一个包含要解密的数据的`BufferSource`。它返回一个承诺，这个承诺是用具有纯文本的`ArrayBuffer`对象解决的。

例如，我们可以像下面的代码一样使用`encrypt`和`dercrypt`方法:

```
const enc = new TextEncoder();
const dec = new TextDecoder();
const keyPair = window.crypto.subtle.generateKey({
    name: "RSA-OAEP",
    modulusLength: 4096,
    publicExponent: new Uint8Array([1, 0, 1]),
    hash: "SHA-256"
  },
  true,
  ["encrypt", "decrypt"]
);
const encodedMessage = enc.encode('hello');
(async () => {
  const {
    privateKey,
    publicKey
  } = await keyPair;
  const encryptedText = await window.crypto.subtle.encrypt({
      name: "RSA-OAEP"
    },
    publicKey,
    encodedMessage
  )
  console.log(encryptedText); const decryptedText = await window.crypto.subtle.decrypt({
      name: "RSA-OAEP"
    },
    privateKey,
    encryptedText
  )
  console.log(decryptedText);
  console.log(dec.decode(decryptedText));})()
```

在上面的代码中，我们首先使用`generateKey`方法生成一个密钥或密钥对，这取决于加密算法是对称的还是非对称的，就像我们在`sign`和`verify`示例中所做的那样。非对称加密算法有一个公钥和一个私钥，如上例所示。RSA 是一种不对称算法。

然后我们用`TextEncoder`对消息进行编码，将其编码成一个`ArrayBuffer`对象，该对象可以与`encrypt`方法一起使用。

然后我们使用带有算法的`encrypt`方法、公钥和带有编码文本的`ArrayBuffer`对象(通过 int 传递)来加密数据。然后，为了解密加密的文本，我们使用将算法对象作为第一个参数传入的`decrypt`方法，然后我们从密钥对传入私钥，然后我们将加密的文本作为第三个参数传入到`decrypt`方法。

这将获得作为`ArrayBuffer`的解密数据，我们将使用`TextDecoder`的`decode`方法和解密的`ArrayBuffer`对其进行解码，以获得原始文本。这意味着最后一个`console.log`语句让我们回到`'hello'`。

`Crypto`对象也有一个方法，就是`getRandomValues`方法。给定一个类型化数组，该方法将创建一个强随机值。该方法接受一个参数。它接受一个类型化数组，可以是一个`Int8Array`、`Uint8Array`、`Int16Array`、`Uint16Array`、`Int32Array`或`Uint32Array`。为了提高性能，该方法不使用真正的随机数生成器来生成数字，而是使用伪随机数生成器来生成数字。传递给参数的类型化数组的条目将被该方法生成的随机数覆盖。

我们可以像下面的例子一样使用`getRandomValues`方法:

```
let array = new Uint32Array(10);
window.crypto.getRandomValues(array);for (const num of array) {
  console.log(num);
}
```

在上面的代码中，我们生成了一个新的`Uint32Array`，并将其传递给`getRandomValues`方法。然后在`for...of`循环中，我们得到覆盖了原始数组中所有条目的生成值。我们应该会看到来自`console.log`的 10 个随机数，每次运行上面的代码，都会得到不同的结果。

使用`window.cryoto`对象，我们可以在浏览器上使用众所周知的加密算法来加密和解密数据。它支持对称和非对称加密，让我们用不同的算法加密数据。此外，我们可以用它来生成数字签名并验证它们。我们也可以用它通过`getRandomValues`方法得到随机数。