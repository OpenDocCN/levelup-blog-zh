# 我的建议。NET 代码-审查

> 原文：<https://levelup.gitconnected.com/my-tips-for-net-code-review-f1a47feece43>

代码审查在我们的日常开发中很重要。一些开发人员可能会花很多时间学习语言的新特性，DDD，分布式系统或一些新奇的东西，但我们应该记住的第一件事是我们需要编写健壮的，可维护的代码。这里是我最近的代码审查的一些提示，我希望对你有所帮助。

![](img/818e85bc2f44e1b3d607c52dc58918d4.png)

# NullReferenceException 系列

这个`NullReferenceException`真烦人。避免这种情况的最好方法是在使用变量之前检查它是否为空。以下是一些潜在的问题。我在这里还包括了其他的例外比如`ArgumentNullException`。

# 总是在声明集合时初始化它

一个常见的错误是，有时我们声明了一个集合，但在使用它之前没有初始化它。像这样:

```
List<Person> persons;
// Some logics
// If you use persons directly you may got a null object
```

所以更好的方法是在声明集合时总是初始化它:

```
List<Person> persons = new List<Persons>();
// Then you can do something as usual
```

尤其是如果我们有一个拥有自己私有领域的财产:

```
// It is good to initialize the private field when we declare it.
private ObservableCollection<Person> _personList = new ObservableCollection<Person>();
public ObservableCollection<Person> PersonList
{
     get => _personList;
     set => SetProperty(ref _personList, value);
}
```

# 不要从返回集合的方法中返回空值

另一个提示是，如果我们有一个返回集合的方法，不要返回`null`。相反，如果没有满意的元素，我们应该返回一个空集合。

```
public List<Person> GetPersons()
{
    // Some logic to retrieve the objects. If not found, then return an empty collection. DO NOT return null.
    return new List<Person>();
}
```

# `FirstOrDefault()`还是`First()`？

LINQ 有一些类似的方法，如`First()`和`FirstOrDefault()`。主要区别在于空集合的行为。如果集合为空或者没有满足条件的元素，那么`First()`将抛出`InvalidOperationException`。对于这种情况，`FirstOrDefault()`是安全的，因为它可以为空集合返回默认值，或者没有元素满足条件。但是我们应该真正清楚默认值是否为空——这是导致`NullReferenceException` - **引用和可空类型的默认值是** `**null**`的另一个原因。所以如果我们这样做会更好:

```
var firstPerson = persons.FirstOrDefault(x => x.Age > 18);
// Check if the variable is null
if (firstPerson != null)
{
    // Do something to the firstPerson
}
```

同样，`Single()`和`SingleOrDefault()`也有不同的结果。`Single()`在这些情况下抛出`InvalidOperationException`:

*   源集合为空
*   没有元素满足该条件
*   **多个元素**满足条件

对于`SingleOrDefault()`，如果集合为空或者没有找到满意的元素，也返回默认值。我们应该在使用返回值之前检查它是否为 null。

另一种处理空集合的方法是使用`DefaultIfEmpty()`扩展方法，如下所示:

```
List<int> numbers = new List<int> { };
// Setting the default value to 1 by using DefaultIfEmpty() in the query.
int firstNumber = numbers.DefaultIfEmpty(1).First();
// Now the firstNumber is 1, not 0.
```

参考消息:[可枚举。第一个](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.first?view=netcore-3.1&WT.mc_id=DT-MVP-5001643) [可枚举。FirstOrDefault](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.firstordefault?view=netcore-3.1&WT.mc_id=DT-MVP-5001643) ，[可枚举。单个](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.single?view=netcore-3.1&WT.mc_id=DT-MVP-5001643)，[可枚举。单一故障](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.singleordefault?view=netcore-3.1&WT.mc_id=DT-MVP-5001643)

# 检查字典中是否存在该关键字

`NullReferenceException`的一个可能原因与字典有关。我在代码审查中见过它很多次。除非我们 100%确定我们知道这个键存在于字典中，(例如，字典是硬编码的。)我们应该总是先检查一下。因为有时字典来自 API，或者它有其他依赖项。我们不能保证我们想要的键在字典中存在。所以试着用这种方式:

