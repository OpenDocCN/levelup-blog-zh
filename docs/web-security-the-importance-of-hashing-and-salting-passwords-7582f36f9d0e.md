# 网络安全:散列和加盐密码的重要性

> 原文：<https://levelup.gitconnected.com/web-security-the-importance-of-hashing-and-salting-passwords-7582f36f9d0e>

![](img/f5018cba656d859e15964a60684c6690.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[静止不动](https://unsplash.com/es/@stillnes_in_motion?utm_source=medium&utm_medium=referral)

说到网络安全，最重要的一个方面是保护密码。在本文中，我们将讨论散列和加盐密码的重要性。我们还将看看这些技术如何帮助保护您的网站免受黑客攻击。

## 为什么我们不应该存储纯文本密码

保护应用程序时要遵循的一条重要规则是不要以纯文本形式存储密码。这意味着，如果黑客获得了您的数据库，他们将能够看到所有的明文密码。这将使他们可以自由支配任何使用这些密码的账户。

如果一个公司以纯文本的形式存储密码，这也意味着任何可以访问数据库的开发者也可以看到密码。虽然大多数开发人员是值得信任的，但是总是存在恶意使用这些信息的可能性。

## 什么是哈希函数？

哈希函数是一种数学算法，它获取数据并将其转换为固定大小的输出。这个输出被称为哈希值。哈希函数是保护密码的关键。哈希函数的输出不能通过反转来获得输入。因此，如果黑客能够访问您的数据库，他们只能看到哈希值，而看不到实际的密码。

然而，将相同的输入放入散列函数总是返回相同的散列值，允许应用程序验证给定的密码是正确的。

以下代码片段演示了哈希函数的工作原理:

```
function hashFunc() {// the hash function’s logic to transform the input into the hash   value}let input = ‘password’;let hashedPassword = hashFunc(input);console.log(hashedPassword) // “23k17dshqnplsaif”
```

![](img/055a0e58e87a9be3596a353853fb08cf.png)

[Towfiqu barbhuiya](https://unsplash.com/es/@towfiqu999999?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

## 为什么散列密码很重要？

散列密码很重要，因为这意味着即使黑客获得了你的数据库，他们也看不到真正的密码。这将使他们更难侵入账户。

然而，散列并不完美。有某些类型的攻击可以用来猜测密码，即使它们是散列的。即使哈希值不能被反转以找到输入，仍然有可能找出原始输入。如果哈希密码泄露，黑客就有可能破解密码。为了解决这个问题，使用一种称为加盐的技术是很重要的。

## 什么是腌制？

加盐是一种用于使猜测密码更加困难的技术，即使哈希值已经泄露。salt 是一个随机字符串，在哈希处理之前添加到密码中。salt 通常有 32 个字符长，可以添加到纯文本密码的开头、结尾或中间。这使得黑客更难从哈希值中找出纯文本密码。

以下代码片段演示了 salting 如何工作的示例:

```
input = salt + plainTextPassword;hashedOutput = hashFunc(input);
```

Salting 解决了源于单独散列密码的问题。对密码进行哈希运算时，两个相同的密码会返回相同的哈希值。但是，如果将哈希函数与 salting 结合使用，两个相同的密码将不会返回相同的哈希值。这是因为每种盐极有可能是独一无二的，因此每种产出将是不同的。

## 结论

为了保护密码，对密码进行散列和加盐处理是很重要的。仅有散列是不够的，因为如果黑客可以访问散列值，他们仍然可以确定纯文本密码。Salting 使得黑客破解密码更加困难，即使他们有哈希值。通过使用这两种技术，您可以帮助保护密码的安全。

我希望这篇文章能教会你一些东西。祝你的编码面试好运！