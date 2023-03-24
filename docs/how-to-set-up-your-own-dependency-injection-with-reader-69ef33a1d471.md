# 如何用 Reader 设置自己的依赖注入

> 原文：<https://levelup.gitconnected.com/how-to-set-up-your-own-dependency-injection-with-reader-69ef33a1d471>

![](img/cf1473df9fd1bf516066ed0aa68e96fc.png)

*原载于*[*https://edward-huang.com*](https://edward-huang.com/functional-programming/scala/cats/monad/tech/2020/01/29/how-to-set-up-your-own-dependency-injection-with-reader/)*。*

有许多方法可以创建依赖注入。依赖注入有助于将一个对象与另一个对象解耦，在运行时绕过对框架或对象的依赖。因此，客户端不需要找到需要向对象或框架提供什么依赖——相反，系统告诉客户端他们需要向对象提供什么依赖。

在 Scala 中，有很多方法可以创建依赖注入。今天，我将讨论在 Cats 中使用 Reader 作为 Monad 来创建依赖注入，以及 Reader 如何帮助您快速地为您的系统创建配置验证。

想象一下，如果您需要创建一个验证系统，可以轻松地挑选各种操作来验证系统中的配置。您可以创建一个方法来验证配置中的每个组件。

```
def isCorrectEmail(id:Long, email:String, repo: Repository) : Boolean = {
  if(repo.findId(id)) repo.getId(id) ....
}
```

在这种情况下，如果有一天需要验证一个新的配置属性，或者需要对配置进行专门的检查以使其通过，您将需要查看代码并更改方法。

随着配置文件中更多的更改，您需要每次都转到该文件并更改代码。此外，每一张支票都是相互关联的，因此将来很难更新。

# 拯救读者

Cats Reader 数据类型可以帮助您将多个操作链接在一起，并产生一个重要的计算，该计算接受一个配置作为一个参数。如果您输入了配置，它会按照我们指定的方式运行，这将是最好的。

我们来看看`Reader[A, B]`的定义:

```
object Reader {
  def apply[A,B](f:A => B) : Reader[A,B] =  ReaderT[Id,A,B]
}
```

这意味着当你创建一个`Reader[A, B]`时，它得到 A 类型的回调函数并返回 B 类型。

创建阅读器的示例:

```
case class Cat(sound: String)

*// extracting the sound from the Cat class* val retrieveSound : Reader[Cat, String] = Reader {cat => 
  cat.sound
}
```

当实例化 Reader 类型时，传递接收到的配置的回调函数，在本例中为`Cat`，并检索其中的一个属性，在本例中为`cat.sound`。

在您定义了阅读器类型之后，您可以通过运行方法`run`来调用它，并将配置作为参数`run`提供:

```
*// calling the cat* println(retrieveSound.run(Cat("meow"))) *// meow*
```

如果您读到这里，您可能会停下来想一想，“Reader 为什么要这样输入 Monad，尤其是对于常规的验证原始函数？”

我的朋友，答案是单子型是它的独特之处。

作为一个 Monad，读取器类型可以调用 map 和 flatMap 来链接操作，并修改现有读取器的操作。

通常，您会创建一组接受相同类型配置的读取器。然后，您可以用 flatMap 和 Map 组合并链接它们，然后使用`run`并在最后提供该配置。

# 地图

map 方法修改读取器内部的计算，绕过函数得到回调函数的结果。

```
*// from previous Cat example* val checkSound: Reader[Cat, Boolean] = retrieveSound.map(_ == "meow")

checkSound.run(Cat("bark")) *// false*
```

# 平面地图

平面图操作是 Reader 如此强大的原因。它有助于组合依赖于相同输入类型的操作。

```
val greet:Reader[Cat, String] = Reader{cat =>
  s"hello $cat"
}

val sound:Reader[Cat, String] = Reader{cat =>
  s"${cat.sound} ${cat.sound}"
}

val greetAndSound = for{
  g <- greet
  check <- checkSound
  s <- sound
} yield {
  if(check) g + s else "sound is not right"
}

val result = greetAndSound.run(Cat("meow"))
println(result)
val notRight = greetAndSound.run(Cat("bark"))
println(notRight)
```

# 读者在行动

Reader 可以设置您的所有操作，并将配置作为参数注入您的操作。

让我们创建一个验证检查函数，给定一个 id 和一个电子邮件，检查数据库中是否有与该 id 对应的电子邮件。让我们创建我们的存储库案例类:

```
case class Repository(userDB:Map[Long, String], emailDB: Map[String, List[String]])
```

`userDB`地图`id, Long`到`username, String`。`emailDB`将用户名映射到电子邮件列表。

首先，我们需要创建一个获取 id 并检索用户名的阅读器类型 getUser。

```
def getUser(id:Long):Reader[Repository, Option[String]] = Reader{repo =>
    repo.userDB.get(id)
  }
```

然后，我们创建`getEmail`，它将获取用户名并返回一个电子邮件列表。

```
def getEmail(username:Option[String]): Reader[Respository, List[String]] = Reader {repo =>
    repo.emailDB.getOrElse(username.fold("none")(st => st), List.empty[String])
  }
```

最后，我们可以让 Reader 接受 id 和 email 参数，并检查 id 是否包含 email 地址。用这个方法，我们把`getUser`方法和`getEmail`结合起来。然后，我们检查电子邮件列表是否与参数中输入的电子邮件相匹配。

```
def checkIfEmailMatch(id:Long, email:String):UserReader[Boolean] = for {
    usernameOption <- getUser(id)
    emails <- getEmail(usernameOption)
  } yield {

    emails.contains(email)
  }
```

如何执行`checkIfEmailMatch`？

让我们设置主方法:

```
val userDB = Map(
    1L -> "john",
    2L -> "jane",
    3L -> "kate"
)
val emailDB = Map(
  "john" -> List("something@gmail.com"),
  "jane" -> List("jane@yahoo.com", "jane@gmail.com"),
  "kate" -> List("kate@hotmail.com", "kate123@yahoo.com")
)

val repo = Repository(userDB,emailDB)
val res = checkIfEmailMatch(2L, "jane@gmail.com").run(repo)
println(res)
```

我们设置了所有的操作，从`getUser`开始，然后是`getEmail`。然后，我们将这两个操作组合到`checkIfEmail` exist。这些都包含在一个配置文件中，也就是 repo。当你调用`run`方法时，它执行操作。

# 外卖食品

*   Reader 为依赖注入提供了一个工具——通过在相同的配置中设置所有的操作。
*   当我们想构造一个可以很容易地用函数表示的批处理程序时，阅读器是最有用的；推迟一个已知参数的注入，隔离我们想要测试的程序部分。
*   通过将您的系统设置为阅读器，您可以将您的阅读器表示为一个纯函数，并使用 map 或 flatMap 来组合它们。

注册我的[时事通讯](https://edward-huang.com/subscribe/)每周获取此内容！