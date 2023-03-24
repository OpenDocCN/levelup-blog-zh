# 面向 Java 开发人员的 Golang 指针、错误处理和并发性

> 原文：<https://levelup.gitconnected.com/go-for-java-devs-pointers-error-handling-and-concurrency-493dad0c5129>

## 从 Java 角度看 Go 编程语言—第 2 部分

![](img/73e124459102cb94eb3dfef69193e47a.png)

作者糟糕的草图

在本系列的第一篇文章中，我将自己描述为一名资深 Java 工程师，最近决定尝试一下。尽管我将永远热爱 Java，但我知道继续学习语言很重要。我选择 Go 是因为它是一种静态类型的编译语言，不会面临 Java 面临的一些问题(比如编译时间和启动时间慢)。

这个系列不是要让 Java 和 Go 对立起来。两者都是强势语言，各有优缺点。相反，这是我作为 Java 开发人员对 Go 的第一次观察。鉴于我仍在学习 Go，如果任何读者认为我误解了 Go(或者 Java，就此而言)，我欢迎任何反馈。

至此，让我们进入一些真正区别于 Java 的领域:指针、错误处理和并发性。

# 两颗北极指极星

在 Java 中，当我们将一个对象传递给一个方法时，我们传递的是一个指向该对象的指针。但是当我们传递一个原语(比如 int 或 double)时，我们实际上传递的是该原语的一个副本(这就是为什么，如果我将`int myInt = 2`传递给一个方法:`void quadruple(int i) { i = i * 4; }``myInt`本身的值仍然是*2*；它的方法作用域*副本*将会翻两番。)

Go 有结构，没有对象。在使用 Go 时，您很快就会了解到，默认情况下，Go 对待结构体的方式与 Java 对待原语的方式相同。也就是说，当您将结构作为函数参数传递时，或者当您从函数返回结构时，将复制整个结构。所以在下面的代码块中:

```
type Person struct {
  name string
}person := Person{"Pat"}func changeName(p Person) {
  p.name = "Chris"
}changeName(person)log.Println("Person's name is %s", person.name)// prints:   Person's name is Pat
```

`person`变量的名字仍然是 Pat 仅*副本的名称* ( `p`)被更改。这不是我们对 Java 对象的期望。同样，如果我做了这样的事情:

```
type Org struct {
  leader Person
}func (o Org) getLeader() Person {
  return o.leader
}org := Org{ Person{"Pat"} }o.getLeader().name = "Chris"log.Println("Org leader's name is %s", org.leader.name)
```

那我就不会真的改变`org`领导人的名字。相反，我会更改从`getLeader()`函数返回给我的*副本*的名称。同样，这也不是我们这些习惯于使用 Java 对象的人所期望的。

然而，Go *通过允许我们显式地传递和返回指向函数的*指针*来允许我们复制 Java 对象的行为。*

## 指针？😳

提到*指针*，你可能会有这样的反应:

1.  我要离开这里！我曾经在 C/c++/C --无论什么语言中做过指针操作，那简直是一场噩梦！
2.  我要离开这里！我听说过其他语言中的指针，它们听起来就像一场噩梦！

如果你的反应符合上面的 1 或 2，不用担心。与 C 语言系列相比，Go 中指针的使用要简单得多，更容易理解，也更不容易出错。尽管如此，我还是遇到了一些在 Go 中使用指针的麻烦。

## 围棋中我们如何传递指针？

如果你在基于 C 的语言中使用过指针，那么 Go 的语法应该看起来非常熟悉。本质上，我们只是使用星号(`*`)来告诉 Go 我们想要传递指针，我们使用&符号(`&`)来获取指针。在这个街区:

```
org := Org{ ... }
per := Person{...}func (o *Org) setLeader(p *Person) *Org {
  o.leader = p
  return o
}(&org).setLeader(&per)
```

