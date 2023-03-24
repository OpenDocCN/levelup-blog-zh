# 构建一个 API 来清除 Spring Boot 应用程序的所有缓存

> 原文：<https://levelup.gitconnected.com/building-an-api-to-clear-all-the-caches-of-your-spring-boot-application-2d0dfdfe71b3>

## 通过按需清除所有缓存数据来避免一致性问题

![](img/f1eae05d6df786c31f262da89c84c5c0.png)

由 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[áRPád Czapp](https://unsplash.com/@czapp_arpad?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

缓存提高了性能，但是也引入了[一致性问题](https://en.wikipedia.org/wiki/Cache_coherence)。这就是为什么在我的 Spring Boot 应用程序中启用缓存后，我意识到提供一个按需清除所有缓存的过程是多么重要。

登录到您的服务器，手动释放您的应用程序的所有缓存数据应该是最后和绝望的选择。因此，我开始寻找方法，为管理员提供一种可靠、安全的方式，在需要时清除所有缓存。幸运的是，这可以通过几行代码轻松实现。

让我们来看看如何定义一个自定义 API 来驱逐 Spring Boot 应用程序的缓存。

# 构建 API

让我们假设您的应用程序已经使用了一个缓存引擎，采用了 [Spring 缓存模块](https://spring.io/guides/gs/caching/)。一切都被认为是正确配置的。

为了处理缓存，Spring 在启动时在内部创建了一个[*cache manager*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/CacheManager.html)bean。尽管 Spring 没有提供按需清除所有缓存的特性，但是您可以通过利用这个缓存管理器来实现这一点。

事实上，它的`[getCacheNames()](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/CacheManager.html#getCacheNames--)`方法返回您的管理器已知的缓存名称的集合。通过迭代它们，您可以检索当前正在处理的每个 [*缓存*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/Cache.html) 实例，并对每个实例调用`[clear()](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/Cache.html#clear--)`方法。

通过这种简单的方法，您可以定义一种安全的方式来清除 Spring Boot 应用程序中使用的所有缓存。这就是为什么设计一个 API 来实现这样的目标并不复杂，并且可以如下实现:

# Java 语言(一种计算机语言，尤用于创建网站)

# 科特林

在这两种情况下，您只需要自动连接已实现的 *CacheManager* bean，并遵循上述方法。通过将这个逻辑封装在一个公开的端点中，您实际上定义了一个触发器，无论何时调用 API 都可以激活它。

请注意，只有在插入第一个条目时，缓存才会被初始化。这意味着在任何数据被缓存之前，您不会在`CacheManager`中看到任何可用的缓存。

另外，应该只允许少数选定的用户访问这个 API。这就是为什么我建议实现一个定制的身份验证系统。如需进一步阅读，您可以查看我以前的文章，其中我展示了如何在 Spring Boot 实现这样的目标。

[](https://betterprogramming.pub/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2) [## 如何在 Spring Boot 和科特林实现自定义的基于令牌的身份验证

### 构建更安全的 Spring Boot 应用程序

better 编程. pub](https://betterprogramming.pub/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2) [](/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19) [## 如何在 Spring Boot 实现基本接入认证

### 让您的 Spring Boot API 更加安全

levelup.gitconnected.com](/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19) 

# 奖金

正如我刚才所展示的，单个 API 可以对您的应用程序产生巨大的影响。类似地，随着项目的增长，您很容易忽略正在进行的工作或当前部署的内容。这就是为什么你应该通过[定义一个 API 来列出 Spring Boot](https://betterprogramming.pub/building-an-api-to-list-all-endpoints-exposed-by-spring-boot-645f1f64ebf3) 暴露的所有端点来保护自己。

[](https://betterprogramming.pub/building-an-api-to-list-all-endpoints-exposed-by-spring-boot-645f1f64ebf3) [## 构建一个 API 来列出 Spring Boot 公开的所有端点

### 不再迷失在你的后端

better 编程. pub](https://betterprogramming.pub/building-an-api-to-list-all-endpoints-exposed-by-spring-boot-645f1f64ebf3) 

此外，您不应该让您的缓存增长太多。这就是为什么[在 Spring Boot 缓存中添加压缩功能](https://betterprogramming.pub/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c)变得不可避免。

[](https://betterprogramming.pub/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c) [## 如何在 Spring Boot 的缓存中添加压缩

### 释放您的缓存

better 编程. pub](https://betterprogramming.pub/how-to-add-compression-to-caching-in-spring-boot-d4d21533167c) 

# 结论

处理缓存可能会带来一些只有彻底清理才能解决的问题。这正是你应该通过添加一些管理工具来保护自己的原因。正如我所展示的，实现一个 API 来清除 Spring Boot 应用程序的所有缓存并不复杂，在大多数情况下，它可以节省您的时间。

感谢阅读！我希望这篇文章对你有所帮助。如有任何问题、意见或建议，请随时联系我。