# 依赖注入和容器是如何帮助设计糟糕的软件的

> 原文：<https://levelup.gitconnected.com/how-poorly-designed-software-is-facilitated-by-using-dependency-injection-and-containers-76fdcba50a18>

## "我将再添加一个构造函数参数."

依赖注入(DI)和控制反转(IoC)经常让你做傻事，让你创建设计糟糕的软件。

依赖容器框架，如 Java 的 Spring Boot 或。NET ServiceCollection 让你摆脱懒惰的设计，因为当你依赖它时，你很少需要为自己考虑。

对于那些一无所知的人来说，简单回顾一下:依赖注入是一种实践，通过这种实践，你让一个类把它的依赖项作为构造函数的参数，而不是把它们重新组合起来。记住这句话:新是胶水。控制反转就是让其他东西——通常是依赖容器——注入所需的依赖。

# 懒惰的设计是什么样子。

粗心的开发人员创建了如下这样混乱的依赖图。

```
public class UserService {
  public UserService(
    IUserRepository userRepository,
    IUserManager userManager,
    IRolesRepository rolesRepository,
    INotificationService notificationService)
  {
   // Assign 
  }
}
```

我们先忽略这是一个所谓的“服务”类。如您所知，服务类通常是[逻辑垃圾站和反模式](https://medium.com/datadriveninvestor/the-true-meaning-of-service-and-manager-class-names-d09a08731fd9)。

无论如何，你可能会说“这是四个构造函数参数。这怎么可能是坏的或者设计的不好呢？”虽然我理解这种情绪，但让我举例说明这是多么简单。

首先，使用依赖框架很容易使用`UserService`。这无非是采取这样的依赖关系:

```
// Imagine this is an MVC controller
public class UserController : ControllerBase
{
  private readonly UserService service;

  public UserController(UserService service)
  {
    this.service = service;
  }
}
```

但是你有没有遇到过这样的时刻“我自己怎么实例化这个对象呢？”

# 用讨厌的对象图测试对象。

你突然被要求创建一堆协作对象，这些对象拥有自己的依赖关系。

下面的例子可能是人为的，但它确实类似于我在为小型和大型项目开发生产应用程序时在许多场合看到的情况。测试一个类很快会变成 7 个协作对象。

```
// Arrange
var appDbContext = new AppDbContext();
var sut = new UserService(
  new EfUserRepository(appDbContext),
  new DefaultUserManager(new DefaultPasswordHasher(SHA256.Create())),
  new EfRolesRepository(appDbContext),
  new DefaultNotificationService(new EfNotificationRepository(appDbContext))
);

// Act
bool result = sut.SaveUser("username", "password");

// Assert
Assert.True(result);
```

我认为这最好被描述为爆炸性腹泻编码。一个物体以霰弹枪的形式突出了许多其他物体。

聪明的开发人员会自豪地宣称这不是一个单元测试，而是坚持嘲笑对象。虽然这是真的，但嘲笑并不能真正缓解问题的根源。

模仿是一种设计工具，而不是测试策略。

# 反驳这种无稽之谈的方法。

花点时间考虑一下，你*是否真的*需要添加额外的构造函数参数。

也许在混乱的对象图中隐藏着一个概念。

我喜欢检查一个类的依赖项和方法，看看是否有只在一个方法中使用的依赖项，以及是否有可能去掉方法和依赖项来创建一个具有非常明确的责任的单一内聚类。

## 您需要分析扇入和扇出的复杂性。

您可以将扇出视为单个对象所依赖的数量，将扇入视为使用单个对象的对象数量。

您通常希望使用低扇出和高扇入，因为这将产生易于测试且灵活的代码。

但是，任何建议都有例外。对于高级的、面向业务的类，你不需要考虑太多，因为它们不太需要被重用。

# 让我们保持联系！

查看 YouTube 频道的[*(@ Nicklas Millard)*](https://www.youtube.com/channel/UCaUy83EAkVdXsZjF3xGSvMw)

1.  B [成为中级会员](https://nmillard.medium.com/membership)。
2.  当我写了一篇新文章时得到通知。
3.  [在 LinkedIn 上连接](https://www.linkedin.com/in/nicklasmillard/)。

# 看其他类似这样的故事。

*   [分支耦合依赖如何破坏你的软件质量](https://medium.com/p/d55833e17e74)
*   [依赖注入让你变得无比懒惰](/dependency-injection-has-made-developers-lazy-255afc5bedf7)