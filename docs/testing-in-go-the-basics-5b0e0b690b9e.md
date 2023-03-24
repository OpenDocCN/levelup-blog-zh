# 围棋测试——基础

> 原文：<https://levelup.gitconnected.com/testing-in-go-the-basics-5b0e0b690b9e>

![](img/6d433f99096ea587ddf4f986010d3c86.png)

由[罗马魔术师](https://unsplash.com/@roman_lazygeek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这个博客是[**Just full Go**系列](https://medium.com/@abhishek1987/just-enough-go-blog-series-c1cd62b04beb)的一部分，通过几个例子来介绍`Go`中的测试。它涵盖了测试的基础知识，然后是子测试、表驱动测试等主题。

> *代码可在* [*【刚够走】*](https://github.com/abhirockzz/just-enough-go)*`*GitHub*`上回购*

# *概观*

*对测试的支持以`testing`包的形式内置到 Go 中。至少，您需要:*

*   *写点代码(需要测试的那个！)例如`hello.go`*
*   *在以`_test.go`结尾的文件中编写测试，例如`hello_test.go`*
*   *确保测试函数名称以`Test_`开头，例如`func TestHello`*
*   *运行您的测试！*

*在编写测试时，您将大量使用`[*testing.T](https://golang.org/pkg/testing/#T)`，其中*“是传递给测试函数的类型，用于管理测试状态和支持格式化的测试日志。”*它包含几种方法，包括`Error`、`Fail`(变化)报告错误/故障、`Run`运行子测试、`Parallel`、`Skip`等。*

*博客的其余部分使用一个简单的例子来演示上述一些概念。这是一个典型的 hello world 应用程序！*

```
*package mainimport "fmt"func main() {
    fmt.Println(greet(""))
}func greet(who string) string {
    if who == "" {
        who = "there"
    }
    return fmt.Sprintf("hello, %s!", who)
}*
```

# *你好测试！*

*下面是对`greet`函数的基本单元测试:*

```
*func TestGreet(t *testing.T) {
    actual := greet("abhishek")
    expected := "hello, abhishek!"
    if actual != expected {
        t.Errorf("expected %s, but was %s", expected, actual)
    }
}*
```

*目标是确认使用特定名称调用`greet`是否会导致`hello, <name>!`。我们调用`greet`函数，将结果存储在一个名为`actual`的变量中，并将其与`expected`值进行比较——如果它们不相等，`Errorf`用于记录一条消息，并将测试标记为失败。但是，测试函数本身会继续执行。如果您需要改变这种行为，使用`FailNow`(或`Fatal` / `Fatalf`)来终止当前的测试，并允许剩余的测试(如果有的话)执行。*

# *子测试*

*我们已经讨论了明显的用例，但是还有另一个场景需要测试——当输入是空字符串时。让我们使用另一个测试来添加它:*

```
*func TestGreetBlank(t *testing.T) {
    actual := greet("")
    expected := "hello, there!"
    if actual != expected {
        t.Errorf("expected %s, but was %s", expected, actual)
    }
}*
```

*另一种方法是使用`*testing.T`上的`[Run](https://golang.org/pkg/testing/#T.Run)`方法来使用`sub-tests`。这种情况下会是这样的:*

```
*func TestGreet2(t *testing.T) {
    t.Run("test blank value", func(te *testing.T) {
        actual := greet("")
        expected := "hello, there!"
        if actual != expected {
            te.Errorf("expected %s, but was %s", expected, actual)
        }
    }) t.Run("test valid value", func(te *testing.T) {
        actual := greet("abhishek")
        expected := "hello, abhishek!"
        if actual != expected {
            te.Errorf("expected %s, but was %s", expected, actual)
        }
    })
}*
```

*测试逻辑保持不变。但是，现在我们已经涵盖了单个函数中的各个场景，每个场景都被表示为一个子测试— `test blank value`和`test valid value`。`Run`方法接受一个`name`和一个类似于顶层父测试用例/函数的测试函数。所有测试都按顺序运行，当子测试完成执行时，顶层测试被认为完成。*

*这样做有什么好处？仅仅是不使用单独的函数吗？是的，但是使用子测试有更多的好处*

*   *与一个功能/方法/功能相关的所有案例都可以放在一个单一的测试功能中——这大大减少了认知负荷*
*   *显式命名使得发现故障变得更加容易——这就是 esp。在大型测试套件中很有用*
*   *只需在子测试之前和之后编写(通用的)安装和拆卸代码*
*   *您有能力并行运行子测试(与父测试中的其他子测试一起)——稍后会详细介绍*

# *表格驱动测试*

*我们的测试用例遵循相同的模板——我们所做的就是用一个参数调用`greet`,并将结果与`expected`的结果进行比较。两个子测试都有重复的代码。虽然，这是一个微不足道的例子，但这在现实世界的项目中也会发生。*

*`table driven tests`在这种情况下可以派上用场。这一切都是为了在你的测试代码中找到可重复的模式，并设置`tables`来定义用例的不同组合——这极大地减少了测试代码的重复和大量的复制+粘贴工作！这些`tables`可以表现为一片结构，每个结构定义输入参数、预期输出、测试名称和任何其他相关细节。*

*下面是我们如何设置`table driven tests`:*

```
*func TestGreet3(t *testing.T) {
    type testCase struct {
        name             string
        input            string
        expectedGreeting string
    } testCases := []testCase{
        {name: "test blank value", input: "", expectedGreeting: "hello, there!"},
        {name: "test valid value", input: "abhishek", expectedGreeting: "hello, abhishek!"},
    } for _, test := range testCases {
        test := test
        t.Run(test.name, func(te *testing.T) {
            actual := greet(test.input)
            expected := test.expectedGreeting
            if actual != expected {
                te.Errorf("expected %s, but was %s", expected, actual)
            }
        })
    }
}*
```

*让我们分解一下，以便更好地理解它。我们从定义`testCase`结构开始...*

```
*type testCase struct {
 name             string
 input            string
 expectedGreeting string
}*
```

*…随后是测试用例，这些测试用例是带有名称、输入和预期输出的`testCase`(`table`)片段:*

```
*testCases := []testCase{
        {name: "test blank value", input: "", expectedGreeting: "hello, there!"},
        {name: "test valid value", input: "abhishek", expectedGreeting: "hello, abhishek!"},
    }*
```

*最后，我们简单地执行每一个测试用例。注意名称、输入和输出是如何分别与`test.name`、`test.input`和`test.expectedGreeting`一起使用的*

```
*for _, test := range testCases {
        test := test
        t.Run(test.name, func(te *testing.T) {
            actual := greet(test.input)
            expected := test.expectedGreeting
            if actual != expected {
                te.Errorf("expected %s, but was %s", expected, actual)
            }
        })
    }*
```

# *平行测试*

*在大型测试套件中，我们可以通过并行运行每个子测试来提高效率。我们需要做的就是在`*testing.T`上使用`Parallel`方法来表明我们的意图。下面是我们如何并行化我们的两个测试用例:*

> **注意对*的调用`*te.Parallel()*`*

```
*for _, test := range testCases {
        test := test
        t.Run(test.name, func(te *testing.T) {
            **te.Parallel()**
            time.Sleep(3 * time.Second)
            actual := greet(test.input)
            expected := test.expectedGreeting
            if actual != expected {
                te.Errorf("expected %s, but was %s", expected, actual)
            }
        })
    }*
```

*因为我们的测试很短，所以特意添加了`time.Sleep`来模拟耗时操作，作为测试的一部分。当您运行这个测试时，您会注意到测试执行的总时间稍微超过`3s`，尽管每个测试休眠了`3s`——这表明测试是并行运行的。*

# *其他主题*

*这里有一些其他有趣的话题，你应该探索，但没有涵盖在这篇文章中。*

*   *`Benchmark`:在`*testing.B`类型的帮助下，`testing`包提供了运行基准的能力。就像正常的测试功能从`Test`开始一样，基准从`Benchmark`开始，可以使用`go test -bench`来执行。他们看起来像:`func BenchmarkGreet(b *testing.B)`*
*   *`Skip`(及其变体):调用这个来跳过一个基准测试*
*   *`[Cleanup](https://golang.org/pkg/testing/#T.Cleanup)`:测试和基准测试可以用这个来*“注册一个函数，当测试及其所有子测试完成时调用这个函数”**
*   *[测试中的例子](https://golang.org/pkg/testing/#hdr-Examples):你也可以包含代码作为例子，测试包也会验证它们。*

*这就结束了**Just full Go**博客系列的又一部分——敬请期待更多。如果你觉得这很有用，别忘了点赞和订阅！*