# 使用 Python 中的 AES128 位加密，在 5 分钟内保护您的数据

> 原文：<https://levelup.gitconnected.com/coding-aes128-bit-encryption-in-python-in-less-than-5-minutes-f6bcbddd2b82>

使用 Fernet 密码快速实现对称密钥加密，在 Python 的密码库中。

![](img/fb299735c6d33d683ab36e2dd726ad88.png)

这就是你忘记关方向锁的后果。马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**对称密钥加密**是最简单的消息加密形式之一，发送方和接收方使用完全相同的密钥进行加密和解密。想要读取密文的各方都可以安全地共享密钥，他们通过将密文加载到用于编码密文的算法中来解密密文。这里，我们将使用 AES128 位 [Fernet](https://cryptography.io/en/latest/fernet/#) 密码和 Python 的[密码术](https://cryptography.io/en/latest/installation/)库。

> 密码学中的“密钥”或“秘密”只是由密码方法(加密算法)生成的一串字节，一端用于加密消息，另一端用于解密消息。想象一下，例如:“我将把这个消息中的每个字母移动一位，移动到字母表中的下一个字母，所以当你得到这个密文时，将每个字母移动一位，移动到前一个字母，以显示我原始的、编码的消息。”
> 
> 试着用我刚才描述的密钥破译这个秘密消息:Dbo zpv sfbe uijt？Cf tvsf up esjol zprs pwbmujof。

例如，计算机生成的密钥文件内部通常是这样的:

`s_g6nE4J-nKktINfrO2b8qyX1H7cpfsqyMnxrWoVSQU=`

只要你和另一个人有相同的字节串，你就能把一条信息加密成一串难以辨认的字母和数字，没有密钥，没有人能破译你写的东西，或者至少有一些量子计算能力供他们使用。

与**不对称加密**不同，在不对称加密中，每个人都有自己唯一的私钥和公开共享的公钥，对称加密简单、直接且易于管理，适用于基本项目和各种加密需求。棘手的部分是安全地共享密钥而不暴露给史努比探听者。

如果你刚刚开始，想快速、轻松地掌握一些质量不错的小消息加密技术，请继续阅读。

# 第一部分。在加密库中进行设置

**第一步:**安装密码术。详细说明请点击[这里](https://cryptography.io/en/latest/installation/)。否则，只需从命令行开始:

```
pip install cryptography # or pip3
```

**第二步:**将你的库放在顶部，导入到一个新的 python 文件中。建议:不想想名字就叫 *fernet_encryption.py* 好了。

```
# Import the Fernet class. 
from cryptography.fernet import Fernet
```

# 第 2 部分:加密秘密消息

现在，要产生一个秘密信息，我们需要一些东西。

1.  生成一个新密钥。
2.  创建要编码的消息。
3.  用密钥加密消息，并将其存储为密文变量。
4.  将该消息传递给接收者。

## 1.生成密钥:

首先，我们将生成密钥，然后将它写入一个. key 文件。我们现在将使用 python 文件所在的文件夹，但是您可以选择任何路径。

```
# Use Fernet to generate the key file.
key = Fernet.generate_key() # Store the file to disk to be accessed for en/de:crypting later.
with open('secret.key', 'wb') as new_key_file:
    new_key_file.write(key)print(key)>>> b'G5mX1vlxKVaQkdg3CfhH6pVQIctECVw3MN6uCXbJpGo='
```

> **编码类型:**消息前面的‘b’表示它被存储为字节。编码类型是存储相同数据的不同方式。在这里，这是一个“UTF-8”类型的编码，这些是用来编码的字符。这是一个更高级的主题，如果您对密码学非常深入，您将需要在不同的类型之间来回转换，如“base64”或“hex ”,但对于本简介，甚至不用担心它。当你看到 b 的时候，只要想“那不是字符串，那是字节！”
> 
> Python 为我们提供了一种非常简单的方法，用。encode()和。decode()函数。如果您开始得到有趣的错误，确保您的字符串在需要时被编码(string.encode())，您的字节字符串消息在需要时被解码(bytes_msg.decode())。

## **2。创建要编码的消息:**

这是你的加密“hello world ”,所以随便选吧，没关系。我用的是这个:

```
msg = "Into the valley of death, rode the 600."# Encode this as bytes to feed into the algorithm.
# (Refer to **Encoding types** above).msg = msg.encode()
```

## 3.加密邮件:

使用刚刚创建的键，将一个`Fernet()`对象实例化为`f`。这告诉算法使用该密钥进行加密和解密。然后，把你的信息传进去，并作为加密信息储存起来`ciphertext.`

```
# Instantiate the object with your key.
f = Fernet(key)# Pass your bytes type message into encrypt.
ciphertext = f.encrypt(msg)print(ciphertext)
```

您应该会看到类似这样的内容:

```
b'gAAAAABfRyALC7N-3gxMZsGYMjZMZIegYgBca2ZNzjtyS--TNYCqBP10YsZTQnzOMlrLuuSUALkq9GnNb5BBZeHwuztqM7ir_Yh9hoXwsQH6ywbW7ehbgUIUNtmasBuj63vHD-EHNo9U'
```

女士们，先生们，这就是你们的密文。这是我的。你的会看起来不一样。为什么？因为它是通过将您的消息字符串转换成字节，输入到 Fernet 对象的`encrypt`方法中而创建的消息，该方法是用您的私钥创建的。你是怎么读的？你猜对了——费尔内的`decrypt`法。

# **第三部分。解密接收的消息。**

让我们假设您不在创建消息的同一个运行时上。因此，我们要加载密钥。如果密钥与 python 文件在同一个文件夹中，下面的代码会将密钥存储为一个变量，并使用它来解密新消息。该密钥必须与用于生成密文的密钥相同。

## 收件人的文件:

```
from cryptography.fernet import Fernet# Load the private key from a file.
with open('secret.key', 'rb') as my_private_key:
    key = my_private_key.read()# Instantiate Fernet on the recip system.
f = Fernet(key)# Decrypt the message.
cleartext = f.decrypt(ciphertext)# Decode the bytes back into a string.
cleartext = cleartext.decode()print(cleartext)
>>> Into the valley of death, rode the 600.
```

事实上就是这样。现在，您可以发送和接收使用 AES128 加密技术加密的消息，128 位加密技术非常可靠:

> “2017 年……世界上最强大的计算机仍然需要大约 885 万亿年来暴力破解 128 位 AES 密钥。”—[proprivacy.com](https://proprivacy.com/guides/aes-encryption)

具体来说，来自 Fernet [文档](https://cryptography.io/en/latest/fernet/#implementation):

> Fernet 建立在许多标准加密原语的基础上。具体来说，它使用:
> 
> `[AES](https://cryptography.io/en/latest/hazmat/primitives/symmetric-encryption/#cryptography.hazmat.primitives.ciphers.algorithms.AES)`在`[CBC](https://cryptography.io/en/latest/hazmat/primitives/symmetric-encryption/#cryptography.hazmat.primitives.ciphers.modes.CBC)`模式下用 128 位密钥进行加密；使用`[PKCS7](https://cryptography.io/en/latest/hazmat/primitives/padding/#cryptography.hazmat.primitives.padding.PKCS7)`填充。
> 
> `[HMAC](https://cryptography.io/en/latest/hazmat/primitives/mac/hmac/#cryptography.hazmat.primitives.hmac.HMAC)`使用`[SHA256](https://cryptography.io/en/latest/hazmat/primitives/cryptographic-hashes/#cryptography.hazmat.primitives.hashes.SHA256)`进行认证。
> 
> 使用`os.urandom()`生成初始化向量。

如果你想隐藏核秘密，咨询安全专家。但是，如果你只是想保持对所有明文数据的窥探，这是一个非常可靠、超级简单的选择。

# 潜得更深

为了进一步阅读，如果你想了解一些关于加密 n00bs 的好建议，请从 [Latacora 的加密权利答案](https://latacora.singles/2018/04/03/cryptographic-right-answers.html)开始。我将在以后的文章中介绍为 Python 推荐的 [PyNaCl](https://pypi.org/project/PyNaCl/) 库，但这应该能让你马上上手。

## 下载可用模块中的上述代码:

如果你想在你的代码中使用它，这里是一个类:[https://github . com/sachio 222/medium/blob/master/fernetcipher . py](https://github.com/sachio222/medium/blob/master/FernetCipher.py)

它包含基本相同的方法，只是排列方式不同，以便您可以在代码中轻松调用它们:

加密快乐！