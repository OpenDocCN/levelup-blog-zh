# “静态不好”、“异步代码快”等编程误区

> 原文：<https://levelup.gitconnected.com/only-interfaces-can-be-mocked-asynchronous-code-is-fast-and-other-programming-misconceptions-2e19b0e94a8>

## 你需要尽快打破它。

![](img/2dce4002c2599542e13a83c5266cd817.png)

乌代·米塔尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

软件开发是一个复杂的过程，有如此多的细微差别，以至于几乎每个开发人员在面向对象设计、单元测试、异步编程或其他领域都曾经或目前有一个或多个误解。

虽然一些误解可能是无辜的，但其他误解甚至可能会妨碍开发人员正确地完成工作，所以最好现在就消除它们。

# 只有接口才有可能模仿

类之间的紧密耦合会导致可测试性问题。让我们看一个演示紧耦合问题的例子:

```
public class Storage
{
    public string GetData() => "Data from the storage.";
}public class Parser
{
    public string Parse()
    {
        var storage = new Storage();
        var data = storage.GetData();
        **//Parse the data**
        return data;
    }
}
```

`Parser`类与`Storage`类紧密耦合，这使得很难在与外界隔离的情况下测试解析逻辑。

避免紧密耦合的常见解决方案是提取依赖关系的接口，并将其注入构造函数。

```
public class Parser
{
    private readonly IStorage _storage; public Parser(**IStorage** storage) => _storage = storage;
}
```

