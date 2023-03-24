# 从头开始在 Node.js 和 C#中实现 AES 加密

> 原文：<https://levelup.gitconnected.com/implementing-aes-encryption-in-node-js-and-c-from-scratch-6ee7b47ae6d4>

## 在 Node 和 C#中实现 AES 加密的简单指南

![](img/f16ed041b9df91fb3742c209373bd222.png)

卢卡·布拉沃在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们需要数据加密来保护我们的数据，同时在微服务之间或向客户端传输。它阻止其他人了解我们的数据。为了实现端到端加密，我们需要确保只有发送方和接收方能够读取传输的消息。

# AES 加密

> 高级加密标准(AES)是一种对称的[分组密码](https://searchsecurity.techtarget.com/definition/block-cipher)，由美国政府选择用于保护机密信息，并在世界各地的软件和硬件中实施，用于加密敏感数据。— [来源](https://searchsecurity.techtarget.com/definition/Advanced-Encryption-Standard)

## 为什么是 AES？

它是全球广泛使用的最安全、最成熟的加密方法之一。由于有大量关于 AES 的阅读材料，我将只与 RSA(我也实现了 RSA)做一些比较。

## RSA 与 AES

RSA 最多只能加密 245 字节的数据，而 AES 几乎可以无限制地加密数据。唯一的副作用是随着数据越来越多，速度会越来越慢。RSA 使用公钥和私钥进行加密，而 AES 使用字符串作为密钥以及 IV(初始化*向量)来添加随机化。*当我们不断改变 IV 的值时，它总会生成不同的加密值作为输出；而 RSA 将继续提供相同的价值。我并不是说 RSA 不安全，但它可能会提示您的系统可能会使用什么加密。

## C#

AES 在 C#中加密和解密

## 具有类型脚本的节点

AES 在 NodeJS 中用 Typescript 加密和解密

# 关键注意事项

1.  为了使双方互相合作，确保**加密类型**、**密钥大小**、**密钥**和 **IV** (初始化向量)是相同的。
2.  在用 C#加密 JSON 对象之前，可以将它序列化为字符串。
3.  为了增加加密的随机性，在加密时随机生成 IV。但我们必须确保解密方也有静脉注射。它将确保即使是要加密的数据也是相同的，并且每次加密的字符串都是不同的。(类似于 C#中的 salt 密码)
4.  将凭证存储在安全的网络或服务中，如 [Azure Keyvault](https://azure.microsoft.com/en-us/services/key-vault/) 、 [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 、 [AWS 密钥管理](https://aws.amazon.com/kms/)。

# 摘要

这基本上就是通过 NodeJS 和 C#.NET 加密和解密消息所需的全部内容。任何定制都依赖于您的实现来进一步进行。

# 最后一个音符

由于新冠肺炎的疫情，在这个艰难的时刻，请大家保持安全，好好吃饭，尽可能呆在家里。我们可以利用这段时间和家人走得更近。我们可以一起度过难关，干杯！