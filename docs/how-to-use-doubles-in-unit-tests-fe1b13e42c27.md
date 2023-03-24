# 如何在单元测试中使用 Doubles

> 原文：<https://levelup.gitconnected.com/how-to-use-doubles-in-unit-tests-fe1b13e42c27>

## 有时候替身是模仿的更好替代。

![](img/5b195525889015f69a661669e6e9f1a4.png)

图片由[类比](https://pixabay.com/users/analogicus-8164369/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4243189)来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4243189)

当您为某段代码编写单元测试时，您应该只测试一段代码，而不是它调用的代码。否则，您可能会重复您的测试工作。隔离代码的一种方法是模仿被调用的对象或方法。但是，如果您正在模仿一个对象，尤其是一个包含状态的对象，那么协调模仿代码可能会很困难。如果对象是一个接口的实现，你可以使用 doubles。

double 是一个行为类似于它的孪生兄弟的对象，只是它被简化了。数据库就是一个很好的例子。如果我们测试的代码需要进行添加、按 id 检索和按 id 删除的数据库调用，底层数据结构只需要是一个映射。即使您需要加倍的接口是广泛的，您只需要填写被测试代码使用的方法。

使用可以携带状态的 doubles 的好处之一是，我们可以通过以某种预期的顺序调用方法来测试一个类。这提供了我们不常想到的单元测试的好处之一，即作为如何使用一个类的例子。如果编程语言是基于类的，那么让单元测试也基于类是很有用的，即使是在混合的经典/函数环境中。基于类的单元测试应该能够以预期的方式使用被测试类的方法，提供一个使用模板。

最近，我必须完成一个代表银行的小型示例 REST 服务，包括用户、账户和余额。它是一个整体，所有的活动都由一个服务处理，逻辑大部分包含在一个类中。其余的类是配置类、数据类或简单的外观类，不需要测试。所以所有的测试都是在单个服务类上完成的。因为服务类依赖于数据库来保存方法调用之间的状态，所以我们想要模拟它，这样我们就可以以自然的方式进行测试，只调用服务的成员。

例如，我们有一个方法`getAccount`可以返回特定用户的账户信息。为了设置要获取的帐户，我们将使用方法`createAccount`(假设用户已设置好)下面是我为测试准备的内容:

```
[@Test](http://twitter.com/Test)
  public void testGetAccount() {
    System.out.println("getAccount");
    AccountPayload account = AccountPayload.builder()
      .currAmount(BigDecimal.valueOf(100, 2))
      .type("checking")
      .build();
    String userId = customers.get(0).getId();
    account = sut.createAccount(account, userId)
      .blockOptional(Duration.ofSeconds(1))
      .orElseThrow(() -> 
             new RuntimeException("result of save not found"));
    AccountPayload result = sut.getAccount(account.getId(), userId)
                .blockOptional(Duration.ofSeconds(1))
                .orElseThrow(() -> 
             new RuntimeException("result of get not found"));

    assertEquals(account.getCurrAmount(),result.getCurrAmount());
    assertEquals(account.getType(),result.getType());
    assertNotNull(result.getId());
    try { // user can only get their own accounts
      sut.getAccount(account.getId(), customers.get(1).getId())
                .blockOptional(Duration.ofSeconds(1))
                .orElseThrow(() -> 
             new RuntimeException("result of get not found"));
      fail("expected exception not thrown");
    } catch (AccountNotFoundException ex) {
      assertEquals("Account " 
         + account.getId() 
         + " not found for user " 
         + customers.get(1).getId(), ex.getMessage());
    }
    try { // unknown account returns reasonable error
      sut.getAccount("0", customers.get(1).getId())
        .blockOptional(Duration.ofSeconds(1))
        .orElseThrow(() -> 
             new RuntimeException("result of get not found"));
      fail("expected exception not thrown");
    } catch (AccountNotFoundException ex) {
      assertEquals("Account 0 not found for user " 
          + customers.get(1).getId(), ex.getMessage());
    }
    try { // unknown user
      sut.getAccount("", "0")
        .blockOptional(Duration.ofSeconds(1))
        .orElseThrow(() -> 
           new RuntimeException("result of get not found"));
      fail("expected exception not thrown");
    } catch (UserNotFoundException ex) {
    assertEquals("User 0 not found", ex.getMessage());
  }
}
```

值`sut`是“受测对象”,在每次测试前设置。给它贴上这样的标签有助于记住我们到底在测试什么，以及什么只是测试本身的一个工件。值 customer 也是在测试类之前设置的，并且是不可变的。因为这个服务假设用户是预先设置好的*，也许结合认证，我们可以从一个文件中加载样本用户。*

*该测试调用其中一个客户的`createAccount`，然后以四种不同的方式调用`getAccount`。第一个调用检查获取我们刚刚创建的帐户的正常条件。第二个呼叫是代表另一个客户完成的，并抛出`AccountNotFoundException`。第三个电话寻找一个假账户，再次抛出`AccountNotFoundException`。最后一个呼叫是代表一个假客户完成的，并抛出`UserNotFoundException`。最后一种情况可能永远不会发生，因为应该通过身份验证来检查客户，这应该会返回 400 错误。*

*当创建值`sut`时，我们也创建了它将使用的 doubles，而不是一个真正的数据库。它是这样设置的:*

```
*public class FinTechServiceTest {
    FinTechService sut;
    UserReactiveRepository userRepository = 
         new UserRepositoryDouble();
    TransferAuditReactiveRepository transferAuditRepository = 
         new TransferAuditRepositoryDouble();
    List<Customer> customers;

    public FinTechServiceTest() {
    }

    [@BeforeEach](http://twitter.com/BeforeEach)
    public void setUp() throws IOException {
        userRepository.deleteAll();
        transferAuditRepository.deleteAll();
        sut = new FinTechService(userRepository,
                            transferAuditRepository);
        sut.initializeUsers();
        customers = userRepository.findAll()
                .collectList()
                .block();
    }...*
```

*在这里，我们创建两个 double，它们代表两个数据库客户机(在上面的测试中，我们没有使用 transfer audit 客户机)，然后在每个测试之前，清除存储库，创建一个用这两个 double 构造的新服务对象，然后初始化并收集客户。因为我们使用的是 Spring 的构造函数初始化，而不是 AutoWire 注释，所以我们可以很容易地用 doubles 替换普通的数据库客户机。*

*最后，这是我们用于用户存储库的替身:*

```
*public class UserRepositoryDouble implements UserReactiveRepository {
  private final Map<String, Customer> data = new HashMap<>(); public Mono<Customer> save(Customer s) {
    Customer s1 = copy(s);
    data.put(s1.getId(),s1);
    return Mono.just(s1);
  } public Mono<Customer> findById(String id) {
    return Mono.justOrEmpty(data.get(id));
  } public Flux<Customer> findAll() {
    return Flux.fromStream(data.values().stream());
  } public Mono<Long> count() {
    return Mono.just(Long.valueOf(data.size()));
  } public Mono<Void> deleteAll() {
    data.clear();
    return Mono.empty();
  } public <S extends Customer> Flux<S> saveAll(Publisher<S> pblshr) {
    return Flux.from(pblshr)
                .map(c -> {
                    Customer s1 = copy(c);
                    data.put(s1.getId(),s1);
                    return (S)s1;
                });
  } private static Customer copy(Customer customer) {
    return Customer.builder()
                .accounts(new ArrayList(customer.getAccounts()))
                .id(customer.getId())
                .name(customer.getName())
                .build();
  }

}*
```

*因为该类实现了一个接口，所以我的 IDE 能够用抛出异常的默认实现来填充所有必要的方法。我所要做的就是为测试中使用的方法填充一点逻辑。为了简洁起见，我省略了上面代码中所有未实现的方法。*

*我之所以能够使用 double，是因为在这种情况下，接口的使用部分很小，并且它的实现很简单。如果界面有更复杂的方面，使用 double 会变得更加困难，嘲讽可能更合适。*

*我还认为，在微服务的世界里，单元测试的重要性已经降低了。测试微服务的最佳方式是作为一个运行单元，因为它们应该相对简单，并且容易部署到测试环境中。真正的困难在于微服务之间的协调，这也是我们应该集中测试精力的地方。但是，我仍然喜欢看到我的代码工作，单元测试是实现这一点的一种方式。*

*这是 GitHub 的存储库，包含所有代码:*

*[](https://github.com/rkamradt/fintechapi/tree/v0.1) [## rkamradt/fintechapi

### 一个带有 Spring Boot 的 Java API 示例。通过在…上创建帐户，为 rkamradt/fintechapi 开发做出贡献

github.com](https://github.com/rkamradt/fintechapi/tree/v0.1)*