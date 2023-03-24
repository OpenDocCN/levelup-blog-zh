# 使用 Java v2 SDK 和 s3 上传的 AWS 客户端加密

> 原文：<https://levelup.gitconnected.com/aws-client-side-encryption-with-kms-using-java-v2-sdk-and-s3-upload-73b525314a5b>

## 一般情况下使用 AWS SDK

一个简单的例子，用 Java AWS SDK v2 实现客户端加密，然后上传到 s3。

这是一个简短的帖子，分享我在使用 Java SDK for AWS 使用 KMS 进行客户端加密后上传有效载荷的工作中所学到的东西。

![](img/1bc1d39422c0194ac5c84da96089306d.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/lock-and-key?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## SDK v1 和 v2

AWS 现有两个版本的 JAVA SDK。v1 和 v2。v2 是对 v1 的重写。对于较新的项目，建议使用 v2，但是 AWS 文档表明 v1 和 v2 可以一起使用以实现某种互操作性。根据我的经验，这是一个很难处理的问题。

 [## AWS SDK for Java 1.x 和 2.x 有什么不同

### 您现在可以使用 AWS SDK for Java 2.x 中的亚马逊 S3 传输管理器(开发者预览版)来加速文件…

docs.aws.amazon.com](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/migration-whats-different.html) 

我一直在寻找一个关于我试图实现的用例的适当文档，但是一开始没有找到。尤其是我正在与加密密钥承诺策略作斗争。

我的任务是在客户端使用加密 ***密钥提交策略*** 用 KMS 加密有效载荷，该策略在客户端(NodeJS 项目)可用且兼容，客户端将使用相同的 KMS 密钥解密有效载荷。

> [密钥承诺](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/concepts.html#key-commitment)确保您的加密数据总是解密成相同的明文。

经过一段时间的搜索和反复试验，这里有一个 java aws sdk v2 中的工作示例。

这是我的 pom

和 java 类

注意第 31 行的关键承诺策略。

如果您仍然使用 SDK 的版本 1，唯一可用的选项是**forbidencryptalllowdecrypt**，并且它必须在两端相同(encryptor 和 decrypt-or)。

如果您使用的是版本 2 的 SDK，我们有 3 个选项:**ForbidEncryptAllowDecrypt**，**requirencryptallowdecrypt**，**requirencryptdirect**

如果您从版本 1 迁移到版本 2，在更改关键承诺策略时必须特别小心，这里是阅读资料

 [## 设定你的承诺政策

### 密钥承诺确保您的加密数据总是解密为相同的明文。为了提供这种安全性…

docs.aws.amazon.com](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/migrate-commitment-policy.html) 

就是这样…

如果你喜欢这个帖子，请分享或评论这个帖子。任何形式的互动都是非常受欢迎的。如果你想支持我，并获得所有伟大的事情，请加入并成为会员。这是我的推荐链接:

[](https://susamn.medium.com/membership) [## 用我的推荐链接加入媒体

### 阅读 Supratim Samanta 的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接…

susamn.medium.com](https://susamn.medium.com/membership)