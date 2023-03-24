# Julia 中带线程的并行代码

> 原文：<https://levelup.gitconnected.com/parallel-code-in-julia-with-threads-b2e7c97f071b>

提高代码速度的最简单方法:ThreadsX.jl

我从 2014 年左右开始关注茱莉亚语言。Julia 最吸引人的一点是，它从一开始就是为多核机器而构建的(在 Python 中使用 joblib 简直是一场噩梦……)。但是最近，该语言的多线程能力变得更好了，所以我花了一些时间来探索在 2022 年你可以用 Julia 和一些额外的内核做些什么。

![](img/5e9d8f372961c9b72ec5e78abd6395fd.png)

照片由[阿巴斯·特拉尼](https://unsplash.com/@phorgod?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/parallel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 用更多线程启动 Julia

在我们深入探讨可以使用什么来并行运行代码之前，我们必须**确保有可供 Julia** 使用的线程。要检查 Julia 正在运行多少线程，只需运行以下代码:

```
julia> using Base.Threadsjulia> Threads.nthreads()
1
```

为了用更多的线程启动我们的 REPL，我们可以将`--threads`选项传递给 Julia 并获得更多的能力！！！

```
julia --threads 6
```

> 注意:我在这里使用 6 个内核，因为我的机器上有 6 个内核。

如果您使用的是 VSCode，您可以编辑您的`settings.json`文件，通过添加下面一行来指定您希望 Julia 使用的线程数量。

```
"julia.NumThreads": 6
```

# 我们愚蠢的功能

为了演示如何并行运行代码，我们应该首先想出一个我们想要运行的函数，然后**建立我们的单核基础案例**。内核越多并不总是越好，因为并行代码也会带来开销。这些进程必须相互通信，这需要资源和时间。

我想保持简单，所以我们的函数会找到所有能被 12 整除的数字。然后我们将使用这个函数来找出在 1 到 10，000 之间有多少个数字能被 12 整除。我知道这是一个愚蠢的例子，但我们必须从某个地方开始。😅

你可以看到我已经为我们的函数加入了几个**单元测试**来确保我们的函数做了正确的事情。我鼓励你对你所有的功能都这样做。随着事情变得越来越复杂，这将使你的生活变得容易得多。

> 如果你以前从未使用过单元测试，开始在你的 Julia 代码中加入一些`@test`宏吧！这是迄今为止我见过的所有语言中最简单的单元测试框架。

我们将使用来自`BenchmarkTools`的`@btime`来为我们的运行时间获得一个好的时间估计，我将展示一些不同的方法来将这个函数应用到更多的数字:

正如你所看到的，仅仅通过不使用广播`.`和利用`sum`有多个相关方法的事实，我们已经可以减少 40%的时间。上面`sum`的第一个用法是取一个数组，并对其求和。第二种方法采用一个函数，将它应用于一个 iterable 的所有元素，并将这些结果相加。

> 请务必阅读关于您正在使用的功能的文档。可能有一种方法可以让你的代码更快更优雅！

# 朴素并行 for 循环

现在我们已经有了一个基准，让我们看看如何让它并行运行。我们的第一次尝试将使用`Threads`,我把它放在这里，因为这可以抓住一些初学者。

现在你可能认为这段代码没有问题，但这正是拥有一个单一核心基线对我们有所帮助的地方。如果你运行上面的代码，你会得到一个与我们简单的`sum`解决方案不同的答案。事实上，每次运行这个函数时，您可能会得到不同的答案，而这个答案与真实答案相差甚远:

```
julia> sum(ismultiple12.(1:N))
833julia> for _ in 1:10
           println(sumupto(ismultiple12, N))
       end
263
265
329
268
322
479
382
270
484
361
```

这是因为我们的变量`s`不是原子的。这意味着一个线程可能在另一个线程试图访问它的同时将其值从`0`更新到`1`。因此会发生以下情况:

1.  当`s`为 0 时，线程 1 获取其值
2.  Thread2 得到了`s`的值，它仍然是 0
3.  Thread1 加 0 + 1 并将`s`的值更新为 1
4.  Thread2 加 0 + 1 并将`s`的值更新为 1 — **它不知道 Thread1 已经给它加了 1**。

所以很明显我们应该两次给`s`加 1，但是我们搞砸了。你可以试着把`s`做成一个原子数组并解决这个问题，但是这对我们的例子来说是混乱和不必要的，所以我会让你去阅读 Julia 文档中的相关内容。此外，还有一个更简单的方法…

# ThreadsX.jl 简介

[](https://github.com/tkf/ThreadsX.jl) [## GitHub - tkf/ThreadsX.jl:并行化的基本函数

### 添加前缀 ThreadsX。如果支持的话，调用 Base 中的函数来获得一些加速。示例:要了解支持的功能…

github.com](https://github.com/tkf/ThreadsX.jl) 

ThreadsX.jl 是一个开源的 Julia 包，同样令人印象深刻，使用简单。它以并行的方式实现了通用的基本 Julia 函数，例如`sum`、`map`、`findall`、`minimum`以及许多其他函数。使用这些超级简单，只需用`ThreadsX`版本替换你的函数调用:

你不必担心比赛条件，只需申请和享受。🎉

> 感谢开发人员制作了这个包！在 [GitHub](https://github.com/tkf/ThreadsX.jl) 上查看该包！

# 但是我们更快了吗？

![](img/f393f3b1b1276b5df4142aea77474607.png)

照片由[михаилпавленко](https://unsplash.com/@pavlenko?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在我们有两个并行的实现，但是我们有没有更快呢？**我们正在使用这些内核吗？**让我们做一些基准测试:

```
julia> [@btime](http://twitter.com/btime) sum(ismultiple12, 1:N)
  5.712 μs (4 allocations: 112 bytes)
833julia> [@btime](http://twitter.com/btime) ThreadsX.sum(ismultiple12, 1:N)
  36.061 μs (423 allocations: 30.86 KiB)
833
```

如你所见，我们得到了正确的答案，但实际上我们变得慢了很多。好像慢了 6 倍。这是因为我们需要协调所有这些核心。

我们犯了计算机编程的一个大罪:**过早优化**！让这成为你的警告，如果你的功能足够快，你不需要让它更快。而是做一些有用的事情。😉

# 是的，我们更快！

![](img/d32451dd8c31a4982097438a7fe0deea.png)

照片由 [Sammy Wong](https://unsplash.com/@vr2ysl?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在，为了演示一个更真实的场景，我们将把我们的函数复杂化。这将确保并行计算的**收益可以抵消内核协调的开销**。我仍然想保持简单，所以我们将寻找增加每个整数的质数，直到 10，000。为此，我们将使用 Primes.jl，并且我们将在前期进行通常的单元测试:

使用这个新函数运行我们的 sum 函数会得到以下结果:

```
julia> @btime sum(num_prime_divisors, 1:N)
  734.400 μs (10037 allocations: 1.23 MiB)
24300

julia> @btime ThreadsX.sum(num_prime_divisors, 1:N)
  163.300 μs (10518 allocations: 1.26 MiB)
24300
```

我们获得了 4.5 倍的加速，这意味着我们的 6 个线程最终得到了利用。

# 再举一个例子

![](img/0bea93b377390b5b1bd88843b501b3f4.png)

由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

从技术上讲，这不是本文所必需的，因为我已经展示了如何使用 ThreadsX 轻松地并行化一些单线程 Julia 函数。然而，有一个叫做[exercism.org](http://exercism.org)的很棒的网站，上面有一些非常好的练习让你学习茱莉亚。我最喜欢的运动之一是拼字游戏评分。一件事接着一件事，我发现自己在问这样一个问题:“莎士比亚在拼字游戏《罗密欧与朱丽叶》中能得多少分？”。我们能同时计算他的分数吗？

首先，我们需要一些函数来计算每个字符的分数:

这里的评分函数看起来有点复杂，所以让我解释一下:

*   `mapreduce`将对每封信应用匿名函数`letter -> get(scores, letter, 0)`。这将尝试在我们的字典中查找每个字母，并把它变成一个数字。如果在字典里找不到，我们就用 0 代替。这个功能就是`map`部分
*   下一个操作符`+`意味着我们将把所有结果相加。这是`reduce`的部分。这可以是对 2 个输入进行操作的任何函数。
*   第三个参数是我们想要减少的文本。
*   最后，我们从 0 开始计数，因此我们的累加器应该从那里开始。这在向`mapreduce`传递空字符串的情况下很有用。

当得到罗密欧与朱丽叶文本时，我不会费心去清理编辑的名字等，我宁愿只使用`Pipe`包来写一些整洁的代码并继续工作:

现在我们已经准备好并行运行它，因为 ThreadsX 已经实现了`mapreduce`，所以这非常简单:

基准时间:

```
julia> @btime score(romeoAndJuliet)
  5.770 ms (136306 allocations: 2.48 MiB)
184297

julia> @btime score_parallel(romeoAndJuliet)
  1.976 ms (227097 allocations: 3.91 MiB)
184297
```

# 结论

总之，如果你想让 Julia 充分利用你的计算机内部的内核，可以看看 ThreadsX.jl，它可以消除线程化代码的麻烦，并让你的速度得到快速提升。

你还可以尝试很多其他功能，只需在 REPL 中键入`ThreadsX.`并点击 tab 键，就可以看到他们还有什么功能。

```
julia> ThreadsX.
Implementations  all              count            foreach          mapreduce        sort
MergeSort        any              extrema          issorted         maximum          sort!
QuickSort        collect          findall          map              minimum          sum
Set              copy!            findfirst        map!             prod             unique
StableQuickSort  copyto!          findlast         mapi             reduce
```

如果你觉得 exercism.org 的练习很有趣，那就去看看吧。我还有一篇文章，是关于我在那里完成一系列 Julia 练习后学到的所有东西的:

[](/15-things-i-learned-about-julia-after-completing-all-easy-exercism-tasks-b982643d79df) [## 完成所有简单的锻炼任务后，我对 Julia 了解了 15 件事

### Julia 语言的一些很酷的特性

levelup.gitconnected.com](/15-things-i-learned-about-julia-after-completing-all-easy-exercism-tasks-b982643d79df) 

> 我写朱莉娅和其他很酷的东西。如果你喜欢这样的文章，请考虑关注我。要获得所有媒体文章的完整访问权限，包括我的文章，请考虑在此订阅。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)