现在可以使用 [Moq](https://github.com/Moq/moq4/wiki/Quickstart) 库轻松模仿`GetData`方法，如下所示:

```
[Fact]
public void Test()
{
   var mock = new Mock<**IStorage**>();
   mock.Setup(m => m.GetData()).Returns("Data for unit test.");

}
```

Moq 框架生成一个实现`IStorage`接口的代理(模拟)对象。

一个常见的误解是只能为接口创建模仿。如果某些类是**公共的**并且它们被模仿的方法是**虚拟的**，那么模仿它们也是可能的。

```
public class Storage
{
    public **virtual** string GetData() => "Data from the storage.";
}[Fact]
public void Test()
{
   var mock = new Mock<**Storage**>();
   mock.Setup(m => m.GetData()).Returns("Data for unit test.");

}
```

Moq 框架将生成从`Storage`继承的代理对象，并覆盖`GetData`方法。

*“这是否意味着我不应该再使用接口了？”*

当然不是。接口支持应用程序中的松散耦合，这使得它在运行时非常灵活。然而，为已经有虚方法的类提取接口是一个多余的步骤。

# 将接口注入构造函数意味着依赖倒置

根据我的主观观察，依赖倒置原则最常被开发人员误解。

一种常见的面向对象编程技术是将接口注入到类构造函数中，这通常被视为遵循了依赖倒置原则。

但是，将接口注入构造函数并不一定意味着依赖倒置。

```
public class CreateUserCommandHandler
{
    private readonly ITcpClient _tcpClient; public CreateUserCommandHandler(ITcpClient tcpClient)
        => _tcpClient = tcpClient;
}
```

在上面的例子中，依赖是通过接口注入到构造函数中的，但是仍然违反了依赖反转原则。

高层策略`CreateUserCommandHandler`依赖于低层细节`ITcpClient`，这些细节表示中间缺少抽象。

依赖倒置的思想不是关于使用接口。你可以查看我的另一篇文章了解详情:

[](/you-are-simply-injecting-a-dependency-thinking-that-you-are-following-the-dependency-inversion-32632954c208) [## 你只是简单地注入一个依赖，认为你在遵循依赖倒置…

### 澄清差异。

levelup.gitconnected.com](/you-are-simply-injecting-a-dependency-thinking-that-you-are-following-the-dependency-inversion-32632954c208) 

# 异步代码比同步代码快

开发人员可以同步或异步运行方法。下面是同步执行的简单示例:

```
public class OrderRepository
{
    private readonly DbContext _context; public OrderRepository(DbContext context) => 
        _context = context;

    public void Insert(Order order)
    {
        _context.Orders.Add(order);
        _context.SaveChanges(); **// <- Synchronous call**
    }
}
```

代码以同步方式编写:方法签名中没有`async`关键字，方法`SaveChanges`是同步的。执行`Insert`操作的线程将在`SaveChanges`方法期间被阻塞。

代码可以很容易地用异步方式重写，因为`SaveChanges`有一个名为`SaveChangesAsync.`的异步实现

```
public async Task Insert(Order order)
{
    _context.Orders.Add(order);
    _context.**SaveChangesAsync**(); 
}
```

异步编程允许我们在线程发起类似数据库调用的操作后解除对线程的阻塞，这样它就可以服务于其他请求。

异步调用可以减少`Insert`方法的执行时间吗？不会。如果同步`Insert`方法需要 1 秒钟将订单插入数据库，那么异步`Insert`方法需要同样长的时间。

那么我们通过编写异步代码实现了什么呢？我们实现了可扩展性。当数据库保存更改时，未阻塞的线程可以为应用程序的其他请求提供服务。使用异步编程模型，应用程序可以处理更多的请求。

# 延迟加载总能解决性能问题

实体框架核心中的延迟加载允许您推迟加载对象的某些部分，直到客户端代码请求它们。

```
public class User 
{     
    public int Id { get; set; }     
    public string Name { get; set; }      
    public virtual ICollection<UserDetail> UserDetails { get; set; } 
}
```

关键字`virtual`具有与延迟加载相关的魔力。从数据库中检索博客记录不会导致读取相关的`UserDetails`记录，直到调用代码明确请求它们。事实上，延迟加载可以提高应用程序的性能，但另一方面，它会导致很大的性能问题。

```
var context = new DbContext();var users = context
       .Users
       .Where(u => u.IsActive)
       .ToList(); //Database call hereforeach (var user in users)
{
    var details = user.UserDetails; //Database call here
    //logic
}
```

上面的例子演示了 N+1 问题，它导致每次迭代都要往返一次数据库。将有一个获取活动用户的数据库调用和一个获取列表中每个用户的详细信息的数据库调用。这极大地影响了应用程序的性能。

为了避免性能问题，应该在一次数据库调用中加载数据。

# 静态类是不好的

静态类的主要“问题”是与消费者的紧密耦合和可测试性问题。

然而，静态类就像任何其他编程工具一样，既不好也不坏。它们可以简单地被正确或错误地使用。

什么时候静态类是好的？

*   静态类是无状态的。
*   静态类总是产生确定性的结果(静态类包含纯函数)。
*   没有必要脱离静态依赖来测试类。

[](/static-classes-and-methods-are-they-terrible-c9c611a921b3) [## 静态类和方法——它们很糟糕吗？

### 从设计角度探索 C#静态类和方法。

levelup.gitconnected.com](/static-classes-and-methods-are-they-terrible-c9c611a921b3) 

当静态类不好的时候。

*   静态类声明应用程序可以改变，因此它会意外地影响应用程序的其他部分。
*   静态类用于设计处理外部环境(如数据库、文件系统等)的依赖关系。在这种情况下，对使用这种静态类的应用程序的逻辑进行单元测试就会出现问题。

# 结论

开发人员头脑中错误概念的寿命应该尽可能的短。通过阅读基础书籍、好文章和所有其他可用的权威知识来源来分解它们。

## 我的其他文章

[](/5-ways-to-create-an-object-in-c-and-when-to-choose-which-one-4aabea5c3e40) [## 用 C#创建对象的 5 种方法以及何时选择哪一种

### 当创建一个对象时，像建筑师一样思考。

levelup.gitconnected.com](/5-ways-to-create-an-object-in-c-and-when-to-choose-which-one-4aabea5c3e40) [](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [## 面向技术领导者和资深人士的 50 个软件工程最佳实践

### 最佳工程师的最佳实践。

levelup.gitconnected.com](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [](/treat-if-else-as-a-code-smell-until-proven-otherwise-3bd2c4c577bf) [## 将 If-Else 视为代码气味，直到被证明并非如此

### C#中 If-Else 重构技术

levelup.gitconnected.com](/treat-if-else-as-a-code-smell-until-proven-otherwise-3bd2c4c577bf)