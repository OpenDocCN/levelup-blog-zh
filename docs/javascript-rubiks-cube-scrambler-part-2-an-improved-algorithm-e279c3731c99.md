# JavaScript 魔方加密器第 2 部分:改进的算法

> 原文：<https://levelup.gitconnected.com/javascript-rubiks-cube-scrambler-part-2-an-improved-algorithm-e279c3731c99>

![](img/a3ddc366eb38a6587bcd0a65555d039e.png)

# 介绍

这是我前几天写的关于用 JavaScript 打乱魔方的后续文章。如果你还没有看过这篇文章，请务必在这里查看[然后再回到这篇文章。在这篇文章中，我将改进 JavaScript 扰码器。要做到这一点，我们将不得不不幸地重写大部分代码，但基本设计保持不变。我们开始吧！](/using-javascript-to-scramble-a-rubiks-cube-306f52908f18)

# 什么是争夺

你应该已经知道什么是魔方争夺(假设你读了第一篇文章)，但是，这里有一个快速复习以防万一。一次争夺是在已解决的立方体上进行的 20 次移动的序列。这 20 步棋是从以下一组棋中随机挑选出来的:

```
F, R, U, B, L, D, F2, R2, U2, B2, L2, D2, F', R', U', B', L', D'
```

以下是一些加扰示例:

```
B2 R F' L2 R B L2 F' D' L2 F L2 R' U B D' F D' U2 R' 
B' D U2 F' L U' R2 L' D B D' R2 B' F' R2 B2 D2 R B D2 
L' R2 U2 F U' B' D2 B D B' F2 U R2 F2 U' L2 B2 R2 L U'
```

# 问题是

上一篇文章的问题是，我们的编码算法没有考虑到两个相同的移动相邻的可能性。例如，我们可能会得到如下所示的扰码:

```
B2 R F' L2 R B L2 F' L' L2 F L2 R' U B D' F D' U2 R' 
B' D U2 F' L U' R2 R2 D B D' R2 B' F' R2 B2 D2 R B D2 
L' R2 U2 F U' B' D2 B D B' F2 U R2 D D L2 B2 R2 L U'
```

起初，这些加扰可能看起来不像一个问题，但是如果你仔细看，你会看到第一个加扰有一个 L '和一个 L2 直接相邻。最后一次争夺有两个相邻的 D 步。这毫无意义。以最后一次争夺为例。就是两个 D 步相邻的那个。这和说 D2 是一样的。本质上，现在是 19 步，而不是 20 步。这是一个问题，特别是当我们在争夺中有 3 或 4 个这样的移动时

此外，R 后面跟着 R '是没有意义的，因为它们只是相互抵消！我们所做的只是移动立方体两次到达同一个点。当这种情况在一次争夺中多次发生时，我们的争夺可能是 15 步或更少，而不是 20 步！

# 修复

这似乎是一个简单的解决办法，但实际上并不那么容易。很难检查两个相邻的移动是否属于同一类型。我们需要考虑用不同于上次的方式来解决这个问题。让我们再来看看每一次争夺的选项。

```
F, R, U, B, L, D, F2, R2, U2, B2, L2, D2, F', R', U', B', L', D'
```

我们可以看到字母有 6 个不同的移动。有 F，R，U，B，L 和 d。此外，每一个都有 3 个不同的移动。一次顺时针移动(F)、两次移动(F2)和一次逆时针移动(F’)。这些组成了我们的 18 个可能的移动(6 x 3 = 18)。

我们可以用数字来表示混乱。我们将把 F 赋给 0，B 赋给 1，依此类推。然后在最后，我们可以把字母替换回来，并且可以确定没有相同的字母会彼此相邻。

# 用 JavaScript 编写解决方案

我们现在可以利用这些知识来解决这个问题。让我们从给 6 种可能性中的每一种赋予一个数字 0 到 6 开始，并将它们存储在一个数组中。我们称这个数组为 numOptions。

```
var numOptions = [0,1,2,3,4,5]
```

