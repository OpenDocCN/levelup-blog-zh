# 处理继承类中的依赖注入

> 原文：<https://levelup.gitconnected.com/handling-dependency-injection-in-inherited-classes-d7ae78952f1a>

![](img/81f49166ad5fc84d3206d44b0f7054e2.png)

依赖注入(DI)是一件很棒的事情。只需将您的依赖项作为一个参数添加到您的类的构造函数(最常见的),将它注册到您的 DI 容器中，然后您就可以离开了——DI 容器将管理剩下的工作。DI 的一些主要好处是:更好的可测试性、更好的可维护性和更好的可重用性。

```
// without DI
public class OrderController : Controller
{
    public ActionResult Post(Order order)
    {
        using (var dbContent = new MyDbContext())
        {
            dbContext.Orders.Add(order);
            dbContent.SaveChanges(); return Ok();
        }
    } public ActionResult Get()
    {
        using (var dbContext = new MyDbContext())
        {
            var orders = dbContext.Orders.ToList(); return new JsonResult(orders);
        }
    }
}// with DI
public class OrderController : Controller
{
    private readonly MyDbContext _dbContext; public OrderController(MyDbContext dbContext)
    {
        _dbContext = dbContext;
    } public ActionResult Post(Order order)
    {
        _dbContext.Orders.Add(order);
        _dbContent.SaveChanges(); return Ok();
    } public ActionResult Get()
    {
        var orders = _dbContext.Orders.ToList(); return new JsonResult(orders);
    }
}
```

然而，我最近遇到了一个用例，在这个用例中，DI 真的很麻烦——继承类中的依赖注入。

# 问题是

我最近一直在使用[mediator](https://github.com/jbogard/MediatR)包，使用它的请求/请求处理程序模式在系统中发布命令(灵感来自[贾森·泰勒的干净架构解决方案](https://github.com/jasontaylordev/CleanArchitecture))。

我通常会创建一个 RequestHandler 基类，其中包含常见的依赖项和功能。然后，每个具体的请求处理程序都可以从这个基类继承。

```
public abstract class RequestHandler<TRequest, TResponse> : IRequestHandler<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    public RequestHandler(IApplicationDbContext dbContext)
    {
        DbContext = dbContext;
    } protected IApplicationDbContext DbContext { get; } public abstract Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken);
}public MyRequestHandler : RequestHandler<MyRequest, MyResponse>
{
    public MyRequestHandler(IApplicationDbContext dbContext) : base(dbContext) { } public override Task<MyResponse> Handle(MyRequest request, CancellationToken cancellationToken)
    {
        // handler logic
    }
}
```

当我想给基类添加更多的依赖项时，问题就来了。现在，我必须检查每一个具体的请求处理程序，并更新构造函数以获取新的依赖关系。幸运的是，如果我错过了一个，代码将不会编译，所以没有运行时错误的风险，但是必须更新每个请求处理程序仍然是令人难以置信的繁琐工作。此外，您可能会以非常大的构造函数而告终，这掩盖了类的意图。

```
public abstract class RequestHandler<TRequest, TResponse> : IRequestHandler<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    public RequestHandler(IApplicationDbContext dbContext, ICurrentUser currentUser)
    {
        DbContext = dbContext;
        CurrentUser = currentUser;
    } protected IApplicationDbContext DbContext { get; }
    protected ICurrentUser CurrentUser { get; } public abstract Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken);
}public MyRequestHandler : RequestHandler<MyRequest, MyResponse>
{
    public MyRequestHandler(IApplicationDbContext dbContext, ICurrentUser currentUser) : base(dbContext, currentUser) { } public override Task<MyResponse> Handle(MyRequest request, CancellationToken cancellationToken)
    {
        // handler logic
    }
}
```

# 解决方案—依赖关系聚合

这个问题的解决方法真的很简单。与其直接注入依赖项，不如创建一个包含依赖项的新类(称为聚合)并注入它。

```
public interface IDependencyAggregate 
{
    IApplicationDbContext DbContext { get; }
    ICurrentUser { get; }
}public class DependencyAggregate : IDependencyAggregate
{
    public DependencyAggregate(IApplicationDbContext dbContext, ICurrentUser currentUser)
    {
        DbContext = dbContext;
        CurrentUser = currentUser;
    } public IApplicationDbContext DbContext { get; }
    public ICurrentUser CurrentUser { get; }
}public abstract class RequestHandler<TRequest, TResponse> : IRequestHandler<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    public RequestHandler(IDependencyAggregate aggregate)
    {
        DbContext = aggregate.DbContext;
        CurrentUser = aggregate.CurrentUser;
    } protected IApplicationDbContext DbContext { get; }
    protected ICurrentUser CurrentUser { get; } public abstract Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken);
}public MyRequestHandler : RequestHandler<MyRequest, MyResponse>
{
    public MyRequestHandler(IDependencyAggregate aggregate) : base(aggregate) { } public override Task<MyResponse> Handle(MyRequest request, CancellationToken cancellationToken)
    {
        // handler logic
    }
}
```

现在，如果我想添加一个新的依赖项，我需要更改代码的地方只有 DependencyAggregate 类和 RequestHandler 基类(我不需要对继承的类做任何更改)。

# 结论

在这篇文章中，我描述了一个简单的方法来管理继承类中的依赖注入，通过创建一个依赖聚合类来注入基类。这确保了新的依赖关系可以很容易地引入，而不必对每个继承的类进行更改。

我发布的大部分内容都是关于全栈的。NET 和 Vue web 开发。为了确保你不会错过任何帖子，请关注这个博客并[订阅我的简讯](https://samwalpole.com)。如果你觉得这篇文章有帮助，请喜欢它并分享它。你也可以在推特上找到我。

*最初发表于*[*【https://samwalpole.com】*](https://samwalpole.com/handling-dependency-injection-in-inherited-classes)*。*