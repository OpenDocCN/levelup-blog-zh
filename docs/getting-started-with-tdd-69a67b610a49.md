# TDD 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-tdd-69a67b610a49>

![](img/447ef864bbf895313f73448409bfa6cb.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

几个月来，我一直对测试驱动开发(TDD)感兴趣。我读了几篇文章，但不能真正理解其中的概念。

本月早些时候，我看了一段视频，视频中 T4 的罗伯特·马丁正在做 TDD 的现场演示。我对 TDD 如何激怒 200 多名观众感到震惊。；)

但最重要的是，我也对它的简单性和快速获得结果的速度印象深刻。

初学者总是一需要编码就奔向键盘，而不是花时间去思考问题本身，边缘情况或复杂性…随着你作为一名开发人员的成长，你会学会与这种情况作斗争。

但是 TDD 似乎并不关心这个。使用 TDD，你有一个问题，你从写一个测试开始。然后你写代码…然后你写测试…如此周而复始，直到你解决问题。

以下是基本的 TTD 规则:

*   只编写足够测试失败的测试代码
*   只有在测试失败的情况下才编写产品代码
*   只编写足够通过测试的生产代码

最近我不得不写一个简单的 Python 函数，并决定尝试使用 TDD。

问题:给定两个字符(一个移动的和一个固定的)和一个数字，得到所有可能的字符串。

```
# Given a moving char, a fixed char, and a number, generate all possible strings
#With: A, n, 4 We will have Annn, nAnn, nnAn,nnnA
#With: 0, 1, 5 We will have 01111, 10111, 11011, 11101, 11110
```

我们开始吧！

**第一步**。写一个测试，期待一个结果。

```
AllPatterns('A', 'n', 4) #Should return ["Annn"]
```

然后执行，您会得到一个不错的错误:**名称‘all patterns’没有定义**

**第二步**。编写最小*代码*以通过测试

```
def AllPatterns(movingChar, fixedChar, length):
    return ["Annn"]
```

**最小代码** …

您刚刚修复了刚刚编写的测试的问题。毫无疑问。

另一个重要步骤:**重构**。如何重构代码以使其可读性、可维护性和整洁性？

在第一步中，代码非常干净，所以让我们进入第二个测试。

**步骤 1a** 。写一个测试，期待一个结果。

```
AllPatterns('A', 'n', 4) #Should return ["Annn","nAnn"]
```

第 2a 步。编写最小*代码*以通过测试。

```
def AllPatterns(movingChar, fixedChar, length):
    return ["Annn","nAnn"]
```

这时，你开始听到有人对你大喊:“给这该死的东西编码！”

实际上，神奇的事情发生了:**重构部分**。显然，我不能像这样离开代码。首先，因为我将不得不硬编码所有的解决方案，其次，因为我没有更接近找到算法，因此，没有更接近找到解决方案。

**步骤 2a。重构 1** 。我需要返回一个数组。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    result.append("Annn")
    result.append("nAnn")
    return result
```

**步骤 2a。重构 2** 。让我们打破移动的角色和固定的角色。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    result.append("A" + "nnn")
    result.append("n" + "A" + "nn")
    return result
```

我的打印仍然返回正确的值，并且我对算法有了更多的了解。我们继续。

**步骤 1b** 。写一个测试，期待一个结果。

```
AllPatterns('A', 'n', 4) #Should return ["Annn","nAnn","nnAn"]
```

**步骤 2b** 。编写最小*代码*以通过测试。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    result.append("A" + "nnn")
    result.append("n" + "A" + "nn")
    result.append("nn" + "A" + "n")
    return result
```

我可以开始重构，但是我还有一个案例要测试，所以让我们开始吧。

**步骤 1c** 。写一个测试，期待一个结果。

```
AllPatterns('A', 'n', 4) #Should return ["Annn","nAnn","nnAn","nnnA"]
```

**步骤 2c** 。编写最小*代码*以通过测试。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    result.append("A" + "nnn")
    result.append("n" + "A" + "nn")
    result.append("nn" + "A" + "n")
    result.append("nnn" + "A" + "")
    return result
```

现在我得到了我的示例代码，所以我们重构。这里有相当多的事情要做。

**步骤 2c。重构 1** 。对齐代码以查看模式。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    result.append(""    + "A" + "nnn")
    result.append("n"   + "A" + "nn" )
    result.append("nn"  + "A" + "n"  )
    result.append("nnn" + "A" + ""   )
    return result
```

**步骤 2c。重构 2** 。替换固定部分的字符重复。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    result.append("".join("n" for i in range(0)) + "A" + "".join("n" for i in range(3)) )
    result.append("".join("n" for i in range(1)) + "A" + "".join("n" for i in range(2)) )
    result.append("".join("n" for i in range(2)) + "A" + "".join("n" for i in range(1)) )
    result.append("".join("n" for i in range(3)) + "A" + "".join("n" for i in range(0)) )
    return result
```

**步骤 2c。重构 3** 。看到循环，编码。；)

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    for i in range(4):
        result.append("".join("n" for i in range(i)) + "A" + "".join("n" for i in range(4-1-i)) )
    return result
```

**步骤 2c。重构 4** 。把变量换成参数就行了。

```
def AllPatterns(movingChar, fixedChar, length):
    result = []
    for i in range(length):
        result.append("".join(fixedChar for i in range(i)) + movingChar + "".join(fixedChar for i in range(length-1-i)) )
    return result
```

*等着瞧*！只需几个步骤，您就可以得到一个漂亮的小函数。；)

我知道这是一个基本功能，大多数开发人员第一次就能做好。但是使用 TDD，就像使用任何其他技能一样，您需要从小处着手，然后朝着更大的目标前进。

从那以后，我曾多次尝试 TDD，并发现每次都很有收获。我仍然不能在完整的项目或非常相关的代码上做到这一点，但我会尝试在简单的功能上做到这一点。就像我说的，你需要从小处着手，一步一步来。

Github: [链接](https://github.com/satan87/Medium/blob/master/TDD%20-%20Moving%20Character.ipynb)

[](https://medium.com/swlh/tdd-and-randomness-b0ad8a5f5ae6) [## TDD 和随机性

### 如何 TDD 随机行为？

medium.com](https://medium.com/swlh/tdd-and-randomness-b0ad8a5f5ae6)