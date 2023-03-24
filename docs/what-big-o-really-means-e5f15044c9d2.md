# “大 O”的真正含义是什么

> 原文：<https://levelup.gitconnected.com/what-big-o-really-means-e5f15044c9d2>

## 大多数时候，大 O 符号的用法都有点不正确。

![](img/eda822505ebcef17179707ecf0831a37.png)

照片由 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[émile Perron](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄。

想象一下你在下面的情况:你是一名招聘人员，正在面试两个软件工程师职位的候选人。因此，您向他们展示包含线性搜索算法的标准实现的 Java 代码，并让他们告诉您使用大 O 符号有多高效:

考生 A 检查完代码后告诉你，这个算法是 *O(n)* 。另一方面，候选人 B 告诉你是 *O(n )* 。根据他们的回答，你认为这两个候选人中哪一个可能更适合这份工作？

可能是候选人 A 吧？

但是如果我告诉你两个答案都是正确的呢？

# 大 O 符号代表一个上限

是真的。候选 B 在技术上也是正确的，因为根据定义，大 O 符号实际上代表一组数学函数的上界。当你说一个函数 *f(n)是 O(g(n))，*真正的意思是 *f(n)* *并不比 *g(n)* 有更高的增长阶*。

非正式地说， *O(g(n))* 可以定义为包含所有不**比 *g(n)* 增长更快的函数的*数学函数集*。因此，以下所有函数都在集合 *O(n ):* 中**

*   ***f(n) = n +3n + 2***
*   ***f(n) = n log(n)***
*   ***f(n) = 3n+1***
*   ***f(n) =n***
*   ***f(n) = log(n)***
*   ***f(n) =1***

**另一方面， *f(n)=n* 不在 *O(n )* 中，因为 *f(n)=n* 比 *g(n)=n .* 增长得快**

**再次考虑上面 Java 代码中的例子。在最坏的情况下，该算法执行 *n* 次比较。考生 A 说是 *O(n)* 显然是对的。但是考生 B 说算法是 *O(n )* 的时候*也是*正确的，因为，确实， *f(n)=n* 包含在 *O(n )* 中。尽管如此，他可能不会得到这份工作。**

# **紧边界和大θ**

**问题是，许多人认为大 O 是对算法效率的准确描述，而根据定义，这种符号只能代表该效率的上限。如果你说一个算法是 *O(n)，*你不够精确。从技术上讲，同样的算法也可以同时是 *O(n)、O(log(n))* 和 *O(1)* ，这里仅举几个可能性。**

**如果招聘人员在面试中问你一个给定算法的效率，你可以用一个不必要的高上限来回答，比如 *O(nⁿ)* ，你可能在任何面试中都是正确的。但这绝不会帮助你得到这份工作，因为招聘人员并不是真的期望你提供算法效率的上限。当人们询问大 O 符号时，他们通常想知道描述该算法效率的*最低*上限是多少，在上面的例子中是 *O(n)* 。这就是通常所说的“ ***紧界****”——*如果我们再把它变小，就不能用来描述算法的效率*。***

**实际上有一个类似于大 O 的符号，确切地代表了招聘人员对候选人的期望。它被称为 ***大θ****(θ)*符号，用来表示函数的紧界。说 *f(n)是θ(g(n))*是指 *f(n)* 与 *g(n)* 有*相同的增长顺序。几个例子:*****

*   **n∈θ(n)**
*   **2n∈θ(n)**
*   **n+3n+2∈θ(n)**
*   **∉θ(n)**
*   **∉θ(n)。**

**再次考虑上面的例子，但是现在想象你重新表述你的问题，并且根据大 Theta 符号询问两个候选人关于算法的效率。如果考生 A 说算法运行在*θ(n)*，他就又一次正确了。但是，如果候选人 B 说它符合*θ(n)*，那么他就绝对错了，你的结论是正确的，他可能不适合这份工作。**

**当人们谈论算法的效率时，大多数时候他们感兴趣的是获得该效率的严格界限。但是，他们通常不使用大θ，而是使用大 o。我猜，在某个时候，有人认为说“n 的 oh”比说“n 的θ”听起来好得多，这最终成为了规范。**

**幸运的是，这没什么大不了的。这只是人们用一个符号( *O* )来表示紧密界限的问题，这个符号不同于约定俗成的符号(*θ*)。尽管如此，如果有人不能从上下文中判断出大 O 是用来表示上界还是紧界，有时还是会引起不必要的混乱。**

# **大ω**

**除了大 O 和大θ，还有第三种渐近符号经常在算法分析中使用: ***大ω***(ω)。这可以理解为大 O 的逆，因为它代表了一组数学函数的下界:*ω(g(n))*是*不具有比* *g(n)* 低的增长顺序 *的所有函数的集合。比如*n∈ω(n)*和*n∈ω(n)*，但是*n∉ω(n)*。***

*虽然大 O 和大 Theta 大多用于描述算法的效率，但大 Omega 通常用于描述计算问题的*内在复杂性*。例如，在一个未排序的数组中搜索一个元素的问题被称为*ω(n)*，因为在最坏的情况下，任何算法都需要检查*至少*数组中所有的 *n* 个位置，然后才能确定所搜索的元素不在那里。这意味着不可能为那个问题设计一个运行时间小于*θ(n)*的算法。*

*了解大ω其实更容易定义大θ:*θ(g(n))*是 *O(g(n))* 和*ω(g(n))*的交集。一个函数 *f(n)* 在*θ(g(n))*中当且仅当它同时在*ω(g(n))*和 *O(g(n))中。**

# *数学定义*

*在上面的讨论中，我试图描述这些渐近符号的含义，而没有真正进入它们的数学形式。如果你有兴趣了解更多，看看我的文章“[“大 O”背后的数学和其他渐近符号](https://towardsdatascience.com/the-math-behind-big-o-and-other-asymptotic-notations-64487889f33f)”。*

*[](https://towardsdatascience.com/the-math-behind-big-o-and-other-asymptotic-notations-64487889f33f) [## “大 O”和其他渐近符号背后的数学

### 像“大 O”、“大ω”和“大θ”这样的符号的正式定义。

towardsdatascience.com](https://towardsdatascience.com/the-math-behind-big-o-and-other-asymptotic-notations-64487889f33f)* 

# *总结和最终想法*

*很多时候，人们用大 O 来形容紧界。然而，根据定义，大 O 是一种描述上界的形式主义，而不是紧界。如果一个给定的算法是 *O(n)* ，也可以说是 *O(n )* 、*O(n)*等无限个效率类。类似的符号 Big Theta(θ)在描述给定算法的效率方面做得更好，因为它提供了一种准确描述严格界限的方法。然而，许多人从未听说过大θ，因为它在实践中很少使用。*

*请不要误解我。我的意图不是发起一场讨伐，强迫所有人在描述一个算法的效率时只使用大θ而不使用大 O。我自己不这样做，正如我之前所说，我不认为这种对大 O 的误解有什么大不了的。尽管如此，我相信分享符号背后的理论是值得的，因为我确信有许多人经常使用大 O 符号，但不熟悉它的正式定义。*

# *参考*

*   *[大西塔符号](https://www.khanacademy.org/computing/computer-science/algorithms/asymptotic-notation/a/big-big-theta-notation)，在可汗学院。*
*   *[Anany Levitin 的设计介绍&算法分析](https://www.amazon.com/gp/product/0201743957/ref=as_li_tl?ie=UTF8&tag=chaulio0b-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=0201743957&linkId=aff0d43922d6a31ae64de969b8441371)。*

**披露:此帖子包含一个或多个来自亚马逊服务有限责任公司协会计划的链接。作为代销商，我从通过这些链接购买的商品中获得佣金，客户无需支付额外费用。**

# *更多由同一作者*

*[](/8-ways-to-measure-execution-time-in-c-c-48634458d0f9) [## C/C++中测量执行时间的 8 种方法

### 不幸的是，没有放之四海而皆准的解决方案。在这里你会找到一些可用的选项。

levelup.gitconnected.com](/8-ways-to-measure-execution-time-in-c-c-48634458d0f9) [](https://towardsdatascience.com/version-control-with-git-get-started-in-less-than-15-minutes-696b4ce7ce92) [## 使用 Git 进行版本控制:不到 15 分钟即可开始

### 完全初学者的循序渐进教程。

towardsdatascience.com](https://towardsdatascience.com/version-control-with-git-get-started-in-less-than-15-minutes-696b4ce7ce92) [](https://towardsdatascience.com/two-simple-steps-to-create-colorblind-friendly-data-visualizations-2ed781a167ec) [## 创建色盲友好数据可视化的两个简单步骤

### 你的剧情可能很多人都无法理解。以下是解决这个问题的方法。

towardsdatascience.com](https://towardsdatascience.com/two-simple-steps-to-create-colorblind-friendly-data-visualizations-2ed781a167ec)*