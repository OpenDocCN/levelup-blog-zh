# 如何在 Spring Boot 实现基本接入认证

> 原文：<https://levelup.gitconnected.com/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19>

## 让您的 Spring Boot API 更加安全

![](img/9ea7be3ec4338b4112838644aba127d6.png)

丹·尼尔森在 [Unsplash](https://unsplash.com/s/photos/cybersecurity?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

使用像 [Spring Security](https://spring.io/projects/spring-security) 到[这样专门设计的框架来实现你的安全目标](https://medium.com/technology-hits/why-devsecops-is-one-the-biggest-tech-trends-in-2021-b8d8c56e38fc)，比如授权和认证，可能是最好的解决方案。

[](https://medium.com/technology-hits/why-devsecops-is-one-the-biggest-tech-trends-in-2021-b8d8c56e38fc) [## 为什么 DevSecOps 是 2021 年最大的技术趋势之一

### 安全性是 IT 企业成功的关键

medium.com](https://medium.com/technology-hits/why-devsecops-is-one-the-biggest-tech-trends-in-2021-b8d8c56e38fc) 

> “Spring Security 是一个专注于为 Java 应用程序提供认证和授权的框架。像所有 Spring 项目一样，Spring Security 的真正强大之处在于它可以很容易地扩展以满足定制需求。” *—* [*弹簧安全*](https://spring.io/projects/spring-security)

然而，对于基本需求，编写几行代码来实现您的身份验证逻辑可能是一种更好的方法。这样，您可以保持您的应用程序简单，并避免不必要的依赖。

我将要介绍的方法将允许您选择使用[基本访问授权](https://en.wikipedia.org/wiki/Basic_access_authentication)来保护 API。该方法允许 [HTTP 用户代理](https://en.wikipedia.org/wiki/User_agent)在发出请求时指定用户名和密码。只有当收到的凭证有效时，服务器才会授权该请求。它被称为*基本*，因为它不需要[HTTP cookie](https://en.wikipedia.org/wiki/HTTP_cookie)、会话标识符或登录页面。事实上，这是对 web 资源实施访问控制的最简单的技术，因为它只基于 HTTP 头中的标准字段。

让我们看看如何在 Spring Boot 用 Java 和 Kotlin 实现基本的访问认证。

# 1.定义自定义注释

要选择 HTTP 基本身份验证系统应该保护哪些 API，您需要一个自定义的注释。这种注释将用于标记类型为`User`的参数，以定义 API 是否需要认证。

因此，您假设每个有效的身份验证凭据对都可以与特定的用户相关联。因此，这些凭证将从特定的 HTTP 头中检索，并由身份验证逻辑用来提取用户，用户的数据将自动传递给受保护的 API 的函数体。

让我们看看如何定义一个定制的`Auth`注释:

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

# 2.定义验证逻辑

您应该将认证逻辑放在一个特定的组件中，比如`BasicAccessAuthenticationHandler` *。*该类的目的是验证收到的凭证是否有效，并提取其相关用户。

这是这样一个类的样子:

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

该组件所做的是提取标头中收到的凭据，并使用它们来尝试检索用户。正如在 [RFC](https://en.wikipedia.org/wiki/RFC_(identifier)) [7617](https://tools.ietf.org/html/rfc7617) 中所定义的，当处理基本访问认证时，您应该期望一个`Authorization: Basic <credentials>`形式的头字段，其中凭证是由单个冒号`:`连接的*用户名*和*密码*的 [Base64](https://en.wikipedia.org/wiki/Base64) 编码。这就是为什么你需要先解码，然后除以`:`的原因。

另外，如您所见，当凭证丢失或无效时，会抛出一个自定义的`AuthenticationException`。在这种情况下，受保护的 API 应该用 *401 未授权的*状态码来响应。

*“HTTP 401 未经授权的客户端错误状态响应代码表示请求尚未应用，因为它缺少目标资源的有效身份验证凭据。”—* [*MDN 网络文档*](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401#)

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

为了实现这一点，标记有`[@ControllerAdvice](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html)`的类可以如下使用:

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

# 3.实施基本身份验证

要让 Spring Boot 在指定定制的`Auth`注释时自动寻找基本的访问认证凭证，您需要为`[HandlerMethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/support/HandlerMethodArgumentResolver.html)`接口提供一个实现。

这可以通过以下方式实现:

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

# 4.配置 Spring Boot

现在，您只需要为配置定义一个定制类。这样，Spring Boot 将能够像设计的那样使用定制的`Auth`注解。

为了一切正常，您需要将之前定义的`CustomWebResolver`类添加到默认的参数解析器中。这可以通过利用`[WebMvcConfigurationSupport](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurationSupport.html)`级来实现。

> “[WebMvcConfigurationSupport]通常通过向应用程序`@Configuration`类添加`@EnableWebMvc`来导入。另一个更高级的选择是直接从这个类扩展，并根据需要覆盖方法，记住将`@Configuration`添加到子类，将`@Bean`添加到被覆盖的`@Bean`方法。 *—* [*春天的官方文件*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurationSupport.html)

因此，您应该定义一个像这样扩展`WebMvcConfigurationSupport`的`@Configuration`注释类:

## Java 语言(一种计算机语言，尤用于创建网站)

## 科特林

请注意，当使用`WebMvcConfigurationSupport`、*、*时，您可能需要处理 CORS 配置。否则，您的 API 可能无法像预期的那样到达。

# 5.把所有的放在一起

现在，是时候看看如何使用`Auth`注释来使一个 API 只被经过认证的用户访问了。这可以通过向所选的控制器 API 函数添加一个标有`Auth`注释的`User`类型参数来轻松实现:

## Java 语言(一种计算机语言，尤用于创建网站)

受基本访问认证保护的 API

## 科特林

受基本访问认证保护的 API

此外，定义缺乏保护的 API 也是可能的:

## Java 语言(一种计算机语言，尤用于创建网站)

不受基本访问身份验证保护的 API

## 科特林

不受基本访问身份验证保护的 API

# 奖金

使用非常相似的方法，您可以实现自定义令牌认证，如这里的[所述。](https://betterprogramming.pub/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2)

[](https://betterprogramming.pub/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2) [## 如何在 Spring Boot 和科特林实现自定义的基于令牌的身份验证

### 构建更安全的 Spring Boot 应用程序

better 编程. pub](https://betterprogramming.pub/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2) 

# 结论

忽视安全可能会产生有害的后果。这就是为什么你不应该让每个人都可以访问你所有 API 的原因。通过使用基本访问认证方法，您可以保护最重要的 API。正如我所展示的，实现它和选择要保护的 API 并不复杂，在大多数情况下，这样一个简单的方法就足以保护您的应用程序。

感谢阅读！我希望这篇文章对你有所帮助。如有任何问题、意见或建议，请随时联系我。