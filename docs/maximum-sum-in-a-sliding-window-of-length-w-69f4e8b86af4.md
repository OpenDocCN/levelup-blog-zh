# 长度为 w 的滑动窗口中的最大和

> 原文：<https://levelup.gitconnected.com/maximum-sum-in-a-sliding-window-of-length-w-69f4e8b86af4>

![](img/0be5c3727a6377ee5188c98071bf114b.png)

Pierre chtel-Innocenti 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

先说一个滑动窗口问题，以及如何解决。

当您有一组数据(如字符串或数组)并且正在寻找数据的连续子集时，滑动窗口方法非常有用。我们创建一个窗口(可以是子数组或子串),并根据条件移动窗口。

考虑下面这个问题:*写一个函数，接受一个整数数组和一个名为* ***w*** *的数字。该函数应该计算数组中连续的* ***w*** *个元素的最大和。*

## **暴力破解— O(nw)**

强力方法是使用嵌套循环。我们已经可以断定这不会是高效的，但仍然值得讨论。

解决方案如下:

首先，我们检查数组确实比`w`长。否则我们返回`null`,因为数组中没有足够的整数相加。

接下来，我们将一个名为`maxSum`的变量设置为`-Infinity`，我们将使用它来比较我们的总和，以便在最后返回最大值。我们将`maxSum`初始化为一个小数字，这样我们迭代时计算的任何总和都将大于它。我们使用`-Infinity`代替`0`，以防数组被负数填充。

我们从数组的第一个元素开始循环，直到可以开始求和的最后一个元素。我们不想去数组中的最后一个元素，而是最后一个元素，这个元素后面还有`w-1`个元素可以被求和。

对于每一次迭代，我们运行一个从`0`到`w`的循环(排他的)来计算我们的和。

我们检查我们的总和是否大于当前的`maxSum`，如果是，将其存储在`max`变量中。

最后，在退出外部循环后，我们返回最大和。

下面提供了示例代码:

```
function maxSubarraySum(*arr*, *w*){ if (num > arr.length){
    return null;
  } let maxSum = -Infinity
  for (let i = 0; i < arr.length - w + 1; i++){
    let temp = 0;
    for (let j = 0; j < w; j++){
      temp += arr[i+j];
    } if (temp > maxSum){
      maxSum = temp;
    }
  }
  return maxSum;
}
```

正如我们前面提到的，这种解决方案效率很低，因为它使用了嵌套循环。这个解的时间复杂度是 O(nw)。

## **滑动窗口解— O(n)**

我们可以使用滑动窗口提出一个更有效的解决方案。

解决方案如下:

像上次一样，我们首先检查数组是否比`w`长。

然后我们创建两个变量，`maxSum`和`tempSum`。

我们遍历数组中的第一个`w`元素，将它们相加，并将它们存储在`maxSum`和`tempSum`中。

接下来我们从`w`开始循环`i`直到数组结束，*向右滑动我们的窗口*。这意味着，不是使用内部循环来计算从当前位置开始的`w`元素的和，而是使用我们已经计算的和，并更新它以说明我们向右移动了一个元素。这是通过从前一个总和中减去最左边的元素(`arr[i — w]`)并加上下一个元素(`arr[i]`)来完成的。然后，我们将新总和(`tempSum`)与当前总和`maxSum`进行比较，并在必要时重新分配`maxSum`。

最后，在退出循环后，我们返回`maxSum`。

下面提供了示例代码:

```
function maxSubarraySum(*arr*, *w*){ if (w > arr.length){
    return null;
  } let maxSum = 0;
  let tempSum = 0; for (let i = 0; i < w; i++){
    maxSum += arr[i]
  } tempSum = maxSum; for (let i = w; i < arr.length; i++){
    tempSum = tempSum - arr[i - w] + arr[i];
    if (tempSum > maxSum){
      maxSum = tempSum;
    }
  }
  return maxSum;
}
```

正如你所看到的，这是一个更有效的解决方案，因为我们完全消除了内部循环。这个解的时间复杂度是 O(n)。

参考资料:

[](https://www.udemy.com/course/js-algorithms-and-data-structures-masterclass/) [## JavaScript (JS)算法和数据结构大师班

### 嗨！我是柯尔特。我是一名热爱教学的开发人员。过去几年我一直在教人们…

www.udemy.com](https://www.udemy.com/course/js-algorithms-and-data-structures-masterclass/) [](https://medium.com/outco/how-to-solve-sliding-window-problems-28d67601a66) [## 如何解决滑动窗口问题

### 滑动窗口问题是在软件工程面试中经常被问到的一种问题

medium.com](https://medium.com/outco/how-to-solve-sliding-window-problems-28d67601a66) 

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)