```
var persons = new Dictionary<string, Person> = new Dictionary<string, Person>();
// Add some values
if (persons.ContainsKey("Jack"))
{
    var person = persons["Jack"];
    // Do something
}
// Alternatively, we can use TryGetValue() method which checks and gets the value in one method:
if (persons.TryGetValue("Jack", out Person person))
{
    // Do something
}
```

如果我们不检查钥匙，我们可能会得到一个`KeyNotFoundException`。仅供参考:[字典< TKey，TValue >。](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2.item?view=netcore-3.1&WT.mc_id=DT-MVP-5001643)，[项字典< TKey，TValue >。ContainsKey(TKey)](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2.containskey?view=netcore-3.1&WT.mc_id=DT-MVP-5001643)

此外，当您将键/值对添加到字典中时，您应该检查字典中是否已经存在该键。

# `as`还是`is`？

当我们转换对象类型时，我们可以使用`as`，例如:

```
protected override void SetValueImpl(object target, SomeStatus value)
{
    var ctrl = target as ControlA;
    ctrl.Status = newValue;
}
```

`as`操作符将表达式的结果显式转换为给定的引用或可空值类型。即使转换不可能，它也不会抛出异常，但是在这种情况下，它会返回`null`。所以通常情况下，它比 [cast 表达式](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-expression?WT.mc_id=DT-MVP-5001643)更安全，但是我们仍然需要检查结果是否为`null`。

`is`操作符检查表达式结果的运行时类型是否与给定类型兼容。因此，使用以下代码更安全:

```
if (target is ControlA)
{
    var ctrl = target as ControlA;
    ctrl.Status = newValue;
}
```

从 C# 7 开始，我们可以使用`is`同时检查和转换类型:

```
if (target is ControlA ctrl)
{
    //You can use ctrl variable directly now because the result is already assigned to it
    ctrl.Status = newValue;
}
```

参考消息:[类型测试运算符和转换表达式](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast?WT.mc_id=DT-MVP-5001643)

# 检查来自深度睡眠模式的 DI 容器的实例

这个问题可能发生在移动设备上。如果我们对移动应用程序使用依赖注入，通常我们只是从 DI 容器中获取服务实例。但在移动设备上，我们需要处理太多棘手的情况，例如深度睡眠模式、后台任务和推送通知等。例如，如果用户点击通知来执行一些动作，DI 容器可能还没有初始化。换句话说，应用的生命周期可能不是我们预期的那样。我已经遇到了一些相关的问题，但我不想在这里展开太多，因为它很少发生在其他应用程序。

# 有没有更好的方法来代替`if`语句进行空值检查？

是的。您可以使用空条件运算符来简化它。用`?`把表达式短路就行了。例如:

```
A?.B?.Do(C)
```

如果`A`为空，链的其余部分不执行。如果`B`为空，则`Do(C)`不执行。

类似地，您可以使用`?[]`进行元素访问。`A?.B?[C]`例。即使`B`为空也是安全的。

调用 delegate/Action/Func 也是一个不错的选择。例如:

```
class Counter
{
    public event EventHandler ThresholdReached;
    protected virtual void OnThresholdReached(EventArgs e)
    {
        // Use ? to avoid null reference exception
        ThresholdReached?.Invoke(this, e);
    }
}
```

顺便说一下，如果它不是`null`，您可以使用空合并操作符`??`返回其左侧操作数的值；否则，它返回右操作数的值。例如，如果我们需要给一个变量赋值:

```
if (variable is null)
{
    variable = expression;
}
```

以下代码在 C# 8.0 中可用:

```
variable ??= expression;
```

参考消息:[成员访问运算符和表达式](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators?WT.mc_id=DT-MVP-5001643)

[零合并算子](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator?WT.mc_id=DT-MVP-5001643)

# 使用`TryParse()`，而不是`Parse()`

