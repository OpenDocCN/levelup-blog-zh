# Python:加盐你的密码散列

> 原文：<https://levelup.gitconnected.com/python-salting-your-password-hashes-3eb8ccb707f9>

## 提高您的哈希对密码破解的抵抗力

![](img/eca44b76eb084e7fe77a1f5ef3de4a6c.png)

图片由 [**cottonbro**](https://www.pexels.com/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 来自 [**Pexels**](https://www.pexels.com/photo/person-seasoning-green-beans-with-salt-3298782/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

在之前的[文章](https://blog.devgenius.io/password-hashing-with-python-f3148692e8b9)中，我分享了如何保护用户的密码。那篇文章假设散列用户的明文密码可以保护他们。

[](https://blog.devgenius.io/password-hashing-with-python-f3148692e8b9) [## 使用 Python 进行密码散列

### 即使用户密码被破解，也要保证其安全。

blog.devgenius.io](https://blog.devgenius.io/password-hashing-with-python-f3148692e8b9) 

嗯，那只是半个事实。散列密码并不能完全保护它们。简单地将明文密码转换成无形的、不可逆的字符串并不能完全保护它们。但是，与在数据库中以明文形式存储用户密码相比，哈希密码仍然是安全的。

散列密码是不可逆的，也是混乱的，但它们仍然容易受到暴力攻击和彩虹表攻击。

在暴力攻击中，攻击者从不同的计算机字符组合中生成散列，希望与数据库中的散列相匹配。

考虑在您的数据库中有一个包含相应的`sha256`哈希值的密码表。未经授权访问您的数据库并拥有包含密码`Harry03`和`tiPsy`的密码字典的攻击者将成功生成匹配的哈希。用户现在可以使用密码`Harry03`和`tiPsy`访问两个用户的数据

用户名和相应的 sha256 哈希表

在彩虹表攻击中，攻击者预先计算并生成一个明文密码及其相应哈希的表。攻击者接下来试图将他的表散列与存储在数据库中的散列进行匹配。预先计算的散列使得彩虹表攻击比暴力和字典攻击更快和更有效。但不要担心，我们会学习如何减轻这一点。

# 用盐强化密码散列

密码[盐](https://en.wikipedia.org/wiki/Salt_(cryptography))降低了密码被破解的风险。在您的密码中添加盐会使攻击者在生成匹配散列时更加复杂。要破解每个 salt 散列，他必须知道与明文密码一起散列的唯一 salt。只知道明文密码是不够的。

加盐密码散列降低了攻击者的成功率，无论攻击是预先计算的攻击还是暴力攻击。

salt 是添加到明文密码中的随机字符串，它们被哈希在一起以生成不可逆的哈希。不知道 salt 的攻击者不能生成匹配的散列。

在 Python 中， [hashlib](https://docs.python.org/3/library/hashlib.html) 模块提供了一个关键的衍生函数(KDF ),我们可以用它来实现这一点。KDF 是散列函数，通过使散列的生成在计算上缓慢，被设计为健壮的并且抵抗强力攻击。KDF 函数有一个成本因素，它决定了哈希的生成速度应该有多慢。更高的成本系数意味着哈希函数需要更长的时间和更多的 CPU 能力来生成哈希。成本因素所做的是，它延迟了攻击者试图破解您的密码散列的努力。在强力攻击中长时间生成散列可能会让攻击者望而却步，或者如果及时引起您的注意，至少会给您一个对攻击做出反应的机会。

> OWASP [将](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)salt 定义为唯一的随机字符串。每个密码都用一个唯一的随机 salt 字符串散列。这加倍了攻击者的努力。攻击者必须知道他试图破解每个密码的唯一 salt。

# hashlib.pbkdf2_hmac(hash_name，password，salt，iterations，dklen=None)

这个函数是基于密码的密钥派生函数 2(PBKDF2)的 Python 实现。这是一种强大而安全的 KDF 散列函数。我们将使用这个函数生成一个加盐散列密码。

这个函数的签名有 5 个参数，并返回生成的散列的摘要。

## 因素

`hash_name`第一个参数是用于生成哈希的哈希算法。SHA-2 算法作为参数被支持。

```
'sha224', 'sha384', 'sha512', 'sha256'
```

`password`第二个参数是明文密码，我们必须将它加盐并散列成无形的不可逆散列。需要编码的字节字符串。

`salt`第三个参数是添加到`password`中的唯一字符串，然后使用我们特定的哈希算法进行哈希处理，以生成健壮的哈希。该值也必须是编码的字节字符串。NIST [建议](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63b.pdf)salt 的长度必须至少为 32 字节或 256 位，唯一且可任意生成。与此一致，我们将使用 Python 的`os.urandom(...)`方法生成我们独特的任意 salt。

`iteration`第四个参数有时被称为成本系数或工作系数。简单来说，就是哈希算法被调用的次数。例如，迭代 3 意味着，指定的哈希函数将对`password`和`salt`进行哈希运算，随后对生成的哈希进行另一次哈希运算，然后对第二次生成的哈希进行第三次哈希运算。

第五个参数是可选的。它表示生成的摘要或哈希值的长度。如果没有提供，哈希的长度等于用于生成哈希的哈希函数的长度。

**让我们使用散列名称** `**sha256**` **为明文密码** `**hellow0rld**` **生成一个加盐散列。您可以使用我前面列出的任何哈希算法。**

*   首先，我们将使用`os.urandom`生成我们独特的任意销售，以符合 NIST 推荐的最小 salt 长度 32 字节

```
import ossalt = os.urandom(32)
```

生成一个随机的 32 字节长度的 salt，如下所示

```
\xa9\x84\x01\x96\xa4\t\xadP\x1e\xf3:\x94[\xb7\x9c=\xfebI\x03\xa2\x05\xd5\x9a\x19\x9b\xabhO\x13\xb8\x83
```

*   我们还将使用 Python 的`str`对象上的`encode()`方法将我们的明文或密码`hellow0rld`编码成字节。

```
plaintext = 'hellow0rld'.encode()
```

*   同样，对于迭代，我们将使用迭代计数`10000`，这是推荐的最小计数。根据您的服务器的计算能力以及您希望对用户的身份验证体验产生的明显影响，您可能需要增加这个值。过高的数字会降低攻击者执行暴力攻击的速度，但同样会降低用户的身份验证体验。因此，应该考虑合理的迭代次数。
*   我们将保持摘要长度为哈希算法的长度。
*   准备好所有的函数依赖项后，我们将使用哈希算法`sha256`生成一个哈希

```
import hashlibdigest = hashlib.pbkdf2_hmac('sha256', plaintext, salt, 10000)
```

*   `digest`变量保存返回的摘要对象。这个 hash 对象公开了一系列操作生成的 hash 的方法。为了简化本文，我们将只考虑如何检索人类友好的十六进制格式的散列，以及一个字节串。
*   使用`digest.hex()`将哈希作为友好的十六进制字符串进行检索

```
hex_hash = digest.hex()
print(hex_hash)
```

输出:

```
83665efbba86a7bc431c118732a3833ba6f1ddac0b9bab1ee5f5403459347678
```

*   在字节串中检索散列，我们将使用方法`digest.fromhex()`。该方法将十六进制字符串作为参数。

```
byte_hash = digest.fromhex(digest.hex())
print(byte_hash)
```

输出:

```
\x83f^\xfb\xba\x86\xa7\xbcC\x1c\x11\x872\xa3\x83;\xa6\xf1\xdd\xac\x0b\x9b\xab\x1e\xe5\xf5@4Y4vx
```

**完整代码**

使用 pbkdf2_sha256 生成 salted hash 的完整代码

## 参考

*   [基于密码的密钥派生推荐标准第 1 部分:存储应用](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf)
*   [使用批准的哈希算法的应用推荐](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-107r1.pdf)
*   [OWASP 密码存储备忘单](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
*   [Python hashlib 模块](https://docs.python.org/3/library/hashlib.html)

希望这篇文章对你有所帮助。

*作为中级会员，您可以无限制地访问无限制的有用内容。使用* [*我的推荐链接*](https://ofelix03.medium.com/membership) *加入今天只为****【5】****。通过* [*加盟 Medium*](https://ofelix03.medium.com/membership) *，你也是在支持我的写作，因为我在你使用* [*我的推荐链接*](https://ofelix03.medium.com/membership) *的时候赚了一点佣金。这项佣金不会增加你的额外费用。*

[](https://ofelix03.medium.com/membership) [## 通过我的推荐链接加入媒体-费利克斯·奥托

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

ofelix03.medium.com](https://ofelix03.medium.com/membership)