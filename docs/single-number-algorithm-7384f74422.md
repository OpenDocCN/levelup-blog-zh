# 单一数字算法

> 原文：<https://levelup.gitconnected.com/single-number-algorithm-7384f74422>

## 使用 XOR 按位运算符

![](img/e7a86e6325151f681c1616be175f13a4.png)

寻找数组中的单个数字是算法中的常见任务。下面我们来看看怎么做。

*给定一个* ***非空的*** *整数数组，除一个元素外，每个元素都出现两次。找到那个单身的。*

示例:

```
**Input:** [2,2,1]
**Output:** 1
```

解决方案是:

```
*const singleNumber = function(nums) {
return nums.reduce((accumulator, value)=>accumulator^value)
}*
```

## 为什么和如何？

让我们检查一下建议的解决方案。

我们使用 reduce 函数并返回累加器，与数组中的每个元素进行比较。

对比？……没错，这个小符号 ***^*** 被称为“异或”位运算符。

那么，我们来谈谈**【按位运算符】**

与逻辑运算符比较基元值的方式相同，按位运算符也用于比较，但方式不同。

根据文件:

***按位运算符*** *将其操作数视为 32 位序列(零和一)，而不是十进制、十六进制或八进制* `[*numbers*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)*.*`

这意味着每个操作数都被转换为 32 位整数，表示为一系列位。你记得二进制吗？

让我给你看一个例子:

```
decimal system: 2 
32 bits system : 00000000 00000000 00000000 00000010
```

现在，当我们使用按位运算符时，每个操作数都与另一个操作数的相应位成对出现。因此，在*位*和*位*之间进行比较。

除了逻辑运算符，最常见的按位运算符还有 AND (&)或(|) XOR (^). ***没错就一个&或者|或者^*** 当然每个按位运算符都有自己的真值表。

[按位 AND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_AND) `a & b`在每个位位置返回一个`1`，两个操作数的对应位都是`1` s

```
 ╔═══╦═══╦═════════╗
                       ║ a ║ b ║ a **AND** b ║
                       ╠═══╬═══╬═════════╣
                       ║ 0 ║ 0 ║    0    ║
                       ║ 1 ║ 0 ║    0    ║
                       ║ 0 ║ 1 ║    0    ║
                       ║ 1 ║ 1 ║    1    ║
                       ╚═══╩═══╩═════════╝
```

示例:

```
**>> 1 & 2**
*1* -> 0001
*2* -> 0010
R -> 0000Decimal: 0
```

[按位 OR](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_OR) `a | b`在每个位位置返回一个`1`，其中一个或两个操作数的对应位为`1` s

```
 ╔═══╦═══╦═════════╗
                       ║ a ║ b ║ a **OR** b  ║
                       ╠═══╬═══╬═════════╣
                       ║ 0 ║ 0 ║    0    ║
                       ║ 1 ║ 0 ║    1    ║
                       ║ 0 ║ 1 ║    1    ║
                       ║ 1 ║ 1 ║    1    ║
                       ╚═══╩═══╩═════════╝
```

示例:

```
**>> 1 | 2**1 -> 0001
2 -> 0010
R -> 0011
Decimal -> 3
```

[按位异或](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_XOR) `a ^ b`在每个位位置返回一个`1`，其中任一操作数而非两个操作数的对应位为`1`

```
 ╔═══╦═══╦═════════╗
                       ║ a ║ b ║ a **XOR** b ║
                       ╠═══╬═══╬═════════╣
                       ║ 0 ║ 0 ║    0    ║
                       ║ 1 ║ 0 ║    1    ║
                       ║ 0 ║ 1 ║    1    ║
                       ║ 1 ║ 1 ║    0    ║
                       ╚═══╩═══╩═════════╝
```

例子

```
**>> 1 ^ 2**
1 -> 0001
2 -> 0010
R -> 0011
Decimal -> 3
```

如您所见，按位运算符在二进制表示中执行运算，但它们返回标准的 JavaScript 数值。

现在，让我们回到我们的问题。使用 XOR 运算符，我们可以排除那些相等的值。

```
**Input:** [2,2,1]First time: 2 XOR 2 2 -> 0010
2 -> 0010
R -> 0000Now our accumulator has 0 as value.Second time: 0 XOR 10 -> 0000
1 -> 0001
R -> 0001 Now our accumulator has 1 and return that value
```

好的，这个很简单，让我们试试另一个例子。

```
**Input:** [4,1,2,1,2]First time 4 XOR 14 -> 0100
1 -> 0001
R -> 0101   ----->  Now our accumulator has 5 as value.Second time: 5 XOR 25 -> 0101
2 -> 0010
R -> 0111  ----->  Now our accumulator has 7 as value.Third time: 7 XOR 1 7 -> 0111
1 -> 0001
R -> 0110  ----->  Now our accumulator has 6 as value.Fourth time: 6 XOR 26 -> 0110
2 -> 0010
R -> 0100  ----->  Now our accumulator has 4 as value. Return 4
```

就是这样。现在，您可以使用按位运算符来执行其他逻辑比较。

继续编码！