我认为大部分。NET 开发人员已经知道了这一点。

# 任务/异步/等待

你可以在 StackOverflow、GitHub 或其他人的博客上找到大量关于`Task`、`async`和`await`的帖子/问题/博客。我确实读过很多文章，但是我不会添加任何新的内容，我只想强调异步编程中的一些最佳实践。

# 避免使用`async void`方法，除非您将它用于事件处理程序

如果`async`方法可行，返回`Task`或`Task<T>`。如果你返回一个`void`方法，编译器不会抱怨，但是这样不好，因为任何异常都会被吃掉，所以你不能正确地捕捉它。

一个特殊的场景是创建一个`async`事件处理程序。通常事件处理程序不返回任何类型，它只返回`void`。但是请确保您已经在事件处理程序中处理了可能的异常。对于所有其他情况，不要使用`async void`方法。

# 避免将`async`和`sync`方法混合在一起

我们应该记住**始终是异步的**，这意味着我们不应该在大多数情况下混合异步和同步代码。更特别的是，**永远不要用** `**Task.Wait()**` **和** `**Task.Result**` **来阻塞异步代码**。`Task.Wait()`是同步的，可能会阻塞呼叫。如果我在代码审查中看到`Task.Wait()`，这无疑是一个强烈的信号，表明代码可能导致死锁。此外，死锁导致的错误很难跟踪，所以请不要再使用它。

# 返回任务，不返回等待

如果您已经有一个异步任务，并且您需要在另一个任务中调用它，只需返回该任务，而不是将其设为异步然后等待。例如:

```
public async Task<string> GetDataFromApiAsync()
{
    //Do some async jobs
}
​
// You can do it like this 
public async Task<string> GetDataAsync()
{
    return await GetDataFromApiAsync();
}
​
// But this one is better
public Task<string> GetData()
{
    // Just return the task
    return GetDataFromApiAsync();
}
```

每次我们将一个方法声明为 async 时，编译器都会创建一个状态机类来包装逻辑。所以如果你在方法中没有其他的`await`，请直接返回任务。这有助于我们不创建额外的资源来在不同的线程之间切换。

# 如果我们不得不阻止异步代码呢？

出于某种原因，我们必须将异步代码转换成同步代码，那么你最好使用`Task.GetAwaiter().GetResult()`。对于`Task.Wait()`来说也是一样，如果任务成功执行，好处是如果任务出错，它可以返回一个正常的异常。但是`Task.Wait()`将异常包装在一个`AggregateException`中，所以很难诊断这个问题。

# 使用`Task.WhenAll()`运行多个任务

如果您有多个任务要执行，并且它们之间没有依赖关系，您可以使用`await Task.WhenAll()`来并行运行它们。类似地，您可以使用`Task.WhenAny()`等待任何任务完成，然后继续下一个代码。

# 使用`ConfigureAwait(false)`避免死锁

