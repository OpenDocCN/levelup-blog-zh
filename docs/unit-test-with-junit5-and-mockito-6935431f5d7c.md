# 用 Junit5 和 Mockito 进行单元测试

> 原文：<https://levelup.gitconnected.com/unit-test-with-junit5-and-mockito-6935431f5d7c>

![](img/7e6fda3d8ae4e98919b10353f618caa3.png)

照片由[www.arhohuttunen.com](http://www.arhohuttunen.com)拍摄

我在上一篇文章中讨论了单元测试的重要性。你可以在这里找到帖子[。](https://medium.com/@ibrahimates/importance-of-unit-testing-a55c1be07785)

今天，我将用我的一些应用程序代码提供一些测试用例。

我开发了 Springboot Restful API，它是关于用 Springboot 开发的自动售货机的。我刚刚添加了我的应用程序产品的控制器、服务和存储库类的一些代码，下面是它们的测试。

ProductController 类中有一个分发产品的终结点；

在开始编写测试用例之前，我将 spring-boot-starter-test 库添加到 pom.xml 中；

我知道这篇文章是关于单元测试的，但是对于控制器类，我更喜欢写集成测试而不是单元测试。

JUnit5 附带了 **@ExtendWith** ，而不是之前版本 JUnit 中使用的 **@RunWith** 。

```
@ExtendWith(SpringExtension.class)
class ProductControllerTest { 
   … 
}
```

此外，为了创建类和方法的模拟实例，我使用了以下注释。

**@InjectMocks，@Mock，MockMvc**

注意:差值`*@Mock and @InjectMocks*`在[栈溢出](https://stackoverflow.com/a/16467893/2152509)中给出为:

> `*@Mock*` *创造一种嘲弄。* `*@InjectMocks*` *创建该类的一个实例，并将使用* `*@Mock*` *(或* `*@Spy*` *)注释创建的模仿注入该实例。*

**MockMvc** 用于 Spring MVC 测试。它封装 web 应用程序 beans 并模拟它们，使它们为测试做好准备。

我在测试类的顶部添加了模拟类定义，如下所示；

```
[@InjectMocks](http://twitter.com/InjectMocks)
private ProductController productController;[@Mock](http://twitter.com/Mock)
private ProductService productService;private MockMvc mockMvc;
```

在所有测试用例执行之前，我用 MockMvcBuilders 初始化了 *mockMvc* 。为此，我在每个前使用了**@。这个注释是 JUnit5 附带的，在 JUnit4 中使用的是**之前的这个**@。**

```
[@BeforeEach](http://twitter.com/BeforeEach)
public void setup() {
 mockMvc = 
           MockMvcBuilders.standaloneSetup(productController)
           .build();
}
```

设置之后，我写了我的第一个测试用例。

**ArgumentCaptor** 在我们试图访问和捕获被模仿方法的参数时使用。

为了清晰的编码和更好的可读性，我通常把它分成三个注释部分:

*   *//给定* : *提供初始输入数据*
*   *//何时:执行待测试的类方法*
*   *//然后:验证检查断言*

所有 ProductControllerTest 类看起来都像这样；

然后我开发了 ProductService 类，它有一个公共方法和一个私有方法；

我一直在写第一个快乐路径测试用例。我使用 Lombok builder 用给定的一些数据构建了一个产品和 *productRequest* 对象。

> *Lombok 的* [*@Builder*](https://projectlombok.org/features/Builder) *是一个使用* [*构建器模式*](https://www.baeldung.com/creational-design-patterns#builder) *而无需编写样板代码的有用机制。我们可以将这种注释应用于一个类或一个方法。*

当用于模仿外部类方法时，我使用了**。然后我调用了我的类方法。**

通过**验证**，我验证了调用了具有给定 id 的*产品存储库*。如果它没有被调用，那么我的测试用例将会失败。

我证实了结果和我的预期一样。如果其中任何一个不相等，那么我的测试用例就会失败。

在愉快路径测试之后，为了涵盖所有其他案例，我在 ProductServiceTest 类中编写了其他测试案例，例如；

最后，我想添加一个带有测试类的存储库。在我的存储库类中，我有一个方法 *updateName* ，如下所示。

那么，我们如何测试我们的存储库类方法呢？因为将您的 all 数据库连接添加到您的测试文件夹并不是一个好的选择，所以我们需要在这里模拟数据库。为此，我添加了一个 H2 数据库(带有测试范围)依赖项来测试我的存储库类。

依赖关系应该是这样的；

正如我之前提到的，我有一个更新方法。所以，我写了一个测试方法。

对于存储库测试，我们需要 **TestEntityManager** *，它是 JPA EntityManager 的替代品，提供编写测试时使用的方法，比如 find 和 persist* 。

这里我需要 **persistAndFlush()** 方法来保存一个模拟记录。

你可以在我的 [GitHub](https://github.com/atesibrahim/vending-machine) 页面找到完整的代码。

希望有帮助。

感谢阅读…

**参考文献:**

 [## ArgumentCaptor (Mockito 2.2.7 API)

### Mockito 以自然 java 风格验证参数值:通过使用 equals()方法。这也是推荐的方式…

site.mockito.org](https://site.mockito.org/javadoc/current/org/mockito/ArgumentCaptor.html) [](https://www.journaldev.com/21892/mockito-argumentcaptor-captor-annotation) [## 莫奇托 ArgumentCaptor，@Captor Annotation - JournalDev

### Mockito ArgumentCaptor 用于捕获被模仿方法的参数。ArgumentCaptor 与 Mockito verify()一起使用…

www.journaldev.com](https://www.journaldev.com/21892/mockito-argumentcaptor-captor-annotation) [](https://stackoverflow.com/a/16467893/2152509) [## @Mock 和@InjectMocks 的区别

### 这是一个关于@Mock 和@InjectMocks 如何工作的示例代码。假设我们有游戏和玩家类。班级游戏{私人…

stackoverflow.com](https://stackoverflow.com/a/16467893/2152509) 

[https://www.baeldung.com/lombok-builder](https://www.baeldung.com/lombok-builder)

# 分级编码

感谢您成为我们社区的一员！更多内容请参见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)