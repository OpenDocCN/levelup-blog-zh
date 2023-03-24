# Scala 中使用自身类型的依赖注入(蛋糕模式)

> 原文：<https://levelup.gitconnected.com/dependency-injection-using-self-types-in-scala-cake-pattern-67c76e603e5b>

本文旨在帮助开发者理解如何借助 Scala 中的 ***自类型*** 实现依赖注入。这种模式也被称为 ***蛋糕模式*** ，用于 Scala 编译器中。

![](img/15c7115556616d7b0f01f6151a3218b6.png)

[汀天](https://unsplash.com/@steven__chan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 什么是自我类型？

> 自我类型是一种声明一种特性必须混合到另一种特性中的方式，即使它没有直接扩展它。

让我们看一个例子:

```
trait A {
  this: B
}
```

这基本上意味着要实现 *A* 你需要实现 *B* ，也就是说 *A* 依赖于*B*

这里，B 被称为 a 的自我类型。自我类型是用关键字' *this* '声明的。

你可以拥有多种特质作为特定类型的自我类型。例如，如果 A 依赖于 B、C 和 D，那么上面的代码应该是这样的:

```
trait A {
  this: B with C with D
}
```

在这种情况下，如果你实现了 *A* ，你也需要实现 *B* 和 *C* 和 *D* 。

# *依赖注入使用自我类型*

让我们通过一个例子来弄清楚如何使用 self type 注入依赖项。

我们这里的问题是创建一个登录服务，它使用某种认证机制来认证用户。我们将从基本代码开始，然后继续重构(这里的目标不是编写成熟的生产就绪代码)。

让我们考虑一个使用第三方认证服务(LDAP/OAuth 服务器)的简单登录服务。为这个认证服务定义一个接口是明智的:

```
trait AuthenticationService {
  def authenticate(userInfo: String) : Response
}
```

为了简洁起见，我们假设服务将接受包含用户名和密码的字符串，并返回一个响应对象。

好的，让我们继续实现这个接口。我们的用户将通过 OAuth 服务器进行身份验证:

```
class OAuthAuthenticationService extends AuthenticationService {
  def authenticate(userInfo: String) : Response = ???
}
```

这个类*oauthenticationservice*将被我们的登录服务使用。使用构造函数，我们可以将这种依赖注入 LoginService，如下所示:

```
class LoginService(authenticationService: AuthenticationService) {
  def login(userInfo: String) : Response = authenticationService.authenticate(userInfo)
}
```

一切都很好，但我们想在这里使用自身类型，而不是使用构造函数。为了将*oauthenticationservice*作为 *LoginService* 的自身类型，我们必须修改*oauthenticationservice*。所以，让我们继续把它包装在一个特征中。我稍后会解释为什么我们需要这样做。

```
trait OAuthAuthenticationServiceComponent { val authenticationService: AuthenticationService = new OAuthAuthenticationService class OAuthAuthenticationService extends AuthenticationService {
    def authenticate(userInfo: String) : Response = ???
  }
}
```

如您所见，我们已经将实际的实现包装在一个特征中。此外，实现对象的创建是在 trait 本身内部完成的。等等，我知道你很惊讶，但我保证我们会回到这个话题。

为了在*登录服务*中使用这个自类型，我们必须对*登录服务*做一点修改:

```
trait LoginServiceComponent {

  this: OAuthAuthenticationServiceComponent =>   val loginService = new LoginService class LoginService {
    def login(userInfo: String) : Response =   authenticationService.authenticate(userInfo)
  }
}
```

我们将实现类包装在一个特征中，就像我们对*oauthenticationservice*所做的那样。让我解释一下这是怎么回事。

*LoginServiceComponent* 内的 *LoginService* 使用 *authenticationService* ，它是*oauthtauthenticationservice component 的成员。*这是因为 *LoginServiceComponent* 已经将*oauthenticationservicecomponent*定义为其自身类型。 *LoginServiceComponent* 的成员可以访问*oauthenticationservicecomponent*的成员，但反之则不行。

现在，这里唯一剩下的是将所有组件连接在一起的 config 对象。ComponentRegistry 这里是我们的配置对象:

```
object ComponentRegistry extends LoginServiceComponent
  with OAuthAuthenticationServiceComponent
```

现在可以像这样调用登录方法:

```
ComponentRegistry.loginService.login(userInfo)
```

这是可行的，但是实现对象的创建逻辑与实际的实现逻辑相耦合，这一点也不灵活。让我们继续改变这一点:

```
trait OAuthAuthenticationServiceComponent {val authenticationService: AuthenticationServiceclass OAuthAuthenticationService extends AuthenticationService {
    def authenticate(userInfo: String) : Response = ???
  }
}
```

并且 *LoginServiceComponent* 看起来会像:

```
trait LoginServiceComponent {

 this: OAuthAuthenticationServiceComponent => val loginService: LoginService class LoginService {
     def login(userInfo: String) : Response =   authenticationService.authenticate(userInfo)
   }
}
```

这给了我们在 config 对象中实例化我们选择的实现对象的灵活性。我们的配置对象现在看起来像这样:

```
object ComponentRegistry extends LoginService
  with OAuthAuthenticationServiceComponent {

  override val authenticationService: AuthenticationService = new OAuthAuthenticationService override val loginService: LoginService = new LoginService
}
```

LoginService 现在将使用 OAuthAuthenticationService 对用户进行身份验证。你可能已经想通了， ***这里的一切都是编译时注入*** 这种类型的依赖。只要您在配置对象中创建了正确类型的对象，就可以开始了。

现在将以同样的方式调用 login 方法:

```
ComponentRegistry.loginService.login(userInfo)
```

# **注入不同的依赖关系**

要使用 LDAP 身份验证来代替 OAuth，我们必须为它编写我们的实现，将其包装在 trait 中，并在 config 对象中创建一个该类型的对象:

```
override val authenticationService: AuthenticationService = new LdapAuthenticationService
```

这样，我们可以使用自身类型注入依赖项。虽然我只使用了一种自我类型，但是您也可以使用多种自我类型，将*与*关键字结合使用:

```
this: OAuthAuthenticationServiceComponent with LoggerComponent with SomeOtherComponent =>
```

现在来谈谈为什么一切都围绕着特质？

你会看到，在这种依赖注入中，我们通常会扩展多个组件，这只有在我们将实现包装在 traits 中时才有可能，因为 Scala 允许扩展多个 traits，但不允许扩展多个类。

# ***这和平原老传承有什么不同？***

有了继承，你就被超类的实现卡住了。使用 self 类型，您可以在配置对象中混合不同的实现。

继承还有另一个问题，超类把它的信息泄露给层次结构中的每一个类。这是违反封装的一个典型例子。

通过使用自身类型的依赖注入，您可以在依赖层次结构中创建层。例如，考虑下面的例子:

```
trait B {
  this : A
}trait C {
  this : B
}
```

***特征 C 的成员能够访问特征 B 的成员，但是不能访问特征 A 的成员*** ，从而在应用程序中创建层。因此这种图案也被称为**饼纹。**

# 结论

Cake 模式为您提供了编译时安全性，因为您在 config 对象中混合了您的实现。如果 config 对象中缺少任何实现，编译器将不会编译您的代码。因此，这种模式可以用在编译类型配置可以接受的地方。

如果您必须表达相互依赖，这种模式也很有帮助。蛋糕模式允许循环依赖，这是其他类型的 DI 所不具备的。

我们在一个项目中使用蛋糕模式，我们已经意识到它带来的问题很容易超过它的优点。现在您可能已经意识到，许多样板代码都是以这种模式编写的，因为您必须将每个实现都包装在一个特征中。这种模式有更多的缺点，但这是改天的话题。

我个人还没有找到使用这种模式的足够好的理由，但这只是我的看法。在 Scala 中注入依赖还有其他方法。其中一些是:

1.  通过构造函数传递普通的旧参数(个人偏好)
2.  Guice(由 Play 框架使用)
3.  麦克威尔
4.  Scala 中的隐含
5.  春天(为什么不呢？)
6.  Reader monad(使用语言构造的蛋糕模式的可能替代方案)

我希望这篇文章有助于理解 DI 是如何在 Scala 中使用自类型的。感谢您的阅读。

可运行代码可以在[https://github.com/shayan1337/scala-cake-pattern](https://github.com/shayan1337/scala-cake-pattern)找到