[SynchronizationContext](https://docs.microsoft.com/en-us/dotnet/api/system.threading.synchronizationcontext?view=netcore-3.1&WT.mc_id=DT-MVP-5001643) 类提供了在各种同步模型中传播同步上下文的基本功能。基本上，使用`ConfigureAwait(false)`可以提高性能，避免死锁。总的指导方针是:

*   不要在应用级代码中使用`ConfigureAwait(false)`。
*   在通用库代码中使用`ConfigureAwait(false)`。

换句话说，如果你正在开发一个 Windows 窗体/WPF/ASP。NET 核心应用程序，通常你不需要使用它。但是如果你开发一个通用的库，你最好使用它，因为这个库不关心环境，所以不需要保持相同的`SynchronizationContext`。

我强烈推荐以下关于任务/异步/等待的文章:

*   [Async/Await —异步编程的最佳实践](https://docs.microsoft.com/en-us/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming?WT.mc_id=DT-MVP-5001643)—Stephen Cleary
*   [异步等待](https://blog.stephencleary.com/2012/02/async-and-await.html)斯蒂芬·克利里
*   Stephen Toub 的[异步/等待常见问题解答](https://devblogs.microsoft.com/pfxteam/asyncawait-faq/?WT.mc_id=DT-MVP-5001643)
*   Stephen Toub 的[配置等待常见问题解答](https://devblogs.microsoft.com/dotnet/configureawait-faq/?WT.mc_id=DT-MVP-5001643)

# 例外

所有的应用程序都可能有例外。但是一个设计良好的应用程序可以处理异常和错误以防止崩溃。一些常见问题是:

# 抛出异常，不返回错误代码。

例外并不可怕。我见过一些代码试图隐藏异常，然后返回错误代码。这样不好，因为调用者可能不会检查返回代码。异常确保可以正确处理故障。

# 尽可能使用标准异常类型

。NET 框架已经提供了很多异常类型，例如`ArgumentException`、`ArgumentNullException`、`InvalidOperationException`等。仅当预定义的异常类型不适用时，才引入新的异常类型。

# 为自定义异常提供附加属性

如果您设计了自定义异常，请将附加信息附加到该异常，这对诊断很有用。例如，`ArgumentNullException`具有参数名称属性，`FileNotFoundException`具有`FileName`属性。信息越多，调试就越容易。

# 不要吃 try-catch 块中的异常

我看过下面的代码:

```
try
{
    // Do something
}
catch (Exception ex)
{
    // Log the exception
    throw ex;
}
```

这并不好，因为它会重置堆栈跟踪。正确的做法是:

```
try
{
    // Do something
}
catch (Exception ex)
{
    // Log the exception
    throw;
}
```

用`throw`保持原来的栈迹即可。所以打电话的人会知道发生了什么。此外，您可能会看到以下代码:

```
catch (Exception ex)
{
    throw new MyException("error occurs", ex); // This will wrap the original stack trace
    //or
    throw new MyException("error occurs"); // This will replace the original stack trace
}
```

所以请确保正确地抛出异常。不要吃它们！

我推荐你阅读下面的文章:

*   [例外情况的最佳实践](https://docs.microsoft.com/en-us/dotnet/standard/exceptions/best-practices-for-exceptions?WT.mc_id=DT-MVP-5001643)
*   [c#中 Throw 和 Throw ex 的区别](https://www.c-sharpcorner.com/UploadFile/a777ce/difference-between-throw-and-throw-ex-in-C-Sharp/)

# HttpClient

说实话，这个`HttpClient`班在。NET 还不够好，因为每天都有很多开发人员在寻找如何正确使用它。所以这里有一些建议:

# 不要使用`using`显式地处理`HttpClient`实例

我认为很多。NET 开发人员已经知道了这一点。这是一些初级开发人员的常见错误，因为`HttpClient`实现了`IDisposable`接口。此外，您可能会在 Microsoft 官方文档的一些代码示例中找到以下代码:

```
using (var httpClient = new HttpClient())
{
    // Do something
}
```

这是可以理解的，因为它是一个`IDisposable`对象，我们应该使用`using`语句。但是对于这种情况，上面的用法是完全错误的。我们应该为整个应用程序共享一个实例，而不是为每个请求创建一个新的实例。因为即使您处置了`HttpClient`实例，底层的套接字连接也不能立即被处置(通常，在 Windows 上它会被保持 240 秒),所以如果您一次又一次地创建新的实例，可用的套接字端口将很快耗尽。

正确的做法是为`HttpClient`保留一个 singleton 实例。可以使用静态实例或者使用单例模式来维护`HttpClient`实例。但是我们还有另一个问题。

# `HttpClient`不考虑 DNS 更改

对于大多数场景，为所有请求维护一个单一实例就足够了。英寸 NET Framework 4.x，`HttpClient`不考虑 DNS 更改。换句话说，如果端点更改了 DNS 记录(当我们执行蓝/绿部署时可能会发生这种情况)，当前的`HttpClient`实例无法检测到这种更改，因此它将被阻塞，直到套接字连接关闭。在最近。网芯，已经改进了。

仅供参考: [Singleton HttpClient？小心这种严重的行为以及如何修复它](http://byterot.blogspot.com/2016/07/singleton-httpclient-dns.html)

[单例 HttpClient 不支持 DNS 更改](https://github.com/dotnet/runtime/issues/18348)

# 指定`HttpHeaders`为`HttpClient`还是`HttpRequestMessage`？

出于某种原因，我们需要指定`HttpHeaders`。我们可以通过两种方式做到这一点:

*   使用`HttpClient.DefaultRequestHeaders.Add()`更新`HttpClient`实例的标题。
*   使用`HttpRequestMessage.Headers.Add()`更新每个请求的标题。

第二种方式更好。因为通常我们会重用 singleton `HttpClient`实例。即使`HttpClient`是线程安全的，如果我们频繁改变`DefaultRequestHeaders`集合，也可能导致下面的`InvalidOperationException`:

```
Operations that change non-concurrent collections must have exclusive access. A concurrent update was performed on this collection and corrupted its state. The collection's state is no longer correct.
```

原因是`HttpClient`的`DefaultRequestHeaders`属性不是线程安全的。当有未完成的请求时，不应该修改它。仅供参考: [HttpClient。默认请求标题](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient.defaultrequestheaders?view=netcore-3.1?WT.mc_id=DT-MVP-5001643)

# 重试请求

HTTP 请求可能会失败，所以通常我们需要使用 **Polly** 来重试失败的请求。因此，您可能会看到另一个例外:

```
InvalidOperation exception: The request message was already sent. Cannot send the same request message multiple times.
```

这是因为 Polly 只是重试请求，而不是改变它。然而，如果`HttpClient`检查到它发送了相同的请求，它将抛出上述异常。解决方案很简单——只需创建一个`clone()`方法来创建请求的另一个副本:

```
public static HttpRequestMessage Clone(this HttpRequestMessage req)
{
    HttpRequestMessage clone = new HttpRequestMessage(req.Method, req.RequestUri);
​
    clone.Content = req.Content;
    clone.Version = req.Version;
​
    foreach (KeyValuePair<string, object> prop in req.Properties)
    {
        clone.Properties.Add(prop);
    }
​
    foreach (KeyValuePair<string, IEnumerable<string>> header in req.Headers)
    {
        clone.Headers.TryAddWithoutValidation(header.Key, header.Value);
    }
​
    return clone;
}
```

参考消息:[Polly 如何防止 InvalidOperation 异常 SendAsync](https://github.com/App-vNext/Polly/issues/313)

英寸 NET Core 2.1，微软提供`HttpClientFactory`试图解决一些问题。它更好，但我不喜欢的一点是它与微软的 DI 紧密结合。下面是 GitHub 上一个有趣的线程:[使用 HttpClientFactory 而不依赖注入](https://github.com/dotnet/extensions/issues/1345)。

# 收藏/列表

. NET 中有太多关于`Collections` / `List`的话题，但我只想在这里强调以下提示:

# 总是在声明集合时初始化它

哦，我刚刚意识到我在这篇文章的开头提到过它。所以请记住这一点，因为当我做代码审查的时候，我已经在这个问题上挣扎过很多次了。

# `ToList()`首先还是直接用`foreach`代替`IEnumerable`？

我相信你已经看到了下面的代码:

```
// GetList() will return an IEnumerable object
foreach (var item in GetList())
{
    await HandleItemAsync(item);
}
```

我们确切地知道，当我们对一个`IEnumerable`对象使用`foreach`时，它将遍历整个集合。对于大多数场景，这一个也是一样的:

```
foreach (var item in GetList().ToList())
{
    await HandleItemAsync(item);
}
```

不同之处在于，当您使用`IEnumerable`时，编译器有机会将工作推迟到以后，有可能优化方式。如果使用`ToList()`，编译器会强制立即得到结果。一个例子是，当我们从数据库中查询数据时，我们通常有 LINQ 条件，所以最好推迟评估，直到我们执行它。但在这种情况下，我们应该意识到`foreach`体内的`async`方法。考虑这个场景:假设我们有一个 Azure blob。我们需要获取 blob 存储中的所有文件，然后删除它们。所以代码可能是这样的:

```
var files = container.ListBlobs(null, true, BlobListingDetails.None);// List all the files in the blob. It will return an IEnumerable object.
foreach (var file in files)
{
    await file.DeleteIfExistsAsync();
}
```

好像不错。但风险在于这是一个云应用。`ListBlobs()`如果无法在指定持续时间内完成，方法可能会超时。因此，如果有一堆文件要删除，对于`ListBlobs()`方法来说会花费相当多的时间。然后你会得到一个超时异常。

所以安全的方法是在进行迭代之前使用`ToList()`:

```
var files = container.ListBlobs(null, true, BlobListingDetails.None).ToList();// List all the files in the blob then convert to a List.
foreach (var file in files)
{
    await file.DeleteIfExistsAsync();
}
```

现在你马上查询文件列表。那么就不需要担心超时问题了。所以不好说哪种方法更好。看情况。

# IEnumerable 的可能多重枚举

如果使用 Resharper，您可能会看到此警告。以下代码来自 Jetbrains:

```
IEnumerable<string> names = GetNames();
foreach (var name in names)
    Console.WriteLine("Found " + name);
var allNames = new StringBuilder();
foreach (var name in names)
    allNames.Append(name + " ");
```

假设`GetNames()`返回一个`IEnumerable<string>`，代码在两个`foreach`语句中进行两次枚举。如果`GetNames()`查询数据库或者调用一个 API，这两个调用可能会得到不同的结果，因为原始数据已经被其他进程改变了。所以可以通过将`IEnumerable<string>`转换为`List`来解决:

```
List<string> names = GetNames().ToList();
```

所以这两个枚举会得到相同的结果。

# 并发收款

`List`和`Dictionary`不是线程安全的。考虑在多线程情况下使用并发收集。你可以在这里找到更多信息:[系统。Collections.Concurrent 命名空间](https://docs.microsoft.com/en-us/dotnet/api/system.collections.concurrent?view=netcore-3.1?WT.mc_id=DT-MVP-5001643)

# 多方面的

# `==`还是`Equals`？

它们都用于比较两个值类型数据项或引用类型数据项。不同的是，如果它们引用同一个对象，`==`将返回`true`。如果内容相同，`Equals()`返回`true`。所以他们不一样。仅供参考:

*   [对象。等于方法](https://docs.microsoft.com/en-us/dotnet/api/system.object.equals?view=netcore-3.1?WT.mc_id=DT-MVP-5001643)
*   [相等运算符(C#参考)](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators?WT.mc_id=DT-MVP-5001643)
*   [c#中等号运算符(==)和 Equals()方法的区别](https://www.c-sharpcorner.com/UploadFile/3d39b4/difference-between-operator-and-equals-method-in-C-Sharp/)

# 对抽象类使用`protected`构造函数

去做吧。

# 事件模式

我遇到了一个关于事件模式的错误。基本上，该类维护一个全局状态字段。如果应用程序检测到状态变化，该类将发布一个事件来通知订阅者。但是它首先发布事件，然后更新全局状态字段。所以当调用者收到事件时，全局状态可能还没有改变。它导致了间歇性故障。因此，如果调用者需要使用一些全局值，请确保发布者首先更新状态，然后发布事件。

# 删除无用的代码

如果有些代码没有用，可以为当前版本添加`Obsolete`属性。然后在未来的版本中彻底删除它们。没有必要在你的解决方案中保留无用的代码。它很丑。我见过很多被评论的代码，但是我不知道为什么它们还在那里。以后还会恢复吗？没人知道。所以如果你不需要，请删除它。或者你可以留下评论，说明为什么要暂时保留它。

# 不要对一个方法使用太多的参数。

如果一个方法有太多的参数，考虑将它作为一个类来重构。

# 摘要

这是一个杂乱的笔记，但事实上我遇到过这些问题很多次。当我有更多的想法时，我可以在这个系列中添加更多的主题。谢了。