我们已经定义了一个函数，它接受一个指向`Person`的指针，并返回一个指向`Org`的指针。注意，这是一个*接收器功能。*因为我们想修改接收器本身，我们声明接收器也必须*和*作为指针传递。

Go 的一个好处是我们不需要为记住解引用指针而纠结。以这个代码示例为例:

```
person := Person{ "Sal" }
org2 := org.setLeader(&person)
log.Println("Org 2's leader is %s", org2.getLeader())
```

从技术上讲，`org2`变量指的是一个指针——当然不包含`getLeader()`方法——而不是一个`Org`结构。然而，Go 有助于识别我们打算在指针引用的*结构*上调用`getLeader()`。

## 一些简单的准则

在决定是否在 Go 中传递指针或值时，有一些简单的准则:

*   正如我们已经讨论过的，如果你想直接改变值，那么你需要使用指针
*   指针有时被认为比传递值更有效，因为它们避免了复制整个结构的开销。然而，情况并非总是如此，因为 Go 在从函数返回指向对象的指针之前必须执行[转义分析](https://en.wikipedia.org/wiki/Escape_analysis)，以确定它是否可以在当前堆栈上为该结构分配空间，或者它是否必须使用堆。
*   你的 API 应该以一致性为目标。将指针和直接值混合在一起会导致 API 混乱。这也会导致我们接下来要讨论的问题

## 一环断掉，整个链条都毁了

正如我提到的，我发现在 Go 中理解和使用指针相对容易。但是我被绊倒了。

例如，我开发了一个在内存中存储数据的小型 web 应用程序。我的结构层次是这样的:

*   一个`Org`包含多个`Event`
*   一个`Event`包含多个`Participant`
*   一个`Participant`包含一个`count`(一个`int`变量)

当一个特定的请求进来时，我想增加给定的`Event`内所有`Participant`的`count`值。

正如你可能猜到的，我的第一次尝试失败了。原因很清楚:我返回的是结构的副本，而不是指针。所以我改变了(我认为是)所有涉及获取相关`Participant`的函数的签名。但是通过记录和调试，我发现我最终还是以某种方式获取了那些`Participant`的副本进行修改。搞清楚这一点并不容易，我不得不到处传递和返回指针(最终导致我重新思考我的整个设计)。

# 错误处理

Go 通过利用函数可以返回多个值的事实，提供了一种有趣的处理错误的方式。任何可能遇到错误条件的函数都返回两个值:函数预期返回的值和一个*错误*。`error`实际上是一个接口，定义了一个方法:

`type error interface { Error() string }`

所以函数的签名看起来是这样的:
`func doSomething(arg string) (string, error)`

如果调用函数时没有出错，那么函数返回想要的结果(这里是一个`string`)和`nil`。如果出错，该函数将返回一个任意字符串(通常是`“”`)和一个非零值`error`。

当然，一个通常不会返回值的函数会简单地声明它返回`error`，并在没有错误的情况下返回`nil`。

当调用这样一个函数时，我们必须——至少——指定一个变量来捕捉错误。否则，代码将无法编译。一旦我们这样做了，如果错误不是`nil`，我们应该采取适当的行动。

让我们看一些调用 Go 的文件系统 API 的代码:

```
var fname string = getFilename()
// below, values for 'file' and 'err' are returned. 
// err's value might be nil
file, err := fs.Open(fname)
if (err != nil) {
  log.Printf("Could not open file %s", fname);
} else {
  // read file, etc
}
```

如果我们试着这样写:
`file := fs.Open(fname)`
我们的代码不会编译。我们必须抓住错误。当然，我们可以选择忽略`err`并假设没有错误发生(并且，很可能，冒着运行时异常的风险)。事实上，Go 提供了“空白标识符”(用下划线或`_`表示)，它允许我们显式地忽略返回值:
`file, _ = fs.Open(fname)`
。然而，在这一点上，我们已经有意识地决定这样做了。

这种方法不同于 Java 和许多其他语言采用的 try/catch 方法。(如果有什么不同的话，那就是在使用 RxJava 之类的反应式框架或 Scala 之类的函数式语言所采用的[或](https://www.freecodecamp.org/news/a-survival-guide-to-the-either-monad-in-scala-7293a680006/)时，感觉更类似于[的错误处理。)](https://www.baeldung.com/rxjava-error-handling)

Java 的 try/catch 机制因许多原因而受到批评；其中包括:

*   我们必须将异常定义为运行时异常(这使得它们很容易被忘记并且无法正确处理)或检查异常(这鼓励了繁琐的方法签名或琐碎的错误处理)。
*   它鼓励开发人员偷懒，简单地将他们的代码包装在粗粒度的 try/catch 块中，并一次性处理所有错误。

相反，Go 的方法强迫(或者至少强烈鼓励)工程师在错误出现时处理它们。没有运行时异常和检查异常:每个错误都以相同的优先级处理。

那么哪个更好呢？我不能肯定地说。据我所知，整个行业也是如此。在比较了 Java 和 Go 的方法之后，我唯一能说的是，永远不会有处理错误的完美解决方案。

## 好人

孤立地看，Go 的方法是有道理的。对于任何给定的函数，我们很清楚这个函数可能会导致某种错误情况。在这些情况下，检测和处理错误相对简单。如果我们确信可以安全地忽略错误，那么忽略错误就更简单了(同样，如果没有明确的决定，几乎没有办法忽略错误)。

Go 的错误本身相对简单:它们只是任何符合`error`接口的结构。除此之外——与 Java 异常不同——它们没有什么神奇之处。事实上，从技术上讲，我可以声明，如果一个错误条件发生，我的函数返回任何东西。

在 Java 中，方法可以声明它可能会遇到多种类型的错误:

```
public void parse(String filename) 
   throws FileNotFoundException, EncodingEception
```

此外，我们可能希望在代码中不同地处理每种类型的异常。在 Java 中这样做相对简单；我们简单地为每种类型实现一个`catch`块。

我意识到在围棋上，我们做不到这一点。然而，考虑到声明一个函数返回一个错误并没有什么神奇的，我意识到我们可以扩展函数的接口来返回多个错误指示器:

```
func Parse(fname string) (File, notFoundError, encodingError)
```

在上面的例子中，`notFoundError`和`encodingError`需要表示接口，以使`nil`能够在它们的位置返回。那会有用的。但缺点是它的非标准性质可能会让工程师感到困惑。

相反，我了解到惯用的方法是打开错误的具体类型:

```
func Parse(fname string) (File, error)...file, err := Parse("file.txt")
if (err != nil) {
  switch err.(type) {
    case *NotFoundError:
      // handle not found errors
    case *EncodingError:
      // handle encoding error
  }
}
```

## 坏事

从更大的角度来看，Go 的错误处理可能相当痛苦。可能没有什么地方比编写数据库访问代码时更渴望 Java 的 try/catch 方法了。人们很容易忘记(也应该忘记)即使是一个简单的数据库操作也有许多部分会产生异常:打开连接、启动事务、执行查询、提交(或回滚)事务、关闭连接等。用 Go 写的时候，需要添加错误处理代码。因为。每个。还有。每一个。操作。

正如你可能猜到的，这变得相当乏味，相当快。

公平地说，我应该注意到 Go 确实提供了一点语法上的好处，有助于减少我们输入的行数。我们可以将错误检查与函数调用本身结合起来，如下所示:

```
if file, err := Parse("file.txt"); err != nil {
  log.Printf("Error parsing file: %s", err.Error())
} else {
  // ... do something with `file`
}
```

关于 Go 的错误处理方法，还有一件事让我感到困扰，尽管这主要是个人的小毛病。Go 的方法迫使我们做以下事情:

*   在编写函数时，我们经常需要在出错时返回一个变量的一次性实例。如果我们的函数的返回值是`(File, error)`并且发生了错误，那么我们将需要返回类似于
    `File{}, NotFoundError(“File not found”)`
*   当使用这样的函数时，我们需要执行零检查

对我来说，每一个都是难闻的气味。

这种错误处理方法不允许的另一件事是错误会冒泡到 main 方法的调用堆栈。这实际上在某些环境中是有用的，比如 web 服务器，其中主控制器可以等待捕捉任何意外的异常并将它们翻译成 500 个响应代码。

原来 Go 也提供这个功能…通过`panic` s。

## 不要慌！好吧，好吧，继续恐慌吧。

恐慌实际上非常类似于 Java 异常…至少是 RuntimeExceptions。如果出现运行时错误(nil 指针引用在 Go 中与在 Java 中一样糟糕！)那么就会出现恐慌。或者，我们可以明确地引起恐慌:

```
if (somethingBadHappened == true) {
  panic("Something bad happened!")
}
```

不管是哪种情况，恐慌都会沿着调用堆栈向上蔓延。如果到达顶部，应用程序将崩溃并打印一个 stacktrace。

然而，正如我们可以`catch` Java 运行时异常一样，我们也可以`recover`避免 Go 死机:

```
defer func() {
  if err := recover(); err != nil {
    Log.printf("Recovering from panic: %s", err)
  }
}()
```

当我们从一个*延迟函数*中调用`recover()`(在下一节中描述)时，它将返回一个传递给初始调用`panic()`(按照惯例通常是一个`string`)的实例。或者，如果应用程序当前没有出现紧急情况，则返回`nil`。不管怎样，我们都可以在那时恢复正常运作。

# 并发

当 Java 第一次出现时，它经常吹嘘的一个好处是它比 C 或 C++中的并发性容易得多。从那以后，Java 的并发产品变得更加健壮，也更易于使用。

尽管如此，Go 的并发性对 Java 来说就像 Java 对 C 和 C++的感觉一样。

Go 最初的既定设计目标之一是强大而简单的并发性。基于 Go 提供的并发原语，这种设计已经被满足了。

让我们来看看其中的一些原语

## 戈鲁廷斯

一个 goroutine(这个名字是对术语[协程](https://en.wikipedia.org/wiki/Coroutine)的一种玩法)是一个代码单元(例如一个函数)，它可以与其他代码单元同时运行。Goroutines 可能看起来像线，它们是相似的…但是更好。Goroutines 的重量比线程轻得多，在内存方面的成本也低得多(通常只需要几千字节的堆栈，并且可以根据需要增加或减少大小)。

[来源:[https://golangbot.com/goroutines/](https://golangbot.com/goroutines/)

就旋转一台机器所需的代码而言，它们的成本也低得多；我们只需要在函数调用前加上`go`关键字。让我们看一个例子:

```
go log.Printf("I am running in a goroutine!")
```

还有…大概就是这样了！上面的代码行启动了一个新的 goroutine，logs 语句在其中运行。

## 频道

Java 的一个问题是跨线程通信。当然，多线程修改同一数据实例是可能的，但是这很容易导致争用情况和难以预料的调试结果。还有[线程间通信](https://www.geeksforgeeks.org/inter-thread-communication-java/)(你知道，这种技术使用`wait()`和`notify()`，并捕捉`InterruptedException` s)，实现起来很痛苦。有像`synchronized`这样的结构，它们可以工作，但是会导致性能瓶颈。还有第三方解决方案(演员系统之类的)。

Go 提供了*频道*作为开箱即用的构造。通道为一个 goroutine 有效地向另一个通道发送消息提供了一种机制。打个比喻，我认为渠道是老式的锡罐电话系统；当我们发射 goroutine 时，我们将其中一个锡罐传递到 goroutine 中，并挂在另一个罐上，以便我们可以交流。

从某种程度上来说，在我看来，通道介于前面提到的 Java 线程间通信和非常精简的 actor 系统之间。然而，通道本身并不是一个重量级的结构；相反，它是一个简单的`chan`原语。

要使用通道，我们只需通过调用`make()`来创建它。Go 是强类型的，通道也是强类型的，所以我们指示要通过通道发送的消息的数据类型。然后，我们将通道传递给将在另一个 goroutine 中运行的函数。我们使用箭头操作符(`<-`)向通道发送消息，并从通道接收消息。箭头操作符很有帮助，因为它总是显示通信的流向。也就是上面的
`ch <- “Hello”`
，我们正在向`ch`频道发送“你好”的消息。在上面的
`msg := <- ch`
中，我们正在接收来自`ch`通道的消息，并将其分配给`msg`。

让我们看一个例子，它展示了使用通道是多么简单:

```
func speak(anim Animal, ch chan string) {
  // below, send a string message through the channel
  ch <- Sprintf("%s says '%s'. ", anim.Name(), anim.Speech())
}func main() {
  dog := Animal{name:"Dog", speech:"Woof"}
  cat := Animal{name:"Cat", speech:"Meow"}
  // below, we create the channel, which will accept string messages
  animalSpeechChannel := make(chan string)
  // then we call speak() twice, passing the channel
  go speak(dog, animalSpeechChannel)
  go speak(cat, animalSpeechChannel)
  // Then we block until we receive two messages via the channel
  a1, a2 := <- animalSpeechChannel, <- animalSpeechChannel fmt.Println(a1, a2) // Dog says 'Bark'. Cat says 'Meow'. 
}
```

尽管使用起来很简单，但是通道提供了更强大的构造，并且可以服务于更复杂的用例。例如:

*   使用通道进行请求/响应类型的通信(即有效地调用一个函数并接收一个返回值，但是跨 goroutines)。这可以很容易地完成，通过跨通道发送消息，并在该消息(需要是自定义数据类型)中提供第二个*响应通道*。
*   频道可以被取消，并且可以通知它们的接收器它们的取消。
*   信道可以被缓冲，保存固定数量的元素，即使不存在接收元素的接收器。
*   通道支持[扇出和扇入](https://www.toolbox.com/tech/enterprise-software/blogs/design-principles-fan-in-vs-fan-out-050407/)。

## 推迟

推迟就像一小片魔法。实际上，任何以关键字`defer`开头的代码块都不会被执行，直到周围的函数将要返回。作为一个半人为的例子:

```
func update(data string) {
  conn, err := openConnection()
  if (err != nil) {
    log.Printf("Error opening connection")
    return
  }
  defer conn.close()
  // now, our connection will be closed no matter what
  entity, err := conn.get(data.Id())
  if (err != nil) {
    log.Printf("Error getting entity")
    return
  }
  err := conn.update(entity, data)
  if (err != nil) {
    log.Printf("Error updating entity")
  }
}
```

这对于强制清理任务非常有用，例如关闭连接，否则这些任务可能无法运行。正如在别处提到的，它让我们从恐慌中恢复过来。

## 其他并发原语

Go 还提供了其他并发原语，包括:

*   [Waitgroup](https://gobyexample.com/waitgroups) ，允许我们轻松阻塞，直到给定数量的优秀 goroutines 完成
*   [互斥](https://tour.golang.org/concurrency/9)，它提供了与 Java 的`synchronized`构造相似的功能。

所有的并发原语都被设计为协同工作，允许我们轻松地开发极其复杂的并发应用程序。

# 让我们继续前进

在本系列的[下一篇也是最后一篇文章](https://medium.com/p/672597c19ae4)中，我们将从 Go 编码的本质上后退一步，讨论 Go 的编译和启动时间，包和模块如何帮助组织代码和依赖关系，以及内置于 Go 核心的一些高级功能。

觉得这个故事有用？想多读点？只需[在此订阅](https://dt-23597.medium.com/subscribe)即可将我的最新故事直接发送到您的收件箱。

今天[成为媒体会员](https://dt-23597.medium.com/membership)，你也可以支持我和我的写作，并获得无限数量的故事。