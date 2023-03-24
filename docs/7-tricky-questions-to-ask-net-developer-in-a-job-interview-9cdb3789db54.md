# 要问的 7 个棘手问题。NET 开发人员在工作面试

> 原文：<https://levelup.gitconnected.com/7-tricky-questions-to-ask-net-developer-in-a-job-interview-9cdb3789db54>

## 带着答案。

![](img/9b673a00eef6a8d8a51b7d7d18ed332b.png)

由 [Van Tay Media](https://unsplash.com/@vantaymedia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

对于那些希望扩展面试问题列表的面试官，或者那些希望在下一次面试前变得更加自信的受访者，或者所有人。NET 开发人员希望学习新的东西，我准备了一些有趣的问题。

# 1.单例对象可以依赖于限定了作用域的/每请求的对象吗？

或者，这个问题可以换成“下面这段代码能工作吗？”：

```
public class Controller
{
    public Controller(UserService userService) { }
}public class UserService
{
    public UserService(IUserRepository userRepository) { }
}public class UserRepository : IUserRepository
{
}**//DI registration:** public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<UserService>();
    services.AddScoped<IUserRepository, UserRepository>();
}
```

这段代码可以编译，但是开发人员会在运行时得到依赖注入容器抛出的异常。

代码示例演示了名为[受控依赖](https://blog.ploeh.dk/2014/06/02/captive-dependency/)的问题。当较高级别的服务`(UserService)`比依赖的较低级别的服务`(IUserRepository).`具有更长的生存期时，问题就出现了

有两种方法可以解决这个问题。第一种方法是重新配置对象的生存期，以便更高级别的服务总是具有与其依赖项相同或更短的生存期。例如，`UserService`和`UserRepository`都可以有一个限定范围的(每个请求)生存期。或者`UserService`可以具有短暂的生存期，而`UserRepository`具有限定范围的生存期。

然而，在大型代码库中简单地更改对象的生存期范围有时会很困难，因为这会对整个应用程序的行为产生不良影响。

在不改变生存期范围的情况下解决生存期较短的依赖项的一个有效方法是使用`IServiceScopeFactory.`

```
public class UserService
{
    private readonly IServiceScopeFactory _scopeFactory; public UserService(IServiceScopeFactory scopeFactory) =>   
        _scopeFactory = scopeFactory; public void DoSomething()
    {
        using (var scope = _scopeFactory.CreateScope())
        {
            **//Resolving scoped dependency**
            var userRepository = scope.
                    ServiceProvider.   
                    GetRequiredService<IUserRepository>(); //Use repository instance
        }
    }
}
```

🔔 [**现在就订阅**](https://esashamathews.medium.com/subscribe) **，所以大家不要错过我接下来的文章。**

# 2.让 DbContext 成为 web 应用程序中的单例是个好主意吗？

实体框架核心中的`DbContext`类负责与数据库交互。因为通常一个服务器实例与一个数据库实例通信，所以为 DI 容器中的`DbContext`选择一个单独的生存期范围似乎是一个好主意。

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<DbContext>();
}
```

实现不是线程安全的。实体框架核心将检测同一个`DbContext`是否被多个线程访问，并抛出一个异常。让`DbContext`成为单例将需要大量的线程同步逻辑。但是添加同步逻辑会降低 web 应用程序中并行用户请求的执行速度。

`DbContext`在单线程应用中可以是 singleton。对于默认情况下多线程的 web 应用程序，作用域或每请求生存期是`DbContext`类的一个好选择。

# 3.如何实现一个可以创建任何对象的深层副本的方法？

C#开发人员可以使用`MemberwiseClone`方法来创建对象的浅层副本。但是，创建深层副本通常需要一些自定义实现。

```
public T CreateDeepCopy<T>(T original)
{
    **//Implementation?**
}
```

至少有办法实现一个方法，可以创建任意对象的深度副本并返回给调用者:**序列化**和**反射**。

如果选择序列化方法，您将需要使用二进制、JSON 或其他类型的序列化在内存中序列化输入对象。然后，您需要将流反序列化回对象。这个反序列化的对象将是原始对象的深层副本。

[](/5-ways-to-clone-an-object-in-c-d1374ec28efa) [## 在 C#中克隆对象的 5 种方法

### 各有利弊

levelup.gitconnected.com](/5-ways-to-clone-an-object-in-c-d1374ec28efa) 

使用反射创建对象的深层副本将比序列化有更复杂的实现。然而，使用反射，您不需要用`[Serializable]`属性标记域类，也不需要用序列化提供者需要的其他东西污染它们。

# 4.字符串的不变性有什么利弊？

任何修改字符串的尝试都会导致创建该字符串的新实例。这种行为被称为不变性。每个人都知道不可变性会导致性能问题，因为频繁修改字符串会导致大量的冗余分配。

好处呢？最根本的优势是副作用是不可能的。

*   字符串是线程安全的。每个试图修改字符串实例的线程都会得到它的一个副本。不需要同步对字符串的访问、创建临界区等等。
*   字符串滞留机制是一种内存优化，如果字符串是可变的数据类型，这种机制将不起作用。

此外，使用字符串或其他不可变类型的代码更容易阅读。

```
string message = "Hello";
DoSomethingWithMessage(message);
Console.Write(message);
```

在阅读这样的代码时，开发人员不需要分析`DoSomethingWithMessage`方法的实现细节来发现`message`是否不同于“Hello”或 no。

不可变数据类型减少了开发人员阅读代码的时间。

# 5.如何用两行代码实现 Singleton 模式？

C#开发人员可以用多种方式实现单例模式。最短的实现如下所示:

```
public class Singleton
{
    private Singleton() { }    
    public static readonly Singleton Instance = new Singleton();
}
```

`new Singleton()`保证在应用程序的生命周期中只执行一次，因为编译器把它放在一个静态构造函数中。此外，静态构造函数在默认情况下是线程安全的。

您可以在我的另一篇文章中找到实现单例模式的其他方法，这些方法各有利弊:

[](/5-ways-to-implement-the-singleton-design-anti-pattern-in-c-68bb664c31f2) [## 在 C#中实现单例设计反模式的 5 种方法

### 各有利弊

levelup.gitconnected.com](/5-ways-to-implement-the-singleton-design-anti-pattern-in-c-68bb664c31f2) 

# 6.如何实现自定义 HashSet <t>数据结构？</t>

这个问题可以考验开发者的数据结构知识，以及用 C#代码实现的能力。

`HashSet<T>`收藏由提供。NET 基类库存储唯一的项。搜索(包含)、插入或删除等操作以常数时间 O(1)执行。

如何实现自己的散列集数据结构，该结构将表现为现有的`HashSet<T>`实现？

```
public class CustomHastSet<T>
{
    public bool Add(T item)
    { 
        **//Implementation?**
    } public bool Contains(T item)
    {
        **//Implementation?**
    }
}
```

自定义散列集数据结构可以作为`Dictionary<TKey, TValue>`数据结构的包装器来实现，它必须将值的散列代码存储为字典键，并将值本身存储为字典值。

```
public class CustomHastSet<T>
{
    private readonly Dictionary<int, T> _items = 
           new Dictionary<int, T>() public bool Add(T item)
    {
        var hashCode = item.GetHashCode(); if (!_items.ContainsKey(hashCode))
        {
            _items.Add(hashCode, item);
            return true;
        } return false;
    } public bool Contains(T item)
    {
        var hashCode = item.GetHashCode();
        bool contains = _items.ContainsKey(hashCode);
        return contains;
    } **//Other methods**
}
```

*该实施不包括两个不同值可能具有相同散列码的情况。你如何支持这一点？*

# 7.可以在 lock 语句中包装异步代码吗？

`lock`关键字用于创建一个只有一个线程可以进入的临界区。

有时有必要为异步代码创建一个临界区，以避免潜在的并发问题。

```
private static object _lock = new object();public async Task DoAsync()
{
    lock (_lock)
    {
        await Task.Delay(1);
    }
}
```

然而，上面的代码甚至无法编译，因为编译器不允许在`lock`语句体中使用`await`关键字。这样做是为了[防止死锁问题](https://stackoverflow.com/questions/7612602/why-cant-i-use-the-await-operator-within-the-body-of-a-lock-statement/7612714#7612714)。

为异步代码创建临界区的一个可行解决方案是使用其他同步原语，如`SemaphoreSlim`。

```
private static SemaphoreSlim _lock = new SemaphoreSlim(1,1);public async Task DoAsync()
{
    await _lock.WaitAsync(); try
    {
        await Task.Delay(1);
    }
    finally
    {
        _lock.Release();
    }
}
```

面试愉快！

## 我的其他文章

[](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [](/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b) [## 5 GitHub。净回购，让你的技术技能更上一层楼

### 亲身体验 GitHub 资源库。

levelup.gitconnected.com](/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b) [](/5-ways-to-create-an-object-in-c-and-when-to-choose-which-one-4aabea5c3e40) [## 用 C#创建对象的 5 种方法以及何时选择哪一种

### 当创建一个对象时，像建筑师一样思考。

levelup.gitconnected.com](/5-ways-to-create-an-object-in-c-and-when-to-choose-which-one-4aabea5c3e40)