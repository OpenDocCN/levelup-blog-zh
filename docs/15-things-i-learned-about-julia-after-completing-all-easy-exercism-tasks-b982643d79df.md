# 完成所有简单的锻炼任务后，我对 Julia 了解了 15 件事

> 原文：<https://levelup.gitconnected.com/15-things-i-learned-about-julia-after-completing-all-easy-exercism-tasks-b982643d79df>

## Julia 语言的一些很酷的特性

我用[exercisem](https://exercism.org)来了解更多的 Julia，并在途中发现了一些有用的技巧和提示。继续读下去，看看有多少你已经知道了。

![](img/ac25305d15db20a5b2fa88af16c7bef9.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/learning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

早在 2014 年，我就开始和朱莉娅交往了，从那以后就断断续续了。我一直想更多地使用它，但是 Python——以及更早的 R——是工作中更受欢迎的选择。我现在的任务是掌握 Julia，并更多地了解这种语言在过去几年中是如何发展的。

作为这次学习之旅的一部分，我遇到了[Exercism.org](https://exercism.org)，在那里你的任务是**解决掌握一门语言的一系列挑战**。有针对 Julia、Python、Go、C++、bash 的曲目，还有更多。如果你正在考虑学习或掌握一门新的语言，我强烈推荐这个网站来获得大量的好的实践。所有的问题都有一个测试套件，这样你就可以在本地运行你的代码——或者甚至在他们的 web IDE 中运行——看看你的功能是否按要求工作。一旦你完成了，你可以提交你的代码——使用命令行界面——然后你可以发布解决方案并向导师寻求支持。对我来说，最好的部分是**你可以看看别人的解决方案**并评论/评论他们。从这些投稿中我学到了很多！

> 通过看别人的代码，你可以学到惊人的东西。

所以，话不多说，下面是我在应对这些挑战时学到的或者重新学到的 16 件事。

# 管道很优雅

![](img/582580ac87fc7f3cca5a3e0871264749.png)

Osman Yunus Bekcan 在 [Unsplash](https://unsplash.com/s/photos/pipe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Julia 中的管道看起来是这样的:`|>`，它们通过将第一步的结果作为输入传递给下一个函数，允许你将多个函数调用链接在一起。

例如:

```
**julia>** "Hello World!" |> reverse |> lowercase |> x -> replace(x, "!" => "?")"?dlrow olleh"
```

如您所见，您可以将多个函数链接在一起，使其可读性更好，就像下面这样:

```
**julia>** replace(lowercase(reverse("Hello World!")), "!" => "?")"?dlrow olleh"
```

更好的是，你还可以用管道广播。我发现的最优雅的解决方案之一是，创建首字母缩写词:

你能猜出它是做什么的吗？在这里运行代码[来找出答案。](https://tio.run/##NU/bDsIwCH22X0GaPazxkvgB7keMMbWyDa20oV3UxH@f3aY8wOFwDoHb4MnuX@PYDuwyBQbrJPD7UcdebEKjoESKnvKP2YDoI5y3p7U2u08DLUnKMKEhRhRXJFCaWyBWyNeROA45wQFqHYNke/EIjPkZ5A6d2NiTS3oD2iFnsR6IM3pPHbJDsFN5a6NUGwTmVSUvIKlVlKL2XOtqGR0aqOr/AzNlTDFPZ3wB)

如果您需要向一个函数传递多个参数，您可以使用来自 [Pipe.jl](https://github.com/oxinabox/Pipe.jl) 的`@pipe`宏，并用`_`传递您的结果。

# 测试很容易

因为练习都有一个测试文件，所以我可以看到很多测试😃。这让我意识到在 Julia 中进行单元测试是多么容易。只需使用`@test`或其替代品之一，一切开箱即用。用`@testset`给你的测试集命名，这样你的输出可以显示你的代码在哪里失败了。

这将为您提供以下输出:

```
**Test Summary: | Pass  Total** Checking sqrt |    3      3Failing tests: Test Failed at ~\Documents\exercism\article.jl:11
  Expression: sqrt(10) < sqrt(5)
   Evaluated: 3.1622776601683795 < 2.23606797749979Test Summary: | Fail  Total
Failing tests |    1      1
```

我认为这非常简洁，并且比我在 Python 中遇到的任何东西都容易使用。

我在 exercism 中遇到的一个技巧是使用`@isdefined`宏有选择地运行一个更难的子问题的测试:

上面的代码允许您在主脚本中声明一个名为`TEST_GRAPHEMES`的变量，只有当上面的代码存在时，测试才会执行。

# Julia 有一个在项目环境中执行的标志

![](img/d291279fa92cdf304c192cc82a065739.png)

[梁杰森](https://unsplash.com/@ninjason?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/flag?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果您在终端中运行`julia filename.jl`,这非常简单，Julia 将在该目录中查找`Project.toml`,并根据该环境执行您的脚本。如果它没有找到一个环境，那么它将在您的机器的主环境或默认环境中运行它。

您可以使用`--project`标志手动指定。

# Primes.jl 真的很快

可以想象，如果没有数论，对 Julia 来说，任何编码挑战都是不完整的。所以要准备找一些质数、约数等。尽管我很喜欢实现自己的函数，但我不得不尝试已经可用的包，看看它们的性能如何。

jl 是一个用于**寻找素数和测试一般素数**的包。好家伙，它很快…所以下次你需要找到一个数的所有质因数时，只需调用:`Primes.factor(Vector, n)`，它会给你一个所有因数的数组。

# `findfirst, findall`和`findnext`

![](img/8fbcba3323597f56f59b5420daf06b22.png)

[粘土堤](https://unsplash.com/@claybanks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/find?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

这三个函数让你找到一个数组的索引，它们在这里`true`。所以如果你的数组是布尔型的，这就给出了你的`true`值的位置:

```
**julia>** x = [true, true, false, true]
4-element Vector{Bool}:
1
1
0
1**julia>** findfirst(x)
1**julia>** findall(x)
3-element Vector{Int64}:
1
2
4**julia>** findnext(x, 3)
4
```

如果你有一个非布尔型的数组，你可以传入一个函数作为第一个参数，它们将返回数组的索引**，其中这个函数的值为真**。

```
**julia>** x = [3, 4, 5, 6];**julia>** findfirst(i -> i > π, x)
2
```

# 正则表达式很好

在 Julia 中，正则表达式不仅非常容易编写，而且功能也非常强大。你可以做简单的事情，但你也可以全力以赴，做**消极的事情或者其他更令人兴奋的事情。**

例如，这个人找到所有紧跟在空格后面的字母:`r"(?<=\s)(\w)"`。

# 我真的很喜欢 Unicode 运算符

不需要写`div(x, n)`来看 n 在 x 中有多少次没有余数。不可以，你可以输入`\div`，按 tab 得到这个:`x ÷ n`。

也可以做`\pi`，得到π作为内置。或者，您可以键入`\circ`并像这样编写函数:

```
julia> f(x) = 2x; g(x) = x^2;julia> h = g ∘ f
g ∘ fjulia> h(5)
100
```

但是在所有 Unicode 函数/操作符中，我最喜欢的是`\in`:

```
**julia>** 5 ∈ 1:10
true
```

这让我内心的数学家发痒…

# 单行函数定义

来自数学背景的一行程序函数真的很让人开心。能够写`f(x) = 2x + 5`是一件乐事。给你一个更大的例子，其中一个任务是找出一个数字是否是一个[阿姆斯特朗数](https://en.wikipedia.org/wiki/Narcissistic_numbe)——一个数字是它自己的数字的和，每个数字的幂。以下是我的完整解决方案:

# 这通常有一个函数

![](img/e9f43b04f6e1f453eb5807c15715ecda.png)

茱莉亚·基地有一个大工具箱——照片由 [Tekton](https://unsplash.com/@tekton_tools?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/toolbox?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Julia 有很多内置函数，所以不要试图去写列表理解或愚蠢的 lambda 函数。检查它是否已经有一个函数。以下是我最喜欢的一些:

*   `digits` —以向量的形式给出数字的位数
*   `ndigits` —长度(位数)基本上
*   `isone` —检查数字是否为一
*   `isempty` —检查数组是否为空，在检查空字符串时也很有用

# 三元运算符会让人上瘾

如果您发现自己正在编写大量微小的 if-else 块，请允许我向您介绍三元运算符:

我们对`next_f`功能感兴趣。它根据条件`isodd(n)`返回一个新的函数，如果为真，我们得到`?`之后的第一个版本，否则，我们得到`:`之后的版本。

我知道他们在技术上是不同的，但短路操作人员也很整洁。只有当双&符号前的表达式为真时，才可以使用`&&`来计算一段代码。这允许您在代码中加入如下内容:

```
x < 2 && continue
x > 10 && break
```

# Get 也适用于数组

我已经知道了`get`功能。它通过关键字返回字典中的一个元素，如果关键字不在字典中，则返回默认值。

```
julia> letters = Dict((l, i) for (i, l) in enumerate('a':'z'))
Dict{Char, Int64} with 26 entries:
  'n' => 14
  'f' => 6
  'w' => 23
  'd' => 4
  'e' => 5
  'o' => 15
  'h' => 8
  'j' => 10
  'i' => 9
  'k' => 11
  'r' => 18
  's' => 19
  't' => 20
  'q' => 17
  'y' => 25
  'a' => 1
  'c' => 3
  'p' => 16
  'm' => 13
  'z' => 26
  'g' => 7
  'v' => 22
  'l' => 12
  'u' => 21
  'x' => 24
  'b' => 2julia> get(letters, '!', 0)
0
```

我不知道的是，你也可以对数组使用同样的函数。因此，您不必担心要求超出范围的索引。

```
julia> my_vector = ['a', 'b', 'c'];julia> get(my_vector, 4, '!')
'!': ASCII/Unicode U+0021 (category Po: Punctuation, other)
```

我用这个技巧在这里产生了[帕斯卡三角形](https://github.com/niczky12/exercism/blob/1c6905591378e64314ee27a964812af40ae66e7a/julia/pascals-triangle/pascals-triangle.jl#L18)。

# 您可以通过计算语句来创建许多宏

作为 rot-cipher 挑战的一部分，我们的任务是为每 26 个可能的旋转创建**字符串宏。**让`R1"str"`将引号中的字符串旋转 1 个字母，`R2"str"`旋转 2 个字母，依此类推。多亏了 eval 宏，这对于 Julia 来说非常容易:

# 大多数聚合函数都可以将函数作为第一个参数

Julia 是函数式语言，因此**函数无处不在**。我们已经在`findall`和`findfirst`中看到了这一点，但是还有许多函数将函数作为输入。通过这种方式，聚合函数变得特别强大。

想对一个数组的平方求和？

```
sum(i → i^2, [1,2,3])
```

想要求和它们的平方根吗？

```
sum(sqrt, [1,2,3])
```

我发现了一个很棒的技巧，如果你可能有一个空数组，你可以**使用 MapReduce 来避免对空数组求和**，你需要做的就是指定一个初始状态:

`mapreduce(i -> i^2, +, [1,2,3], init=0)`

除了对`[]`也有效之外，这给了你和上面 sum 一样的结果！

# 您可以在一行中执行双 for 循环

![](img/ad0e4b1a8bc34a2c026a7e68fee83d78.png)

2 比 1 好，对吗？—[约根·哈兰](https://unsplash.com/@jhaland?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/double?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我就让这个自己说吧:

```
julia> for i in 1:4, k in i:4
           [@show](http://twitter.com/show) i, k
       end
(i, k) = (1, 1)
(i, k) = (1, 2)
(i, k) = (1, 3)
(i, k) = (1, 4)
(i, k) = (2, 2)
(i, k) = (2, 3)
(i, k) = (2, 4)
(i, k) = (3, 3)
(i, k) = (3, 4)
(i, k) = (4, 4)
```

# 数字非常有用

由于许多问题涉及到对二进制数字进行编码或解码，我发现自己越来越多地使用 T4。我已经知道这是存在的，它把一个数字分解成几个数字:

```
julia> digits(1729)
4-element Vector{Int64}:
 9
 2
 7
 1
```

我不知道的是，这个函数可以为您提供填充，以保证某个长度的向量在较大数字的位置填充 0:

```
julia> digits(1729, pad=5)
5-element Vector{Int64}:
 9
 2
 7
 1
 0
```

为什么这会有用？哎呀，我不知道，也许当你需要把一个数分解成 10 的幂时？

```
julia> digits(1729, pad=5) .* (10 .^ collect(0:4))
5-element Vector{Int64}:
    9
   20
  700
 1000
    0
```

作为奖励，您还可以指定您想要使用的基础。我给你 1729 的二进制表示:

```
julia> digits(1729, base=2, pad=5)
11-element Vector{Int64}:
 1
 0
 0
 0
 0
 0
 1
 1
 0
 1
```

# 运动！

![](img/85b35268055047e81bcee6a30003c55d.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com/s/photos/treadmill?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

既然你看到了我的经历，你应该去锻炼一下自己。解决挑战和阅读他人的解决方案是学习和练习语言的好方法，因此我强烈推荐 Exercism.io 及其友好的 Julia 社区。

> 你可以在 GitHub 上找到上面的所有代码，但是我建议你先自己解决它们。

[](https://github.com/niczky12/exercism) [## GitHub-niczky 12/exercisem:exercisem . io 解决方案

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/niczky12/exercism) 

> 我写朱莉娅和其他很酷的东西。如果你喜欢这样的文章，请考虑关注我。要完全访问所有媒体文章——包括我的——可以考虑在这里订阅[](https://niczky12.medium.com/membership)**。**

# *分级编码*

*感谢您成为我们社区的一员！在你离开之前:*

*   *👏为故事鼓掌，跟着作者走👉*
*   *📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容*
*   *🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)*

*🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)*