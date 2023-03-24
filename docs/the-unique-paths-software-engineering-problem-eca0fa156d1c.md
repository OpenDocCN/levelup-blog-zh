# “唯一路径”软件工程问题

> 原文：<https://levelup.gitconnected.com/the-unique-paths-software-engineering-problem-eca0fa156d1c>

## 一个简单的，针对流行算法问题的优化解决方案。

![](img/101b13ea7084ad3c3c1f619726b15a7e.png)

[塔拉·斯卡希尔](https://unsplash.com/@yellowumbrellamedia?utm_source=medium&utm_medium=referral)在[未拉伸](https://unsplash.com?utm_source=medium&utm_medium=referral)时拍摄的照片

## 问题

唯一路径可在 leetcode [上找到，此处为](https://leetcode.com/problems/unique-paths/)。

`m x n`网格上有一个机器人。机器人最初位于左上角**(即`grid[0][0]`)。机器人试图移动到**右下角**(即`grid[m - 1][n - 1]`)。机器人在任何时间点只能向下或向右移动。**

给定两个整数`m`和`n`，返回*机器人到达右下角*可能的唯一路径数。

![](img/932da2fba0ab4ec7a830ee687da6de93.png)

网格上的机器人

对于这个例子，我们有一个 3 x 7 的网格。`m = 3, n = 7`

机器人只有向右和向下移动才能到达“终点”,这有 28 条独特的路径。

> 如果机器人也能向上或向左移动，除了一种情况之外，每种情况都会有无数的解决方案。你知道为什么吗？你知道边缘病例吗？

## 解决方案

当我第一次处理这个问题时，我的第一个想法是构造矩阵，然后用递归计算所有可能的路径。我很快意识到，从空间或时间复杂性的角度来看，这不会有很好的表现。

接下来，我想，“一定有某种方法可以用数学来解决这个问题。”

为了让机器人到达终点，它需要向下移动总共`m-1`个空间，向右移动`n-1`个空间。因此，如果`m = 3`和`n = 7`，这是可能的路径之一:

```
DDRRRRRR
```

其中`R`表示向右移动，`D`表示向下移动。所以实际上，我们需要做的就是找到这个“字符串”的所有排列。

带有`x`字母的单词的唯一排列数是`x!`，或 x 阶乘。这是假设这个词没有任何重复的字母。

对于单词中的每个字母，`x!`数字除以每个字母的频率阶乘的乘积。因为`1! = 1`，所以对于一个有严格唯一字母的单词来说，结果是`x!`阶乘。但是对于“和平”这个词，它会出现在`5! / (1! * 2! * 1! * 1!)`或`5! / 2!`中。

对于这个独特的路径问题，单词的长度是`m-1 + n-1`。而且我们知道有两个字母，`D`和`R`，它们的频率分别是`m-1`和`n-1`。所以最终的解决方案是:

```
m--;
n--;
(m + n)! / (m! * n!);
```

下面是在 TypeScript 中的样子:

```
function uniquePaths(m: number, n: number): number {
    m--;
    n--;
    let result = factorial(m + n) / (factorial(m) * factorial(n));
    return result;
}function factorial(num: number): number {
    let product = 1;

    while (num > 0) {
        product *= num;
        num--;
    }

    return product;
}
```

> 注意:如果你试图解决这个问题的语言有自己内置的阶乘函数，我推荐你使用它。我肯定它比我这里的性能更好。
> 
> 事实上，我猜想高级编程级别中的内置阶乘函数可能是 O(1)，保存结果并进行查找。在达到 64 位最大整数值之前，您可能连 20 阶乘都做不到。

我写这篇文章是因为我想提高自己对这个问题的认识。我希望我也帮助提高了你对它的理解。

感谢您的阅读！