现在，我们可以将这些数字随机添加到另一个名为 scramble 的数组中，直到数组中有 20 个随机字母。记住，一个魔方的争夺是 20 步。

```
var scramble = [] // initialize to empty

    for (var i = 0; i < 20; i++ ) {
        scramble.push(numOptions[getRandomInt(6)])
    }

    // random int code from last tutorial
    function getRandomInt(max) {
        // returns up to max - 1
        return Math.floor(Math.random() * Math.floor(max))
    }
```

现在，我们有一个从 0 到 5 的 20 个随机数的数组！现在，你们中的一些人在想，我们仍然有和上次一样的问题。如果那是你，你绝对是对的。我们仍然有两个 3 相邻或者两个 1 相邻的可能性。但是，由于我们使用的是数字(而不是字母)，所以遍历数组并检查这一点非常容易。我们现在可以编写代码来生成一个由 20 个数字组成的数组，遍历并检查是否有相同的数字相邻，如果是，则再次生成数组。

```
var bad = true

    while (bad) {
        scramble = []
        for (var i = 0; i < length; i++) {
            scramble.push(numOptions[getRandomInt(6)])
        }
        // check if moves directly next to each other are the same letter
        for (var i = 0; i < length - 1; i++) {
            if (scramble[i] == scramble[i + 1]) {
                bad = true
                break
            } else {
                bad = false
            }
        }
    }
```

简单解释一下上面的代码:我们声明一个布尔值 bad，并将其设置为 true。我们已经看过的下一段代码。然后，我们通过循环数组来检查加扰。它将索引 0 处的值与索引 1 处的值进行比较。如果它们不相等，它继续前进，并以同样的方式检查数组的其余部分。如果在任何一点上两个数相等，它就中断，重新产生加扰，并再次检查。

# 添加字母

最后一步是用字母代替数字。为此，我们将声明一个包含所有可能选项的数组，然后编写一个嵌套在 for 循环中的 switch 语句，用正确的字母替换每个数字。

《出埃及记》如果数字是 3，我们将放置 a、B、B2 或 B’(当然是随机的)。

```
var options = ["F", "F2", "F'", "R", "R2", "R'", "U", "U2", "U'", "B", "B2", "B'", "L", "L2", "L'", "D", "D2", "D'"]
var move

for (var i = 0; i < 20; i++) {
        switch (scramble[i]) {
            case 0:
                move = options[getRandomInt(3)] // 0,1,2
                scrambleMoves.push(move)
                break
            case 1:
                move = options[getRandomIntBetween(3, 6)] // 3,4,5
                scrambleMoves.push(move)
                break
            case 2:
                move = options[getRandomIntBetween(6, 9)] // 6,7,8
                scrambleMoves.push(move)
                break
            case 3:
                move = options[getRandomIntBetween(9, 12)] // 9,10,11
                scrambleMoves.push(move)
                break
            case 4:
                move = options[getRandomIntBetween(12, 15)] // 12,13,14
                scrambleMoves.push(move)
                break
            case 5:
                move = options[getRandomIntBetween(15, 18)] // 15,16,17
                scrambleMoves.push(move)
                break
        }
    }

function getRandomIntBetween(min, max) { // return a number from min to max - 1\. Ex. 3, 9 returns 3 - 8
    return Math.floor(Math.random() * (max - min) + min)
}
```

你会注意到我们有另一个函数，它可以得到两个数字之间的一个随机数，叫做 getRandomIntBetween。我添加这个是因为我们需要能够得到 2 个数字之间的一个随机数，而我们当前的 randNum 函数只允许我们得到一个从 0 到指定数字的随机数。

# 结论

看了我是怎么解决这个问题的，能不能更好的解决？我很想看看是否有更好的解决方案！如果你想试试，请在评论中告诉我。你可以在我的 [**GitHub**](https://github.com/bjcarlson42/blog-post-code/blob/master/Rubik's%20Cube%20JavaScript%20Scrambler/part_two.js) 上获得本教程的代码。