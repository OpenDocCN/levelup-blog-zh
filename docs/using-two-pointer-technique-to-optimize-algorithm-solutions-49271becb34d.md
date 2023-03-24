# 使用双指针技术优化算法解

> 原文：<https://levelup.gitconnected.com/using-two-pointer-technique-to-optimize-algorithm-solutions-49271becb34d>

![](img/6505778a9f91317473642c76cc955d85.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当在算法挑战的背景下处理字符串和数组时，我们的第一直觉通常围绕着内置方法。

让我们来看看这个看似简单的问题:

```
/* Description:
Given a sorted (ascending) array of integers, 
write a function that returns a sorted (ascending) array 
which contains the square of each number.
*/// Examples:
square([0, 1, 2, 3, 4, 5])
// => [0, 1, 4, 9, 16, 25])square([-7, -3, 2, 3, 11])
// => [4, 9, 9, 49, 121]
```

像许多其他人一样，我的第一反应是在映射出(`map()`)每个整数的平方版本后使用`sort()`方法，如下所示:

```
function square(arr) {
  arr = arr.map(num => num * num)
  return arr.sort((a, b) => a - b)
}
```

虽然我上面的解决方案达到了预期的结果，但它有些蛮力的方法导致了不太好的`O(n log(n))`时间复杂度。

那么如何才能提高运行时的复杂度呢？

这就是一个流行而有效的策略**两点技术**发挥作用的地方。

当迭代一个数组或字符串时，我们可以设置两个指针来搜索和/或比较两个元素。有三种设置指针的常用方法:

1.  在迭代开始时启动两个指针
2.  在迭代结束时启动两个指针
3.  将一个指针放在起点，另一个放在终点，两个指针都向对方移动，并在中间相遇。

在我们的`square()`示例中，它是如何工作的:

## 步骤 0:

初始化一个空数组来存储我们的结果。

## 第一步:

创建两个指针，`i`和`j`，其中`i`跟踪负整数，而`j`跟踪正整数。

## 第二步:

迭代数组。继续向前移动`j`，直到数组的元素(`arr[j]`)为正整数。

## 第三步:

在迭代中，比较索引 I 和索引 j 之间的平方元素，将较小的元素推/附加到结果数组中。

## 第四步:

在第 3 步的迭代之后，我们得到的数组将有一组排序后的整数。剩下的是索引 I 和索引 j 处的元素。

我们可以随后将剩余的元素推/附加到结果数组中。

## 第五步:

返回结果数组。

下面是**两点技术**方法(由圣地亚哥编码的[妇女提供):](https://www.meetup.com/Women-Who-Code-San-Diego/events/272815170/)

```
function squareTwoPointer(arr) {
  let result = []
  // create 2 pointers: i keeps track of negatives, j keeps track of positives
  let j = 0
  let i; while (j < arr.length && arr[j] < 0) {
    j++
    i = j - 1
  } while (j < arr.length && i >= 0) {
    if ((arr[i] * arr[i]) < (arr[j] * arr[j])) {
      result.push((arr[i] * arr[i]))
      i--
    } else {
      result.push((arr[j] * arr[j]))
      j++
    } } while (i >= 0) {
    result.push((arr[i] * arr[i]))
    i--
  } while (j < arr.length) {
    result.push((arr[j] * arr[j]))
    j++
  } return result
}
```

这个优化解决方案的时间复杂度是`O(n)`，因为我们一次只执行一次迭代，并对元素进行排序。

和几乎所有的算法挑战一样，有多种方法可以解决这个问题。两点策略似乎是一个很好的优化起点。

如果您还没有在解决问题的过程中应用两点技术，我希望这个例子能增强您的信心，提出更高性能的算法解决方案。

向前向上！