# Scala journals——Currying:一个没有人真正使用过的代码示例的故事

> 原文：<https://levelup.gitconnected.com/scala-journals-currying-the-tale-of-that-one-code-example-no-one-actually-ever-used-d36db58e345b>

![](img/f07ef82abbec0ccc62da370f580cf263.png)

因此，不可避免的是，有一天你会读到一些 Scala 代码，并且想知道:currying 就像以一种过于复杂的方式传递更多的参数，或者我错过了什么？似乎是不必要的和复杂的，所以不可避免的问题来了: ***你为什么要这样做…？然后，你搜索 currying 示例，并冒险在线查看字符串 concat 示例，或者可能是整数加法。您遇到了如下几个例子，迫不及待地想在生产中使用这个新特性。所以……一个没有人真正使用过的代码示例的故事就像下面的片段一样，你在互联网上随处可见:***

```
def add(a: Int)(b: Int) = a + b
def add4 = add(4) _
add4(10) // 14
```

如果你问自己**为什么**——很好。让我解释一下，因为这也困扰了我很长时间。

# 什么是 currying？

对于**现在的**，把奉承想象成在**多个** **组中传递参数给一个函数。**

```
// one group, three arguments, not currying!
def add(one: Int, two: Int, three) = one + two + three // three groups, one argument each - currying!
def add(one: Int)(two: Int)(three: Int) = one + two + three 
```

让我们暂时接受这个完全不真实的加法例子，让我们来谈谈函数签名。一旦我们有了一个更大的图景，我们将转移到现实生活中的例子。

**柯里函数中的每个组都是一个函数本身，**因此对于:

```
def add(one: Int, two: Int)(three: Int) = one + two + three
```

…签名(*)看起来像这样:

```
def add(one: Int, two: Int)(three: Int) = one + two + three
**(Int, Int) => Int => Int**
```

这意味着我们可以将其分解为:

函数`add`是一个需要两个函数来运行的函数:

*   一个有签名的`(Int, Int) => Int`
*   一个有签名的`Int => Int`

我们需要为`add`函数提供所有参数组来评估结果吗？当然，**是的:** `add`不然就不行了。

但是…我们需要一次提供所有的参数组吗？**不！**

# 分步解决

所以我们不必一次提供所有组。从逻辑上来说，你应该让函数更好地重用它们，分解它们，简化它们。

那会是什么样子呢？让我们看看:

```
def add(one: Int)(two: Int) = one + two
**compiler output**:
add: (one: Int)(two: Int)Intdef partiallyApplied = add(1) _ // passing only the first arg group
**compiler output**:
partiallyApplied: Int => Int // will require the second arg groupval fullyAppliedNow = partiallyApplied(5) // passing 2nd arg group
**compiler output**:
6
```

在`add(1) _`中，快速注意上面的**下划线**。你可以把它想成“**剩下的我以后给你**”。编译器对此没有意见，所以它返回满足参数组初始列表所需的任何内容。

下面是关于下划线的更多细节:在 Scala 中有函数和方法。作为一门完全支持函数式编程的语言，Scala 中的函数是对象。Scala 编译器允许在方法(`def`)和对象(`val`)之间转换。这叫做**埃塔扩张。所以当你做 _ 的时候，你把一个方法转换成一个函数，你 **eta expend** 。**

# 让我们谈谈现实生活

这一切都有道理，但正如没人用过的代码告诉我们的那样:没人会真的用 currying 做加法。事实上……奉承实际上并没有用那么多。有几个原因，但我觉得最主要的原因是它使得代码库很难推理。我可以看到在一个更加函数化的纯粹主义环境中 curry 会被更多的使用，但是到目前为止我只是偶尔看到它。

经验法则是——如果你能创作它，你可能就能创作它。编译器是极限。但是请记住，谄媚会影响代码的可读性，如果过分，弊大于利。

这里有几个现实生活中的例子:

*   所有的**隐式参数**都需要修改。在使用期货的[代码库中，你会经常看到这样的内容:](https://github.com/leqoo/slick-lab2/blob/5e7b9b2aa2333e497a3c924f790879d29929e931/application/src/main/scala/Lab2_0.scala#L14)

```
def update(name: String)(implicit ec: ExecutionContext) 
```

*   修正**棘手的争论**。如果你需要在所有的函数中使用它，你可以使用它。一个很好的例子就是跟踪 id，它允许我们在日志中关联请求。

```
def handleRequest(tracingId: TracingId)(request: Request) = {
 handleWithTracingId(request, tracingId)
}def handleGetRequest  = handleRequest(generateRandomTraceingId) _handleGetRequest(request)
```

*   与上述相关，**数据库连接**也可能是一个棘手的问题..甚至**配置**！

```
def queryDbUsingConn(dbConn: DbConnection)(query: Query) = {
 dbConn.query(query)
}val dbConn = new DatabaseConnectiondef queryDb = queryDbUsingConn(dbConn) _queryDb(query) // no need to worry about db connection anymore while passing queries!
```

*   还有一个更复杂的例子——在更高的抽象层次上组合功能，这些功能依赖于用户在传入请求中提供的内容，而根本不需要引用传入请求。如果您 curry，您将能够传递函数，而不会因为不属于它的细节而污染代码:

```
case class IncomingRequest(name: String, settings: UserSettings)
// above case class represents the incoming http request with some user details that we will know only once the request is indef update(service: SettingsService)(settings: UserSettings) = {
 val darkTheme = settings.isDarkTheme // data from http request
 service.saveDarkTheme 
}// imagine below code is on a high level of abstraction, e.g. in a module trait. We don't want to pollute it with request handling related code so we just say "the rest coming later" and using _:val settingsService = new SettingsService
def updateSettings = update(settingsService) _ // updateSettings is now ready to be passed down to a responsible handler and to be filled in with the settings we will get from the user request once it comes in. Imagine in the below code we are in the request handling logics and have IncomingRequest already in scope.def sendNewSettingToDatabase = updateSettings(incomingReq.settings)
```

# 结论

Currying 是一个伟大的工具，可以减轻程序员的认知负担。它允许您在进行过程中，或者当参数组出现/变得可用时，向初始函数添加参数组。把我们不关心的细节抛在脑后，只关注对给定的类有意义的东西，这很好。作曲，作曲，作曲！如果使用太多，会使代码混乱，很难推理，所以要小心使用！我个人的观点是，9/10 的情况下，你可以不去做。

示例用法:

*   棘手的参数:数据库连接、配置、流 id
*   隐式参数
*   “推迟”传递尚不可用的参数(例如，传入 http 请求中的数据)

(ETA 扩展后的签名