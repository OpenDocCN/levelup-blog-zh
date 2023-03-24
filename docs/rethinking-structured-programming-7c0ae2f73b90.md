# 重新思考结构化编程

> 原文：<https://levelup.gitconnected.com/rethinking-structured-programming-7c0ae2f73b90>

## 编程的演变

![](img/0080148c8e99d814feeabb8bc4630317.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=692959) 的[强尼·香农](https://pixabay.com/users/jstarj-884623/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=692959)

在我最近的文章中，[所有的循环都是一种代码味道](https://medium.com/swlh/all-loops-are-a-code-smell-6416ac4865d6)，我断言循环的正常形式，`for`，`while`等等，是应该避免的低级编码构造。虽然对这篇文章的总体反应是好的，但也有一些明显不买账的人大声反对。所以我想我应该多花点时间复习一下我的推理。

让我从结构化编程的概念开始。关于结构化编程的早期想法始于 20 世纪 50 年代末。 [Edsger W. Dijkstra](https://en.wikipedia.org/wiki/Edsger_W._Dijkstra) 在 1968 年写了一封公开信“[转到被认为有害的语句](https://en.wikipedia.org/wiki/Go_To_Statement_Considered_Harmful)”，在信中他恳求程序员放弃`goto`语句，转而支持更加可控的应用程序路由，例如`if`和`while`语句以及独立的子例程。Donald Knuth 在 1974 年写了一篇反驳文章《使用 go to 语句的结构化编程》。但它主要是关于优化每个 CPU 周期和保持“可证明性”，高级程序员应该推迟优化，直到显示需要。当然,`goto`语句今天仍然作为编码出口存在，但是通常被认为是一种代码味道。

当我在 1989 年第一次开始专业编程时(我记得是因为那年我儿子出生了)，仍然有人认为`goto`是一种合适的编码形式。结构化编程的早期采用者 P. J. Plauger 说:

> *我们这些皈依者在顽固不化的汇编语言程序员的眼皮底下挥舞着这条有趣的新闻，这些程序员不停地炫耀扭曲的逻辑，并说，“我打赌你不能构建这个。”无论是 bhm 和 Jacopini 的证明，还是我们在编写结构化代码方面的多次成功，都没有比他们准备好说服自己的时间早一天让他们改变主意。*

因此，我一直在思考结构化编程的概念，以及哪些部分现在可能被认为是可以隐藏在某个库中的低级抽象。goto 语句从未消失，它仍然在机器代码级别使用。但是我们大多数人都没有达到那个水平。也许高阶函数可以使结构化编程的一些构造像 goto 语句一样不必要，只在极少数情况下使用。

子例程，今天也称为方法和函数，仍然是日常编程的一部分，并且很可能仍然是应用程序分解的基石。事实上，有人可能会说它们是函数式编程不可或缺的一部分。

`if`语句更复杂，但是仍然有很多情况下我们可以去掉它。以 Java 等语言中的空检查为例。可以很容易地用 Java 可选类型替换它:

```
// ancient system calls returns the evil null
  String env = System.getenv("SOME_UNDEFINDED_ENV"); 
  if(env != null) {
    env = env.toUpperCase();
    System.out.println("using if env = " + env);
  } else {
    System.out.println("using if env = null");
  }

  System.out.println("using Optional 1 env = "
     + Optional.ofNullable(System.getenv("SOME_UNDEFINDED_ENV"))
   .map(e -> e.toUpperCase())
   .orElse("null"));
```

Optional 是一个非常方便的对象，尽管它可能会被误用。使用`map`高阶函数可能会让一些人感到困惑。考虑可选的方式是作为 0 或 1 个对象的列表。在许多函数式语言中，它有几乎和列表一样的方法集。我们可以通过将 Java 的 Optional 转换成一个流来解决高阶方法的缺乏:

```
Optional.ofNullable(System.getenv("SOME_UNDEFINDED_ENV"))
                .stream()
                .map(e -> e.toUpperCase())
                .forEach(e -> System.out.println("env = " + e));
```

但是如果你这样做了，如果选项是空的，你就失去了做不同事情的能力。

在 Java 中，你可以使用 [projectreactor.io](https://projectreactor.io/) 提供的 Mono 类型。单声道类型是可选的，但是有非常丰富的方法:

```
Mono.justOrEmpty(System.getenv("SOME_UNDEFINDED_ENV"))
  .map(e -> e.toUpperCase())
  .defaultIfEmpty("null")
  .subscribe(e -> System.out.println("using Mono env = " + e));
```

有关 projectreactor.io 的更多信息，请参见我的文章[非阻塞 Java——超越宣传](/non-blocking-java-beyond-the-hype-dfdc405848d7)。

`null`检查是一个特例，因为它可能经常发生，如果错过，可能会导致可怕的空指针异常。但是其他形式的`if`语句就不那么容易捕捉了。

loop 语句的一种形式是输出为输入的循环，这种形式的循环不会被引入到高阶函数中。以计算平方根的巴比伦方法为例。它接受一个种子值，然后迭代对输入的操作，直到输出与输入相同(近似)。

为什么我如此渴望看到循环和条件句消失？首先，对于每个崇拜代码覆盖的人来说，如果没有循环或条件，那么只有一条代码路径需要覆盖。代码覆盖率已解决。第二，这是一些反对我的第一篇文章的人提出的，我们所做的就是把循环推到一个库中。没错。为什么要写那些在库中为你编写的代码，并且希望有合适的测试覆盖率。我最喜欢的一句话是“如果你 95%的代码在某个库中，而那个库有 100%的代码覆盖率，那么你的应用程序有 95%的代码覆盖率。”只要确保当你使用一个库时，它有足够的代码覆盖率。这是我几乎不再浪费时间为我的应用程序代码编写单元测试的原因之一。但那是另一篇文章了。

提及的文章:

[](https://medium.com/swlh/all-loops-are-a-code-smell-6416ac4865d6) [## 所有的循环都是代码味道

### for、while 及其同类之死。

medium.com](https://medium.com/swlh/all-loops-are-a-code-smell-6416ac4865d6) [](https://medium.com/better-programming/functional-programming-from-an-object-oriented-perspective-9b47100b488a) [## 从面向对象的角度看函数式编程

### 为什么我正在慢慢抛弃面向对象的过去

medium.com](https://medium.com/better-programming/functional-programming-from-an-object-oriented-perspective-9b47100b488a) [](/non-blocking-java-beyond-the-hype-dfdc405848d7) [## 非阻塞 Java——超越宣传

### 集装箱化世界中阻塞与非阻塞的比较

levelup.gitconnected.com](/non-blocking-java-beyond-the-hype-dfdc405848d7)