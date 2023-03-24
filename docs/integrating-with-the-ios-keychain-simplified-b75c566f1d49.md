# 与 iOS 钥匙串集成，简化

> 原文：<https://levelup.gitconnected.com/integrating-with-the-ios-keychain-simplified-b75c566f1d49>

![](img/1eb3fe4a7addae9ff0a108e06dd9981b.png)

标准 iCloud 钥匙串服务器(加利福尼亚州苹果公园，2019 年)

# 介绍

如果说 UserDefaults 是微波炉，那么 Keychain 就是烤肉。在我第一次用 Keychain 开发时，我期望它是一个非常基本的键值存储，可能像 UserDefaults 一样，但在安全性方面有所尝试。虽然这或多或少是事实，但如果你不熟悉这些明显的差异，就会让你生气、困惑，吃未煮熟的食物。

最重要的是，在网上找到实用的帮助并不容易。因此，就像我的大多数其他文章是如何构思的一样，我想我应该整理出一个使用 Keychain 的小指南。

这篇文章不会太深入，因为——当我知道我到底在做什么时，我开始意识到——[文档](https://developer.apple.com/documentation/security/keychain_services)实际上非常好。所以请注意--我将不再讨论访问组和底层的东西。

本文*将*做的是让你熟悉 Keychain 独特的 API，给你一些使用它的实用建议，并希望引导你得出启发性的结论，即 Keychain 毕竟只是一个键值存储。

# 什么是钥匙扣？

Keychain 是 Apple 的一项服务，它将密码、证书和密钥等用户机密储存在安全的数据库中。从开发人员的角度来看，它类似于 UserDefaults，因为它允许您存储(和持久化)特定于给定用户的少量数据。然而，虽然用户默认值可以很容易地访问和读取，但钥匙串项目是受保护的。

# 如何使用钥匙串

Keychain 和常规的旧键值存储之间的最大区别在于，键值存储有密钥，而 Keychain 有查询。有时，一个查询与钥匙链和 boom 中的一个项目完全匹配，相同但不同，你就有了一个键值存储。其他时候，这些查询可能会返回一大堆东西。

换句话说，查询就是查询。

但是有一个问题:这些查询不仅用于*查询钥匙链中的*项，还用于*插入*它们。

例如，让我们来看看这个用于将项目添加到 Keychain 的查询:

这是一个相当简单的查询。以下是对每个属性作用的简要说明:

*   ***kSecClass:*** 我们要插入钥匙圈的项目类型；在这种情况下，是一个通用密码。
*   ***kSecAttrSynchronizable:***该属性表示项目是否应该与 iCloud 同步。
*   ***kSecAttrAccount:***该密码的帐户名。如果您想创建一个准键值存储，使用这个属性作为键，使用 *kSecValueData* 字段作为值将是一种可行的方法。
*   ***kSecValueData:*** 最后，我们要插入到 keychain 中的数据。

仅此而已；我们的数据现在在 Keychain 中。

从这里开始，我们可以稍微修改一下这个查询，实现一个 fetch 而不是 insert，然后用它来检索我们的数据。在下面的代码中，我删除了 *kSecValueData* 字段(仅用于将数据插入到 keychain 中)并添加了 *kSecReturnData* 字段，这表明我希望查询返回数据。

然后，我们可以使用查询来检索我们刚才存储的数据，如下所示:

瞧，我们存储的钥匙链项目已被检索到。

但是当我们将 *kSecAttrSynchronizable* 属性更改为 *false* 时会发生什么呢？

不幸的是，我不能很容易地以交互方式演示这一点，所以我只能告诉你:我们什么也得不到。好吧，我们确实得到了 OSStatus -25300(表示没有找到数据)，但这确实是题外话。这是因为这个项目是在将 *kSecAttrSynchronizable* 属性设置为 *true* 的情况下插入到 Keychain 中的，因此这个将 *kSecAttrSynchronizable* 设置为 *false* 的新查询不再与之匹配。所以要确保你所有的查询都是完全正确的，因为像这样的小细节会导致很多痛苦。

这就是 Keychain 的工作方式:您使用查询来插入项目——我们称之为“插入查询”——要取回这些项目，您必须使用与这些“插入查询”的所有属性相匹配的查询

# 模仿键值存储

Keychain 的一个常见用例是模拟键值存储。我简单提到的解决方案是使用*kSecClassGenericPassword*字段作为“键”，使用 *kSecValueData* 字段作为“值”

然而，不要急于围绕 Keychain 创建一个简单的键值抽象并使用它，因为这样做会失去 Keychain 提供的许多很酷的功能。

例如，[*kSecClassInternetPassword*](https://developer.apple.com/documentation/security/ksecclassinternetpassword)*类，连同其相关属性如[*kSecAttrServer*](https://developer.apple.com/documentation/security/ksecattrserver)*和[*kSecAttrAccount*](https://developer.apple.com/documentation/security/ksecattraccount)*，*可以让你的应用程序将钥匙串项目与特定的域和帐户相关联，以便 Safari 等其他应用程序可以使用它们为你的用户自动填充密码。**

**或者考虑一下[*kSecAttrAccessControl*](https://developer.apple.com/documentation/security/ksecattraccesscontrol?language=objc)*属性，它可以让您对哪些钥匙圈项目需要什么权限进行粒度控制；有些可能需要 face ID，有些可能需要解锁设备，等等。***

***你能做的还有很多很多。确保您的实现是灵活的！***

# ***避免竞态条件***

***Keychain 有一个由来已久的问题，就是搞不清自己。如果你的程序正在运行钥匙串查询，很有可能你会遇到竞争情况，你的程序会崩溃。***

***鉴于此，为你的应用程序的钥匙链实现编写一个包装器，并使用 NSLock(或类似的东西)来确保一切顺利运行是一个好主意。***

# ***结论***

***那是钥匙链。很好地理解 API 和它的独特之处是困难的部分。一旦你记下了这一点，就真的没有更多的了；疯狂去实现一些奇特的功能！***