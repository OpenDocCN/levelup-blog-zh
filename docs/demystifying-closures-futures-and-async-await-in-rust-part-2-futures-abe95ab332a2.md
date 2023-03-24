# 揭开 Rust 中闭包、未来和异步等待的神秘面纱第 2 部分:未来

> 原文：<https://levelup.gitconnected.com/demystifying-closures-futures-and-async-await-in-rust-part-2-futures-abe95ab332a2>

在这一系列文章中，我试图揭开神秘的面纱，带您从 Rust 闭包，到 futures，最后到 async-await。

这继续了我们在 [**第 1 部分:闭包**](https://medium.com/@alistairisrael/demystifying-closures-futures-and-async-await-in-rust-part-1-closures-97e531e4dc50) 中离开的地方

# 东京漂移

为什么在期货和`async` / `await`之前理解平仓如此重要？

因为在大多数情况下，无论是使用`futures`机箱还是`async`/`await`,`std::future::Future`特征的实现仅仅是包装闭包的另一个结构！

然而，在我们继续之前，有必要注意一些事情。

其他支持异步编程的语言通常会在解释器或虚拟机中内置运行时。或者他们自动编译并链接运行时到最终的二进制文件中。

因为 Rust 的核心理念之一是“零成本抽象”，设计者决定*不*默认提供绿色线程或内置运行时。

(关于 Rust 异步编程及其历史的精彩、深入的解释，请参见 InfoQ 的 [Rust 的异步/等待之旅](https://www.infoq.com/presentations/rust-2019/)。)

相反，要编写异步代码，我们需要另一个库或箱子来提供实际的运行时。*事实上的*运行时为 Rust 的是**Tokio:**[**https://Tokio . RS**](https://tokio.rs/)

因为我们将与期货打交道，我们还想使用`[futures](https://docs.rs/futures/0.3.5/futures/)` [箱](https://docs.rs/futures/0.3.5/futures/)中的一些方便的方法。不要求使用`futures`板条箱，但是*通常很有帮助。*

编辑`Cargo.toml`并将`futures`和`tokio`添加到依赖关系部分:

```
[dependencies]
futures = "0.3"
tokio = { version = "0.2", features = ["full"] }
```

(`"full"`功能标志告诉 Tokio[启用所有公共 API](https://docs.rs/tokio/0.2.20/tokio/#feature-flags)。)

我们现在需要初始化 Tokio 运行时。我们可以简单地创建默认的 Tokio 运行时:

```
let rt = tokio::runtime::Runtime::new().unwrap();
```

## 输入()

使用 Tokio 运行时的最快捷的方式是使用`[enter()](https://docs.rs/tokio/0.2.20/tokio/runtime/struct.Runtime.html#method.enter)`方法，该方法使用一个`FnOnce`，一个闭包！

```
rt.enter(|| {
    println!("in rt.enter()");
});
```

如果你运行这个，你应该在程序输出中看到`in rt.enter()`。没什么太激动人心的，嗯？

让我们继续导入`futures::future`:

```
use futures::future;
```

## 产卵()

在运行时闭包内，我们可以使用`tokio::spawn()`来执行一个`Future`(无需等待它完成或返回)。

为了创建一个合适的`Future`,我们将使用`futures::future::lazy`,它同样需要一个闭包，但这次只需要一个参数(一个上下文，我们现在忽略它):

```
rt.enter(|| {
    println!("in rt.enter()");
    tokio::spawn(future::lazy(|_| println!("in tokio::spawn")));
});
```

如果我们将`tokio::spawn()`调用移到`rt.enter()`闭包之外会发生什么？

```
rt.enter(|| {
    println!("in rt.enter()");
});
tokio::spawn(future::lazy(|_| println!("in tokio::spawn()")));
```

我们得到一个运行时错误:

```
thread 'main' panicked at 'must be called from the context of Tokio runtime configured with either `basic_scheduler` or `threaded_scheduler`',
```

或者，我们可以在运行时本身调用`.spawn()`:

```
rt.spawn(future::lazy(|_| println!("in rt.spawn()")));
```

## block_on()

最后我们可以调用`rt.block_on()`。但在此之前，我们需要将变量`rt`改为`mut`(因为`*block_on()*` [*可能会提前运行时的状态*](https://docs.rs/tokio/0.2.20/tokio/runtime/struct.Runtime.html#method.block_on) ):

```
let mut rt = tokio::runtime::Runtime::new().unwrap();
```

现在我们可以写:

```
rt.block_on(future::lazy(|_| println!("in rt.block_on()")));
```

最终得到的代码:

```
let mut rt = tokio::runtime::Runtime::new().unwrap();
rt.enter(|| {
    println!("in rt.enter()");
    tokio::spawn(future::lazy(|_| println!("in tokio::spawn()")));
});
rt.spawn(future::lazy(|_| println!("in rt.spawn()")));
rt.block_on(future::lazy(|_| println!("in rt.block_on()")));
```

如果你运行上面的代码，你可能会看到:

```
in rt.enter()
in tokio::spawn()
in rt.spawn()
in rt.block_on()
```

我说你*可能会*看到上面的输出，因为有时候你可能会看到不同顺序的输出。或者，您可能只会看到`in rt.enter()`后面跟着`in rt.block_on()`

为了了解发生了什么，我们需要看看引擎盖下。

# 在后台

让我们看看引擎盖下面。首先，我们将使用一些日志库，因此将以下内容添加到`Cargo.toml`中的依赖项中:

```
log = "0.4"
simplelog = "0.7"
```

接下来，将以下导入添加到`main.rs`的顶部:

```
use log::debug;
use simplelog::{ConfigBuilder, LevelFilter, SimpleLogger};
```

然后在`main()`开始初始化记录系统时:

```
let config = ConfigBuilder::new()
        .set_target_level(LevelFilter::Trace)
        .build();
let _ = SimpleLogger::init(LevelFilter::Debug, config);
```

最后，用`debug!()`替换所有的`println!()`宏调用。例如，我们之前的代码现在应该是这样的:

```
rt.enter(|| {
    debug!("in rt.enter()");
    tokio::spawn(future::lazy(|_| debug!("in tokio::spawn()")));
});
rt.spawn(future::lazy(|_| debug!("in rt.spawn()")));
rt.block_on(future::lazy(|_| debug!("in rt.block_on()")));
```

现在运行该程序时，输出应该类似于以下内容:

```
10:36:28 [DEBUG] (1) in rt.enter()
10:36:28 [DEBUG] (1) in rt.block_on()
10:36:28 [DEBUG] (13) in tokio::spawn()
10:36:28 [DEBUG] (12) in rt.spawn()
```

圆括号中的数字`(1)`、`(13)`、`(12)`是记录系统识别的线程“id”。有时你会看到`in tokio::spawn()`和`in rt.spawn()`的其他线程 id，但是你应该总是看到`in rt.enter()`和`in rt.block_on()`的`(1)`

我们甚至可以看到 Tokio 运行时是如何启动线程的。将`let rt =`线更换为

```
let mut rt = tokio::runtime::Builder::new()
        .threaded_scheduler()
        .core_threads(4)
        .on_thread_start(|| debug!("on_thread_start()"))
        .build()
        .unwrap();
```

现在，当您`cargo run`时，您应该看到 5 个线程正在启动，每个线程都有自己的线程 id:

```
10:43:57 [DEBUG] (3) on_thread_start()
10:43:57 [DEBUG] (5) on_thread_start()
10:43:57 [DEBUG] (4) on_thread_start()
10:43:57 [DEBUG] (1) in rt.enter()
10:43:57 [DEBUG] (2) on_thread_start()
10:43:57 [DEBUG] (1) in rt.block_on()
10:43:57 [DEBUG] (4) in tokio::spawn()
10:43:57 [DEBUG] (5) in rt.spawn()
```

## 发生什么事了？

当`"rt-threaded"`特性启用时(通过`features = ["full"]`启用)，默认的 Tokio 运行时使用线程调度器。

`tokio::spawn()`和`rt.spawn()`都可以在不同的线程上调度给定的`Future`。

另一方面，`rt.enter()`和`rt.block_on()`都在`main()`线程上运行(线程 id `(1)`)。

现在，因为不同的线程异步并发执行，所以产生的任务可能以任何顺序运行，或者根本不运行。

然而，由于`rt.enter()`和`rt.block_on()`在同一个线程上执行，因此它们按顺序执行*。*

此外，它们都执行给定的块或未来完成。也就是说，它们*等待*给定块的完成。

# 回到未来

现在，精明的读者可能已经注意到了,`futures::future::lazy`是一个简洁的小函数，它简单地返回一个包装了给定闭包的`Future`!具体来说，它返回一个实现了`Future`特征的`Lazy`结构。

我们也可以使用`futures::future::ready()`来创建一个`Future`来立即返回给定值。

在我们继续之前，让我们看看如果我们只是调用一个直接返回一个`Future`的函数会发生什么。例如，如果我们在`main()`的末尾添加以下几行:

```
future::ready(42)
```

我们得到一个编译器错误:

```
error[E0308]: mismatched types
   --> src/main.rs:157:5
    |
157 |     future::ready(42)
    |     ^^^^^^^^^^^^^^^^^ expected `()`, found struct `futures_util::future::ready::Ready`
    |
    = note: expected unit type `()`
                  found struct `futures_util::future::ready::Ready<{integer}>`
help: try adding a semicolon
    |
157 |     future::ready(42);
    |                      ^
```

如果我们加上一个分号，就像它说的:

```
future::ready(42);
```

相反，我们得到一个编译器警告:

```
warning: unused `futures_util::future::ready::Ready` that must be used
   --> src/main.rs:157:5
    |
157 |     future::ready(42);
    |     ^^^^^^^^^^^^^^^^^^
    |
    = note: `#[warn(unused_must_use)]` on by default
    = note: futures do nothing unless you `.await` or poll them
```

我们暂时就说到这里，你将不得不等到 [**第三部分**](https://medium.com/@alistairisrael/demystifying-closures-futures-and-async-await-in-rust-part-3-async-await-9ed20eede7a4) 我们将再次遇到这个问题。

现在我们已经知道我们需要`.spawn()`或者`.block_on()`一个`Future`，所以下面应该可以工作:

```
 {
        let result = rt.block_on(future::ready("Hello from rt.block_on()"));
        debug!("{}", result);
    }
```

那么我们应该会看到这样的内容:

```
11:05:24 [DEBUG] (1) Hello from rt.block_on()
```

## 回归未来

我们可以编写自己的函数来返回期货。

首先，我们需要:

```
use std::future::Future;
```

要声明一个`Future`返回类型，我们需要指定其关联的`Output`类型。让我们尝试编写一个返回简单标量值的函数:

```
fn returns_future_i32() -> Future<Output = i32> {
    future::ready(42)
}
```

我们马上会遇到`trait objects without an explicit `dyn` are deprecated`和`error[E0746]: return type cannot have an unboxed trait object`。

## impl 未来

要解决这个问题，我们可以根据需要做两件事。更深入的解释见[这篇文章](https://joshleeb.com/posts/rust-traits-and-trait-objects/#impl-trait)。

简单的解决方案是，如果我们*绝对确定*我们的函数将只*曾经*返回与`Future`实现的*完全相同的*类型(或者，在本例中，只有一种类型),我们可以简单地使用`impl Future`

```
fn returns_future_i32() -> impl Future<Output = i32>
```

另一方面，如果我们调用其他函数，并且所有这些不同的函数可能返回*不同的* `Future`实现，那么`impl Future`将不起作用。

为了演示这一点，首先让我们在`Cargo.toml`中添加一个随机数生成器库到我们的`[dependencies]`

```
rand = "0.7.3"
```

现在，如果我们尝试将函数改为:

```
fn returns_impl_future_i32() -> impl Future<Output = i32> {
    if rand::random() {
        return future::ready(42);
    }
    future::lazy(|_| 1337)
}
```

在这个函数中，有 50%的几率它会“短路”并返回一个`future::Ready`。否则，它试图返回一个`future::Lazy`。

Rust 编译器给出了下面的错误(甚至告诉我们哪里做错了以及如何修复它):

```
error[E0308]: mismatched types
  --> src/main.rs:44:12
   |
39 | fn returns_impl_future_i32() -> impl Future<Output = i32> {
   |                                 ------------------------- expected because this return type...
40 |     if rand::random() {
41 |         return future::ready(42);
   |                ----------------- ...is found to be `futures_util::future::ready::Ready<i32>` here
...
44 |     return future::lazy(|_| 1337);
   |            ^^^^^^^^^^^^^^^^^^^^^^ expected struct `futures_util::future::ready::Ready`, found struct `futures_util::future::lazy::Lazy`
   |
   = note: expected type `futures_util::future::ready::Ready<i32>`
            found struct `futures_util::future::lazy::Lazy<[closure@src/main.rs:44:25: 44:33]>`
   = note: to return `impl Trait`, all returned values must be of the same type
```

## dyn 未来

在这种情况下，我们需要返回一个动态`Future`。现在，如果我们简单地将函数改为:

```
fn returns_dyn_future_i32() -> dyn Future<Output = i32>
```

我们又回到了`error[E0746]: return type cannot have an unboxed trait object`。

## 方框

直观的解决方案是。这意味着我们还必须`Box::new()`我们的返回值:

```
fn returns_dyn_future_i32() -> Box<dyn Future<Output = i32>> {
    if rand::random() {
        Box::new(future::ready(42))
    } else {
        Box::new(future::lazy(|_| 1337))
    }
}
```

现在我们遇到了一个全新的错误，当我们:

```
error[E0277]: `dyn core::future::future::Future<Output = i32>` cannot be unpinned
  --> src/main.rs:96:34
   |
96 |         let result = rt.block_on(returns_dyn_future_i32());
   |                                  ^^^^^^^^^^^^^^^^^^^^^^^^ the trait `std::marker::Unpin` is not implemented for `dyn core::future::future::Future<Output = i32>`
   |
   = note: required because of the requirements on the impl of `core::future::future::Future` for `std::boxed::Box<dyn core::future::future::Future<Output = i32>>`
```

## 插脚<box future="">></box>

现在的解决办法是**牵制**。至于原因，我就不赘述了；关于这一点，请参考[这一章关于牵制和为什么，具体来说，期货需要他们](https://rust-lang.github.io/async-book/04_pinning/01_chapter.html)。

简而言之，我们需要返回一个`Pin<Box<dyn Future>>`，而不仅仅是`Box<dyn Future>`。首先确保:

```
use std::pin::Pin
```

幸运的是(但非直觉地)，我们不必`Pin::new(Box::new(...))`。确切地说，我们只是`Box::pin(...)`。我们的函数现在工作得非常好:

```
fn returns_dyn_future_i32() -> Pin<Box<dyn Future<Output = i32>>> {
    if rand::random() {
        Box::pin(future::ready(42))
    } else {
        Box::pin(future::lazy(|_| 1337))
    }
}
```

或者，正如另一位读者所指出的，你可以使用 `[futures::future::FutureExt](https://docs.rs/futures/0.3.5/futures/future/trait.FutureExt.html#method.boxed)`中的`[.boxed()](https://docs.rs/futures/0.3.5/futures/future/trait.FutureExt.html#method.boxed)` [方法，就像这样:`future::ready(42).boxed()`。](https://docs.rs/futures/0.3.5/futures/future/trait.FutureExt.html#method.boxed)

# 期货中的错误处理

如果所有的东西都是独角兽和彩虹，我们写的所有代码将总是能够正常工作，我们根本不需要处理错误。

可悲的是，除了人为的或琐碎的例子，我们需要处理错误，对于异步编程，我们也需要一种异步处理它们的方法。

由于`Future`特征的关联`Output`类型可以是任何类型，我们可以将其设置为`Result`。

同样，首先我们需要几个新的导入:

```
use std::error::Error;
```

现在，让我们尝试返回一个输出类型为`Result`的`Future`。这一次，我们不仅仅使用`future::ready()`，而是使用`future::ok()`。但是如果我们尝试

```
fn returns_future_result() -> impl Future<Output = Result<i32, Error>> {
    future::ok(42)
}
```

我们立即得到熟悉的`warning: trait objects without an explicit `dyn` are deprecated`和`error[E0277]: the size for values of type `dyn std::error::Error` cannot be known at compilation time`

既然我们知道错误不会发生，让我们试着去做。迎接我们的是一个全新的编译器错误:

```
error[E0720]: opaque type expands to a recursive type
  --> src/main.rs:47:64
   |
47 | fn returns_future_result() -> impl Future<Output = Result<i32, impl Error>> {
   |                                                                ^^^^^^^^^^ expands to a recursive type
   |
   = note: type resolves to itself
```

**注:** *在写这篇文章的时候，我还在试图调查和理解为什么一个* `impl Error` *不起作用，需要* `Infallible` *。跟 Rust 不能从函数体决定具体实现类型应该是什么有关。一个 MRE 见这个* [*游乐场例子*](https://play.rust-lang.org/?gist=298dd143aa93f824ae85bb2a97ea353b) *。*

相当“不透明”的解决方案(双关语)是使用`std::convert::Infallible`，并将其作为通用类型参数提供给`future::ok`

```
use std::convert::Infallible;fn returns_future_result() -> impl Future<Output = Result<i32, impl Error>> {
    future::ok::<i32, Infallible>(42)
}
```

或者，我们可以让 Rust 简单地推断出`Result`的成功类型:

```
future::ok::<_, Infallible>(42)
```

如果您正在调用另一个函数，并且不想拼出成功类型，这很有用。

或者，我们可以再次使用`Box<dyn ...>`:

```
fn returns_future_result() -> impl Future<Output = Result<i32, Box<dyn Error>>> {
    future::ok(42)
}
```

# 未来链接

到目前为止，我们一直在重复调用`rt.block_on()`,每次都检查结果。

现在，如果出于某种原因，我们希望有一个单独的`rt.block_on()`，它可能调用一个返回一个未来的函数，但是却连续运行几个其他的未来，该怎么办？

也就是说，我们只想:

```
rt.block_on(returns_future_chain());
```

其中`returns_future_chain()`返回一个`Future`，它将连续运行我们以前的所有期货。由于我们不关心输出，我们可以只使用`()`单元类型。

```
fn returns_future_chain() -> impl Future<Output = ()>
```

但是现在我们没有对 Tokio 运行时的引用，所以不能调用`rt.block_on()`(或者`rt.spawn()`)。

这就是`futures`箱派上用场的地方，它为我们提供了“未来组合子”,让我们可以相对轻松地构建未来。

简而言之，下面是使用不同组合符的`returns_future_chain()`的样子:

```
fn returns_future_chain() -> impl Future<Output = ()> {
    future::lazy(|_| debug!("in returns_future_chain()"))
        .then(|_| {
            debug!("in first then");
            future::ready("Hello from rt.block_on()")
        })
        .inspect(|result| debug!("future::ready() -> {}", result))
        .then(|_| returns_impl_future_i32())
        .inspect(|result| debug!("returns_impl_future_i32() -> {}", result))
        .then(|_| returns_dyn_future_i32())
        .inspect(|result| debug!("returns_dyn_future_i32() -> {}", result))
        .then(|_| returns_future_result())
        .map(|result| result.unwrap())
        .inspect(|result| debug!("returns_future_result().unwrap() -> {}", result))
        .then(|_| {
            debug!("in last then");
            future::ready(())
        })
}
```

# 延期期货

为了更好地衡量，让我们写一个函数，在返回之前等待一段时间。为此，我们将使用`tokio::time::delay_for()`:

```
use std::time::Duration;
use tokio::time::delay_for;fn returns_delayed_future() -> impl Future<Output = i32> {
    delay_for(Duration::from_millis(500))
    .then(|_| futures::future::ready(42))
}
```

但是，如果我们试图通过将它传递给`rt.block_on()`来直接使用它:

```
let result = rt.block_on(returns_delayed_future());
debug!("{}", result);
```

我们遇到了一个常见的运行时错误:

```
thread 'main' panicked at 'there is no timer running, must be called from the context of Tokio runtime',
```

原因是`tokio::time::delay_for()`已经期望在 Tokio 运行时下运行。

但是如果我们简单地在传递给`rt.enter()`的块中调用它，我们会得到一个警告:

```
warning: unused implementer of `core::future::future::Future` that must be used
   --> src/main.rs:118:9
    |
118 |         returns_delayed_future();
    |         ^^^^^^^^^^^^^^^^^^^^^^^^^
    |
    = note: `#[warn(unused_must_use)]` on by default
    = note: futures do nothing unless you `.await` or poll them
```

现在，让我们将它添加到我们之前构建的期货链中:

```
 .then(|_| returns_delayed_future())
        .inspect(|result| debug!("returns_delayed_future() -> {}", result))
```

## 通过“奉承”来拖延未来

回忆一下我们在第 1 部分中的[奉承练习:闭包](https://medium.com/@alistairisrael/demystifying-closures-futures-and-async-await-in-rust-part-1-closures-97e531e4dc50#406c)。

现在，有了我们所知道的一切，让我们试着实现一个在评估给定的未来之前等待一秒钟的函数:

```
fn wait_a_sec<F, O>(f: F) -> impl Future<Output = O>
where
    F: Future<Output = O>,
{
    let delay = Duration::from_millis(1000);
    delay_for(delay).then(|_| f)
}
```

让我们试一试:

```
 .then(|_| wait_a_sec(future::ready(42)))
        .inspect(|result| debug!("wait_a_sec(future::ready(42)) -> {}", result))
```

因为我们阻塞了这个期货链的结果，所以我们结束了等待，在打印出`wait_a_sec()`的结果之前应该有一秒钟的明显停顿。

如果我们使用了`.spawn()`,那么不能保证未来会运行完成(因为程序的其余部分可能已经退出了)。

我希望这一章(相当长)对铁锈期货有所帮助。

你可以在 GitHub 上查看附带的源代码:[https://GitHub . com/aisrael/rust-closures-futures-async-await](https://github.com/aisrael/rust-closures-futures-async-await)

在 [**第 3 部分:Async + Await**](https://medium.com/@alistairisrael/demystifying-closures-futures-and-async-await-in-rust-part-3-async-await-9ed20eede7a4) 中，我们将结合到目前为止我们所学的关于闭包和未来的知识来更好地理解 async-await。