# Kotlin 多平台 iOS:证书锁定

> 原文：<https://levelup.gitconnected.com/kotlin-multiplatform-ios-certificate-pinning-fd1abba5ca8f>

![](img/d70601b293bd6a6181065c162c8e190e.png)

## 如何使用 Ktor 在 Kotlin 多平台中实现证书锁定

# 证书锁定

在与远程 API 交互时，固定证书是一种常见的做法。这是限制您信任哪些证书的行为。这有助于抵御对证书颁发机构的攻击。它还有助于对抗中间人攻击。

有大量关于证书锁定的信息。以下是一些帮助您开始的信息:

-[https://owasp . org/www-community/controls/Certificate _ and _ Public _ Key _ Pinning](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning)

-[https://labs . nettitude . com/tutorials/TLS-certificate-pinning-101/](https://labs.nettitude.com/tutorials/tls-certificate-pinning-101/)

-[https://docs.broadcom.com/docs/certificate-pinning-en](https://docs.broadcom.com/docs/certificate-pinning-en)

# 科特林多平台

[Kotlin 多平台](https://kotlinlang.org/docs/reference/multiplatform.html) (KMP)是用 [Kotlin](https://kotlinlang.org/) 编写跨平台代码的一种方式。它不是要为所有平台编译所有代码，也不是要把你限制在一个公共 API 的子集上。KMP 提供了为通用 API 编写特定于平台的实现的机制。

# Ktor

Ktor 是一个用于构建异步服务器和客户端的 KMP 框架。Ktor 的客户端库使您能够创作连接到远程 API 的多平台代码。

Ktor 客户端支持 JVM、Android、iOS、Javascript、macOS、Linux 和 Windows。

Ktor 提供了执行特定于平台的请求的引擎。这些引擎遵循一个公共接口。在普通代码中，您可以通过引擎发出 HTTP 请求。

对于 Android，可以使用一个 [OkHttp](https://square.github.io/okhttp/) 引擎。Ktor 内置了一个使用`NSURLSession`类的 iOS 引擎。

# 用 OkHttp 锁定

OkHttp 长期以来一直支持针对公钥的证书锁定。实现很简单，我喜欢他们解决这个问题的方法。

[https://square . github . io/ok http/4 . x/ok http/ok http 3/-certificate-pinner/# setting-up-certificate-pinning](https://square.github.io/okhttp/4.x/okhttp/okhttp3/-certificate-pinner/#setting-up-certificate-pinning)

# 用 iOS 钉住

在 iOS 上，这就没那么简单了。你必须覆盖`NSURLSessionDataDelegateProtocol`上的一个函数，并用它配置一个`NSURLSession`。

[https://developer . apple . com/documentation/foundation/nsurlsessiondelegate/1409308-urlsession？language=objc](https://developer.apple.com/documentation/foundation/nsurlsessiondelegate/1409308-urlsession?language=objc)

以科特林为代表:

```
fun invoke(
    session: NSURLSession,
    challenge: NSURLAuthenticationChallenge,
    completionHandler: (NSURLSessionAuthChallengeDisposition, NSURLCredential?) -> Unit
)
```

在这个函数中，您需要检查挑战的各个方面。最终检查证书符合您的期望。

# 认证员

我开始为 KMP 编写一个简洁的类来实现这个证书锁定功能。因为我们需要能够覆盖一个委托方法，所以我们至少需要使用 Ktor 的 1.3.2 版本。

这是我想到的类。它受到 OkHttp 的`CertificatePinner`的启发，与他们的实现非常接近。

# 如何使用

若要开始，请使用中断的配置来配置证书固定。然后，当连接失败时，在日志中读取预期的配置。一定要在一个可信的网络上做这件事，不要使用像查尔斯[或小提琴](http://charlesproxy.com)[这样的中间人工具。](http://fiddlertool.com)

例如，要锁定[https://publicobject.com，](https://publicobject.com,)从断开的配置开始:

不出所料，此操作会因异常而失败，请查看日志:

现在将公钥散列粘贴到证书 pinner 的配置中:

# 在后台

## 挑战主持人

首先要注意的是`CertificatePinner`实现了`ChallengeHandler`。这意味着它实现了所需的委托方法。它将在 iOS 引擎中被调用。

这个`Builder`和`Pin`类使用户能够轻松地创建一组公钥来锁定。它们还提供通配符功能。你可以在课程文档中读到这方面的内容。

## `NSURLSession`委派

`invoke`功能是实际锁定功能发生的地方。

首先，我从`challenge`中检索`hostname`。然后，我检查是否有针对该主机的引脚。然后我检查`authenticationMethod`是否适合证书固定。

详见[苹果文档](https://developer.apple.com/documentation/foundation/nsurlauthenticationmethodservertrust?language=objc)。

我检索`serverTrust`并检查它的[有效性](https://developer.apple.com/documentation/security/2980705-sectrustevaluatewitherror)(如果配置了的话)。

现在到了我检查证书公钥和固定公钥的时候了。这里的大部分复杂性是以我想要的格式检索公钥。

一旦格式正确，我们就检查它是否与固定格式匹配，并返回结果。

# 结论

这是一个很难解决的问题。它涉及到使用 Kotlin 的 C-interop 特性，这可能很难掌握。

这个类可供您随意使用和更改。希望它也能帮助你解决这个问题。

*上一篇文章*

[](https://medium.com/@alistairsykes/kotlin-multiplatform-android-ios-connecting-coding-and-culture-a2412efd7c0c) [## Kotlin 多平台 Android/iOS:连接编码和文化

### Kotlin 多平台如何影响您的开发文化？

medium.com](https://medium.com/@alistairsykes/kotlin-multiplatform-android-ios-connecting-coding-and-culture-a2412efd7c0c) 

*最初发表于*[*https://www.brightec.co.uk*](https://www.brightec.co.uk/blog/kotlin-multiplatform-ios-certificate-pinning)*。*