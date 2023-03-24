# 播放声音🔔当你的朱丽亚函数完成时

> 原文：<https://levelup.gitconnected.com/play-a-sound-when-your-julia-function-is-finished-3daad599f174>

## 当你的脚本完成时得到一个声音通知

![](img/3596c91c4e9ff6373b0d7d3fd855dd08.png)

照片由[在](https://unsplash.com/@theblowup?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/bell?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上放大

很久很久以前，我使用 r。有时我会有一些函数或**进程需要很长时间才能完成**，所以我会在等待结果的时候离开，在电脑上做些别的事情。问题是我从来不知道**脚本什么时候会结束运行**。幸运的是，R 中有一个`beep()`函数，当执行时**会发出声音。所以我想，为什么不在朱莉娅身上做同样的事情呢？😉**

> 当您没有足够的屏幕来关注您的长时间运行的脚本时，在您的计算机上播放一些声音是有用的。

[](https://towardsdatascience.com/build-your-first-neural-network-with-flux-jl-in-julia-10ebdfcf2fa3) [## 在 Julia 中用 Flux.jl 构建你的第一个神经网络

### 没有任何外部数据的初学者教程

towardsdatascience.com](https://towardsdatascience.com/build-your-first-neural-network-with-flux-jl-in-julia-10ebdfcf2fa3) 

# 制造一些噪音！

![](img/b1f8d2884b6176f78a238ec798f5f550.png)

由 [Roméo A.](https://unsplash.com/@gronemo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/mario?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

由于前面提到的 R 包有一些马里奥的声音，这也是我想要的。幸运的是，我[找到了这个网站](https://themushroomkingdom.net/media/smb/wav)，那里有一堆`wav`格式的声音，所以我很快**和 Julia** 一起下载了这些声音:

是的，在 Julia base 中有一个`download`功能，可以让你将互联网上的文件直接下载到本地机器上的一个文件名中。

接下来，我们必须以某种方式播放这些声音。为此我们有`WAV.jl`包，你可以在 REPL 输入`]`后运行`add WAV`或者运行`import Pkg; Pkg.add("WAV")`来添加。

> WAV 包的 GitHub repo 可以在[这里](https://github.com/dancasimiro/WAV.jl)找到。

要播放声音，我们可以用`wavplay`直接从磁盘播放，也可以先读入然后播放:

注意，`wavread`返回一组浮点和其他位，我们需要将它们分别传递给`wavplay`。这就是索引的意义所在。

上面有点难看，我们把它抽象掉，变成一个`beep()`函数:

要使用它，我们只需选择一个下载的声音，并播放一些好的老马里奥兄弟的声音:

```
beep("game_over")
beep("coin")
beep("stage_clear")
```

# 用宏启动！

![](img/de547749dd27a5083ec6510d1af795e4.png)

照片由[在](https://unsplash.com/@geekyshots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/mario?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的极客拍摄

我不喜欢用对`beep()`的函数调用来破坏我的代码，所以我想我会把这整个事情变成一个宏。**我不是 Julia macros 的专家**——尽管我发现元编程很吸引人——所以对这里的一切都要有所保留。我在这里基本上使用宏作为 Python 中的函数装饰器，所以只有基本的东西。

以下是我如何设法把`beep()`变成`@beep`的:

这将评估原始表达式，收集其结果，然后播放声音。你可以这样称呼它:

```
@beep println("hello") "coin"
```

它还将保留返回值，因此您可以将它赋给任何您想要的值:

```
# test function that takes longer to run
**julia>** fib(n) = n < 2 ? 1 : fib(n-2) + fib(n-1)
fib (generic function with 1 method)**julia>** x = [@beep](http://twitter.com/beep) 10 "coin"
10**julia>** y = [@beep](http://twitter.com/beep) fib(40) "game_over"
165580141**julia>** println("x is $x and fib(40) is $y")
x is 10 and fib(40) is 165580141
```

为了设置默认声音，我制作了第二个宏，作为上面宏的一个特殊变体——如果你知道更好的方法，请在评论中告诉我！🙏我很乐意接受如何让这一切变得更好的建议。

```
**julia>** @beep fib(40)
165580141
```

# 去收集所有的硬币！

谢谢你一直读到最后。我希望你能从 [**GitHub**](https://github.com/niczky12/medium/blob/master/julia/finish_sounds.jl) 中取出我的`@beep`宏，让甜美的马里奥声音引导你从 YouTube 或 Medium 回到 Julia 控制台😉！

[](/progressbars-tqdm-and-likes-in-julia-f5102cdd1841) [## Julia 中的进度条(tqdm 和 likes)

### 注意你的 for 循环

levelup.gitconnected.com](/progressbars-tqdm-and-likes-in-julia-f5102cdd1841) [](https://towardsdatascience.com/memoize-and-cache-in-julia-48aa98f6549) [## Julia 中的内存和缓存

### 使用动态编程让你的函数更快

towardsdatascience.com](https://towardsdatascience.com/memoize-and-cache-in-julia-48aa98f6549) 

> 我写朱莉娅和其他很酷的东西。如果你喜欢这样的文章，请考虑关注我。要获得所有媒体文章的完整信息——包括我的——可以考虑在这里订阅[](https://niczky12.medium.com/membership)**。**