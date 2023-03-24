# 大 O 符号:计算时间复杂度

> 原文：<https://levelup.gitconnected.com/big-o-notation-calculating-time-complexity-d4fd5750f0bb>

![](img/3868301579f360b7206b6a4f6447c74c.png)

准备技术面试有时会很困难、令人沮丧和困惑。随着我继续找工作，我想我应该花些时间来分享我到目前为止学到的关于计算时间复杂度和大 O 符号的知识。当 Flatiron 第一次介绍这个主题时，对我来说并不容易理解，但是一旦你进一步深入研究，big O 背后的想法实际上是相当简单的。这里有一个它是如何工作的纲要，以及你需要知道什么来开始钉那些技术面试问题！

首先，当人们说“大 O”时，他们实际上指的是大θ(“大θ”)。这一概念旨在评估函数在接近特定值/无穷大时的极限行为。本质上，当使用大 O 或“上渐近界”时，您看到的是最坏的情况要计算时间复杂度，您只需查看具有最大指数的项，忽略系数/较小的项，您还可以计算函数中嵌套循环的数量，以帮助确定 big O。在这个例子中，我试图在一个由 60 个数字组成的混排数组中找到一个特定的目标数字，比如说 23。

```
array = [*1..60].shuffledef linear_search(array, target)
  counter = 0

  # iterate through the given array starting 
  # at index 0 and continue until the end

  while counter < array.length 
    if array[counter] == target       # exit the loop if the target element is found 
      return "Took: #{counter} iterations to find the target." 
    else 
      counter += 1
    end 
  end 

  return "#{target} could not be found in the array." 
endlinear_search(array, 23)
```

当试图找到目标时，运行我的方法`linear_search` 5 次给出了这个输出— 23。

```
=> "Took: 12 iterations to find the target."
=> "Took: 20 iterations to find the target."
=> "Took: 37 iterations to find the target."
=> "Took: 55 iterations to find the target."
=> "Took: 30 iterations to find the target."
```

本质上，这个输出告诉我们的是，给定我们的函数，如果它被混洗到数组的索引 0，找到目标所需的最小迭代次数将是 1。更重要的是，如果一直拖到最后，最坏的情况是 60 次迭代。如果我们的数组有 150 个数字/项，那么最坏的情况是 150 次迭代。线性搜索的大 O 符号是 O(n)，其中 n 等于集合中元素的数量。

以下是一些需要注意的其他常见大 O 符号:

*   o(1)-总是花费相同的时间，速度不变。
*   O(log n) —每次循环迭代后，工作量除以 2。
*   o(n)-计算时间取决于输入大小。
*   O(n log n) —嵌套循环，其中内部循环在`log n`时间内运行。
*   o(n^2)——完成工作的时间随着关系的发展而增长。
*   o(n^3)——与`n^2`相似，但在`input size ^ 3`关系中成长。
*   o(2^n)——计算工作在`2 ^ input size`关系中增长。

简而言之，这就是大 O 符号。基本上，您只是试图确定绝对最坏的情况，或者更确切地说，给定函数/算法的整体性能取决于它必须处理的数据量。这个主题乍一看似乎很有挑战性，但是计算时间复杂度并不一定如此。我的建议是尽可能多地查看代码示例，这些代码说明了上面列出的不同符号，以便真正区分它们的含义。如果你想通过技术面试，获得下一份工作，对大 O 符号有很强的理解是至关重要的！

来自 Level Up Coding 的提示:订阅我们的 [YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或加入 [Skilled.dev 获取编码面试教程和职业建议](https://skilled.dev)。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)