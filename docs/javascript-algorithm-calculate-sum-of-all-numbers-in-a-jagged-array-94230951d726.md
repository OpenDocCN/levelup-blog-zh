# JavaScript 算法:计算交错数组中所有数字的和

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-calculate-sum-of-all-numbers-in-a-jagged-array-94230951d726>

## 创建一个计算交错数组中所有数字之和的函数。

![](img/edc4056c1bc2d5a727099bda7f27f60f.png)

澳大利亚八月在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我们将编写一个名为`sumArray`的函数，它将接受一个数组`ar`作为参数。

对于这个函数，我们给你一个交错的数组。交错数组是包含数字和其他数组的组合的数组。数组可以有多级深度。

该函数的目标是找出交错数组中所有数字的总和。

示例:

```
let ar = [1, 2, [15, [23], [5, 12]], [100]];//output: 158
```

接下来，我们将找到一种方法，让我们的函数遍历所有内部数组，以获得我们的总和。实现这一点的一个关键函数是递归。

我们要做的第一件事是创建一个名为`sum`的变量，并给它赋值`0`。这是我们将在函数结束时返回的变量。

```
let sum = 0;
```

接下来，我们将使用 for 循环来遍历数组:

```
for(let el of ar){        
    if (Array.isArray(el)){            
        el = sumArray(el);        
    }        
        sum += el;    
}
```

让我们看看 for 循环内部发生了什么。

在我们的第一个 if 语句中，我们使用`Array.isArray()`方法检查当前迭代的元素是否是一个数组。如果它是一个数组，我们使用递归将数组插回到函数中。这将递归地深入到数组中的任何级别，直到我们在一个数字处终止。

使用上面的例子，我将创建一个正在发生的视觉效果。

```
[1, 2, [15, [23], [5, 12]], [100]]
```

第一个和第二个元素是数字，所以它们被添加到我们的`sum`变量中，但是我们的第三个元素是一个数组。

```
[15, [23], [5, 12]]
```

我们使用递归深入到主数组的另一个层次。当数组进入 for 循环时，第一个元素被添加到我们的`sum`中。但是我们在第二个元素中又遇到了另一个数组，所以我们使用递归来更深入一层。

我们现在有三层了。

```
[23]
```

因为这个数组只有一个不是数组的项，所以我们将这个数字添加到我们的`sum`中，并返回到主数组的第二层。

```
[15, [23], [5, 12]]
```

for 循环继续到下一个元素，这个元素恰好也是一个数组。

```
[5, 12]
```

我们递归地进入数组，遍历它，并将`5`和`12`添加到`sum`中。

该功能返回到主数组的第一级，并前进到下一个元素`[100]`。

```
[1, 2, [15, [23], [5, 12]], [100]]
```

我们使用递归返回来再次访问主数组的第二层。一旦我们进入 for 循环，我们就把`100`加到`sum`上。

该函数不断检查每个元素。它要么给`sum`加一个数，要么进入另一个数组。对每一级阵列重复这一过程。一旦它完成了对该特定级别的数组中所有元素的检查，它将继续向后工作，以确保所有的数字都被相加。

这种级别之间的来回跳跃一直持续到函数最终到达我们主数组的第一级并到达末尾。

当 for 循环结束时，我们返回我们的`sum`。

下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/swlh/javascript-algorithm-sorted-union-9e316654561f) [## JavaScript 算法:排序联合

### 我们编写一个函数，它将返回从两个或更多其他数组中获取的唯一值的数组。

medium.com](https://medium.com/swlh/javascript-algorithm-sorted-union-9e316654561f) [](https://javascript.plainenglish.io/javascript-algorithm-distance-between-points-7fe0026857e3) [## JavaScript 算法:两点之间的距离

### 创建一个函数来计算由 x 和 y 坐标定义的两点之间的距离。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-distance-between-points-7fe0026857e3) [](/javascript-algorithm-sum-of-all-digits-in-a-number-f1882e323ab1) [## JavaScript 算法:一个数中所有数字的总和

### 写一个函数，计算一个数中所有数字的和。

levelup.gitconnected.com](/javascript-algorithm-sum-of-all-digits-in-a-number-f1882e323ab1)