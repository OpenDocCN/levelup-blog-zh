# C#中实现开闭原则的 4 种方法

> 原文：<https://levelup.gitconnected.com/4-ways-to-achieve-open-closed-principle-in-c-d2d0ebea4b86>

## 利弊分析。

![](img/80f0e9d65df6d79cd29521055367f3f6.png)

照片由[大卫·兰格尔](https://unsplash.com/@rangel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

开闭原则表明，像方法、类或模块这样的软件组件应该对扩展开放，但对修改关闭。

开发人员一般在以下四种情况下遵循开闭原则**:**

*   **当添加到一个对象的行为不是它真正关心的时候。例如，域类不应该实现缓存、跟踪、日志和其他横切关注点。这些问题应该放在单独的类中。**
*   **当要添加的行为应该被系统中不同的类重用时。例如，如果开发人员将缓存逻辑直接放入`ProductRepository`类，就不可能为其他类型的存储库重用缓存。**
*   **当现有组件的行为由于其脆弱性而难以扩展时。在重用现有组件功能的新组件中实现新需求会更合理。这通常发生在旧的遗留系统上。**
*   **当根据一些用户设置、许可或其他条件应该可以启用/禁用新行为时。当然，开发人员可以将逻辑直接放在现有的组件中，并编写`if-else`分支，但是代码将很难测试和维护。**

**让我们来看看最简单的带有接口的类:**

```
public interface IService
{
   void DoSomething(int input);
}public class Service : IService
{
    public void DoSomething(int input)
    {
        throw new NotImplementedException();
    }
}
```

**在我们的例子中，我们需要用一个简单的功能来扩展`DoSomething`方法的行为，以跟踪方法调用。当然，这要用开合的方式。**

# **1.依赖注入**

**也许遵循开闭原则最明显的选择是将额外的行为放入一个单独的包装类中，该包装类实现与我们需要扩展的对象相同的接口。然后注入对象以扩展到包装器中。这实际上是一种装饰设计模式。**

```
public class ServiceTracer : IService
{
    private readonly IService _service; public Tracer(IService service) => _service = service; public void DoSomething(int input)
    {
        WriteLine($"DoSomething method, input = {input}");

        _service.DoSomething(input);    
    }
}
```

## **优点:**

*   **`ServiceTracer`包装器可以和任何实现`IService`接口的类型一起工作。**
*   **开发人员可以在依赖注入容器中动态启用或禁用包装器，而不必重新编译代码。**

## **缺点:**

*   **`ServiceTracer`只能用于实现`IService`接口的类型，不能被其他接口重用。**
*   **这种方法无法扩展受保护成员的行为。这只是为了装饰公众成员。**

# **2.扩展方法**

**新的行为可以放在扩展方法中。扩展方法应该实现跟踪功能，然后调用`IService`类型的原始方法。**

```
public static class Tracer
{
  public static void DoSomethingWithTracing
       (this IService service, int input)
  {
      WriteLine($"DoSomething method called, input = {input}"); service.DoSomething(input);
    }
}
```

## **优点:**

*   **以及在包装器的情况下，扩展方法将与实现`IService`接口的任何类型一起工作。**

## **缺点:**

*   **如果你想停止使用跟踪扩展，你只需要用`DoSomething`替换调用`DoSomethingWithTracing`，然后重新编译代码。**

# **3.属性**

**属性是将对象从它不应该关心的问题中分离出来的另一种方式。**

```
[PSerializable]
public class TracingAspect : OnMethodBoundaryAspect
{
    public override void OnEntry(MethodExecutionArgs args)
    {
        WriteLine($"{args.Method.Name} method, {args.Arguments[0]} - args.Arguments.GetArgument(0)");
    }
}
```

**`[TracingAspect]`只是一个. NET 属性，可以应用于这样的方法:**

```
public class Service 
{
   **[TracingAspect]**
   public void DoSomething(int input)
   {
      WriteLine("Base");
   }
}
```

**它是使用 [postsharp](https://www.postsharp.net/) 库实现的。**

## **优点:**

*   **`[TracingAspect]`属性可以应用于应用程序中的任何方法。**
*   **对于开发人员来说，应用属性是一个非常快速简单的操作。**

## **缺点:**

*   **为方法应用`[TracingAspect]`属性需要修改和重新编译代码。**
*   **在方法调用之前或之后执行属性需要使用第三方库或编写大量样板代码。**

# **4.遗产**

**最后一种方法是使用继承。虽然继承有许多限制，但它仍然有一些优势。**

```
public class ServiceTracer : Service
{
  public new void DoSomething(int input)
  { 
      WriteLine($"DoSomething method called, input = {input}"); base.DoSomething(input);
    }
}
```

## **优点:**

*   **基于继承的方法允许扩展受保护的方法的行为。**

## **缺点:**

*   **继承不能用于扩展密封类的行为。**
*   **如果需要多态行为，开发人员需要将`Service`类中的方法虚拟化。然而，当要扩展的类驻留在单独的库中时，这并不总是可能的。**
*   **使用继承，您只能扩展某些类，而不能扩展接口。**

# **摘要**

**由于各种原因:可测试性、可重用性、可配置性或其他原因，现有的软件组件应该关闭以进行修改。**

**开发人员至少有四种方法来扩展现有软件组件的行为:包装器(装饰器)、扩展方法、属性、继承。**

## **我的其他文章**

**[](/7-tricky-questions-to-ask-net-developer-in-a-job-interview-9cdb3789db54) [## 要问的 7 个棘手问题。NET 开发人员在工作面试

### 带着答案。

levelup.gitconnected.com](/7-tricky-questions-to-ask-net-developer-in-a-job-interview-9cdb3789db54) [](/fast-database-fast-application-useful-db-performance-optimization-techniques-34b6926d1196) [## 快速数据库—快速应用程序(有用的数据库性能优化技术)

### 了解加速关系数据库的最佳实践。

levelup.gitconnected.com](/fast-database-fast-application-useful-db-performance-optimization-techniques-34b6926d1196) [](/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0) [## 如何专业地对 Bug 修复进行代码审查

### 审查 bug 修复时要问的几个重要问题。

levelup.gitconnected.com](/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0)**