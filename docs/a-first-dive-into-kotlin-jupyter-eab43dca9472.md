# 首次潜入科特林-朱庇特

> 原文：<https://levelup.gitconnected.com/a-first-dive-into-kotlin-jupyter-eab43dca9472>

![](img/2439053d91c14bd3c7c2a5dc9ba42fbd.png)

## 或者为什么我发誓不再使用 Python 再一次

这是关于科特林的故事。更具体地说，[kot Lin-jupyter](https://github.com/Kotlin/kotlin-jupyter)——一个用于解决简单科学和可视化问题的交互式笔记本环境。Jupyter 框架最初是为 Python 语言开发的，但后来成为不同服务器(内核)的通用平台。我们有很多不同的内核:Julia，Scala，Java，Groovy，甚至多个 Kotlin 内核，但去年冬天，Roman Belov 在 KotlinConf-2019 上做了一个[演讲，他在演讲中宣布了一个 Jupyter 内核，由 Jetbrains 官方支持，专门为数据科学团队提供 Kotlin。](https://youtu.be/APnyDVye4JA)

这个演讲在不断增长的数据科学和非数据科学社区中产生了很多兴奋，因为这是我们已经等待了一段时间的事情。正如你在[我在同一个会议上的报告](https://www.youtube.com/watch?v=LI_5TZ7tnOE)中看到的(我的报告是在 Roman 的报告之前，所以我不知道它)，缺乏简单的启动和可视化工具是 Kotlin 在科学界采用的最重要的问题。所以我从一开始就是这个想法的支持者之一，然而，采用是一个漫长的过程，所以到目前为止，我还没有使用 kotlin-jupyter 来做或多或少严重的事情(当然，我已经做了几次测试)。

总的来说，我尽量不在工作中使用笔记本，原因如下:

*   没有静态分析意味着没有办法及早发现错误。
*   自动完成功能相当有限。
*   最重要的一点:结果取决于你调用细胞的顺序，所以很容易得到错误和混乱的结果，没有办法理解为什么会发生这种情况。

尽管如此，当你需要快速绘制一些图片时，笔记本还是很有用的，有时我不得不使用它。当然，因为我用它来做一些快速的事情，我的第一选择通常是 Python，因为我熟悉这个过程，而且系统中已经有很多工具。大多数情况下，半小时后我会感到沮丧，并发誓再也不碰 Python，但有时我仍然会得到结果。更频繁使用 Python 的人对它的特性更宽容，每次都告诉我，我只需要知道“如何烹饪它”，但这并没有减少我的沮丧。

# 问题是

这个问题对于粒子物理学来说是相当典型的，我们有一个光谱(在这种情况下，来自 Be-7 同位素上的电子俘获的能量沉积)，它包含由定制函数定义的窄[δ峰](https://en.wikipedia.org/wiki/Dirac_delta_function)和宽峰。然后我们需要[用类似高斯核的东西卷积](https://en.wikipedia.org/wiki/Convolution)这个光谱来模拟探测器的响应。这个问题相当简单，但是熟悉数值方法的人都知道这里面有猫腻。你不能直接将卷积所需的数值积分应用于广义δ函数，因为它的宽度无限窄，会“穿过”任何有限积分网格。这个问题有两个解决方案:

*   用解析积分代替广义函数的数值积分(在δ函数的情况下，卷积的结果只是一个移位的核函数)。
*   用一条窄而有限的高斯线代替一个δ函数，并将其作为一个常规函数进行积分。

第二种解决方案有一个明显的缺点。同样，熟悉数值积分的人都知道，要得到正确的结果，需要使用正确的网格(或初始步长)设置。对于宽函数，必须使用粗网格。对于窄函数，必须使用细网格。如果在一个积分中既有宽函数又有窄函数，那么就有一个问题，这个问题通常只能通过手动分割积分区域以对不同的部分使用不同的网格来解决。如果你有一个很窄的代数线，你可能会有数值精度的问题。

第一种解决方案不适合 Python。至少一般不会。我们想要的是有一个统一的峰值列表和积分器来决定使用哪种算法取决于线的类型。Python 官方没有类型，所以我们要么使用字符串标签，要么有两个单独的列表。这是可以做到的，但看起来并不乐观。

当我们谈论 Python 时，有另一种解决方案，看起来很有希望。Numpy 是用来处理数组的，这也是 Python 和 Numpy 最擅长的。所以:

*   将连续积分替换为离散积分(并通过值的数组起作用),并用单个非零值表示增量函数。

这将解决网格问题，因为它是由初始数组定义的，你不能偶尔错过这个点，这里的增量不为零。此外，它还解决了 Python 的性能问题，因为我们不使用函数(在 Python 中非常慢)，只使用数组(在 Numpy 中非常快)。所以让我们试着去做。

# 蟒蛇皮的浸泡

让我们看看我最后得到的代码:

Python 代码

代码不大。我确信巨蟒剧团可以写得更好，但当一个人需要一个“快速和肮脏”的情节时，这仍然是他想要的。可悲的是，结果是错误的。

![](img/f41091053f52494ae327fea48fc895ed.png)

python 代码的结果

您可以看到，卷积后，δ峰与中心自定义峰相比相当小，而我们预计卷积后的归一化因子将是相同的，因此右线应占总积分的 90%左右。显然，Python 中的卷积函数不仅决定逐个元素地卷积数组，还决定应用归一化因子。因此，我们用`p*step`代替`p`对δ峰的积分。

还请注意一行标有`PITFALL`的注释。如果将`0.0`替换为`0`，自定义峰值消失。惊喜！试着猜猜为什么。

最有可能的回应是 Python 实际上是有类型的，所以当`vectorize`遇到整数常量时，它决定创建一个类型化的整数数组，并进行自动舍入。如果你没有预料到这个陷阱，你可以花几天时间来寻找问题。

在这一点上，我真的很沮丧，并决定，我不会再用 Python 写任何东西了。我可以打开 IDEA 并创建一个小的 Kotlin 项目，但是我决定尝试 kotlin-jupyter 内核。我早就有这个打算了。

> **注**:其实可以像`*curve[k_base_energy/step] =k_base_prob/step*`一样，通过按比例增加峰高来固定 Python 代码。但我是在决定用 Kotlin 重写代码后才发现这一点的，我对这个决定很满意。Python 里有太多可以出错的地方。

# 和科特林一起在未知的水域游泳

所以我决定试试 Kotlin 笔记本。因为我已经有了一个 jupyter 笔记本和 Anaconda 发行版，所以在同一个浏览器页面上很容易做到。我已经安装了 kotlin-jupyter，但是如果没有，你可以通过输入

> conda install-c jetbrains kot Lin-jupyter-kernel

在控制台中，甚至在笔记本电脑中。它就像魔咒一样管用。你可以马上开始你的第一个 Kotlin 笔记本。

现在，我们需要加载库。在我的例子中，我想使用一个来自 [commons-math](https://commons.apache.org/proper/commons-math/) 的集成器，所以我想加载这个 [maven 依赖项](https://mvnrepository.com/artifact/org.apache.commons/commons-math3/3.6.1)。

> **注意**:kot Lin notebook 的一个重要特性是你不必在 pythonic 环境下工作(关于这一点我有很多话要说)！您可以从 Java/Kotlin 依赖管理生态系统中受益，并实际声明在每个笔记本中使用什么依赖，完全解决可再现性问题。

我在直接从 maven-central 加载 commons-math 工件时遇到了一些[问题](https://github.com/Kotlin/kotlin-jupyter/issues/71)(这个问题可能会在下一个版本中得到解决，所以我不会详细讨论它)，但是我决定我可以做得更好，使用整个 kmath-commons 模块(我不需要它来完成这个特定的任务，但是为什么不呢)。Kotlin-jupyter 已经有了描述符(谢谢，Roman！)，但只针对核心模块，不针对 commons-math 绑定。所以我从`~/.kotlin-jupyter/cache/libraries/kmath.json`中取出缓存的描述符，将其复制到`~/.kotlin-jupyter/libraries/kmath-commons.json`中，只修改一行:

> scientifik:kmath-core-JVM:$ v-> scientifik:kmath-commons:$ v

此外，我需要加载一些东西来绘图，所以我写:

> %使用 let-plot
> %使用 kmath-commons

它只是工作:commons-math 是一个可传递的依赖项，它是用 kmath-core 和 kmath-commons 包装器自动加载的。

现在让我们看看代码:

科特林代码

这里我使用了前面的方法 1。我定义了一个具有两个后代的密封类层次结构，并定义了两种不同的卷积方法。用 Python 来做是可能的，但是会更难，调试起来会更难。我为每个分支实现了一个卷积过程，并通过扩展添加了一些额外的功能。它完美地工作。

你可以注意到我用了 commons-math `UnivariateFunction`而不是 Kotlin `(Double)->Double`。有两个原因:首先，我打算在 commons-math integrator 中使用它，所以我需要在 API 中使用它，第二个原因是，使用 Kotlin 原始函数，很容易得到意外的装箱。我在这里写了一点关于那个。

自动完成功能工作得很好，所以我在笔记本上而不是在 IDE 上写代码时没有太多的不适，我相信它在将来会得到进一步的改进。

当然，没有什么是免费的，所以 Kotlin clarity 和笔记本电脑都是有价格的。

*   首先:进口。在 Java/Kotlin 生态系统中，你需要为你使用的任何类或函数添加“import”。所以你可以看到 commons-math 类的导入行。我预料到会有一些问题，但令人惊讶的是，我没有发现任何问题。当然，我不得不在一个单独的窗口中打开一个 commons-math 文档来找到我需要使用的类，但这与 Python 完全相同。Jupyter-kotlin 包名自动完成允许将问题降到最低。还应该记住，commons-math 是一个 Java 库，它不是为笔记本设计的。现代的 Kotlin APIs 是以这种方式编写的，因此它们可以通过像`scientifik.kmath.linear.*`这样的单行“星形”导入来加载。
*   第二个问题是文档。对于来自 Python 的人来说是可以的。使用基于 Python 的笔记本时，通常只需在浏览器中打开文档并定期检查。对于来自美好的 Java IDEs 的人来说，这并不那么舒服。我们被宠坏了，期望通过悬停在方法上，文档应该立即可用。实际上，他们最近甚至为 Python 笔记本提供了这个特性(Jupyter 实验室中的 Shift+Tab)。这是我最怀念的事情。
*   让我们-绘图。JetBrains 团队对这个库投入了大量精力。尽管如此，它还是模仿了 [R 包](https://ggplot2.tidyverse.org/index.html)，如果没有以前的 R 经验和适当的文档(更不用说 API 设计了)，真的很难使用它。我花了很多时间来计算如何正确地编写它，甚至不得不查看[let-plot-kot Lin](https://github.com/JetBrains/lets-plot-kotlin)源代码。最后，我设法得到了一张合适的照片(这正是我想要的照片！)，但是我没有找到快速导出到图形文件并插入到这里的方法，所以我必须做一个截图，这对于一个“快速获取图片”的解决方案来说是不可接受的。

![](img/3a3137e226b6712be72a042db1afa6dd.png)

科特林结果

> **注**:文章写了大半，发现[这一页](https://github.com/JetBrains/lets-plot-kotlin/blob/master/demo/browser/src/main/kotlin/exportSvgDemo/GGBunchSvg.kt)。显然，导出 SVG 是可能的，但是需要更深入地研究源代码。

# TLDR；潜水后干涸

*   在简单和不那么简单的“快速画图”的情况下，kotlin-jupyter 笔记本足以替代基于 Python 的工具。
*   它可以与来自 java 生态系统的非常强大的工具集成，因此用户可以直接在笔记本上完成复杂且对性能敏感的工作。库和模块加载过程甚至比 Python 中的更简单，并且不会导致环境冲突和其他传统的 Python 问题。
*   自动完成和其他工具当然比 IDE 差，但是与 Python notebook 相比，我们看不到任何不足。
*   库可视化支持仍然需要改进(我们希望通过升级我们的 [plotly.kt](https://medium.com/@altavir/plotly-kt-a-dynamic-approach-to-visualization-in-kotlin-38e4feaf61f7) 库来加入这项工作)。
*   如果你有足够的 Python，你可以很容易地切换到 Kotlin 笔记本。

> **注意**:我确信我会立刻得到很多关于如何更好地解决我用 Python 描述的问题的评论。我知道，用 Python 来做这件事是可能的，我以前也做过(当我展示分布卷积时，我甚至在我的统计讲座中描述了一个可能的解决方案)。尽管如此，Kotlin 为重要的任务提供了更加灵活和容易出错的环境。重要的是，在 Kotlin 中，人们并不完全依赖于标准库中的现成解决方案。大多数事情都可以立即在笔记本上实现，而不会对性能产生重大影响。

感谢[彼得·克利迈](https://medium.com/u/c485ff9f868a?source=post_page-----eab43dca9472--------------------------------)和[михаилевгеньевичзелёный](https://medium.com/u/5cca9784bea0?source=post_page-----eab43dca9472--------------------------------)的点评。