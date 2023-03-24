# JavaScript 算法:寻找奇数整数

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-find-the-odd-int-4410a7e7f0a3>

## 给定一个整数数组，找出出现奇数次的整数

![](img/730e6894bec24b945c7726b054f0f382.png)

瑞安·昆塔尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将编写一个名为`findOdd`的函数，它接受一个数组`arr`作为参数。

对于这个函数，我们给你一个数组。这个数组包含整数，除了一个数字之外，所有的整数都有偶数个副本。该函数的目标是找到出现奇数次的数字并返回它。

如果没有奇数个重复的数字，函数将返回`-1`。

示例:

```
findOdd([1,1,1,1,1,1,10,1,1,1,1]) // output: 10findOdd([20,1,1,2,2,3,3,5,5,4,20,4,5]) // output: 5
```

我们要做的第一件事是循环遍历`arr`。

```
for (let i = 0; i < arr.length; i++) {
    const count = arr.filter(value => value === arr[i]).length;
    if (count % 2 == 1) {
        return arr[i];
    }
}
```

在 for 循环的第一行，我们使用`filter()`方法创建一个新的数组，包含所有匹配当前迭代值的值。我们还使用`length`方法来帮助我们获得`arr`中特定数字的重复数。

我们将这个数字赋给`count`变量。

```
const count = arr.filter(value => value === arr[i]).length;
```

我们使用 if 语句来检查`count`是否是奇数。如果是，我们通过返回数字来停止循环。

如果循环结束，我们返回`-1`说数组中没有奇数个重复的数字。

```
return -1;
```

就是这样。

下面是完整的函数:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://javascript.plainenglish.io/javascript-algorithm-simple-pig-latin-f4138eb91b69) [## JavaScript 算法:简单的猪拉丁

### 将字符串翻译成拉丁文

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-simple-pig-latin-f4138eb91b69) [](/javascript-algorithm-unique-in-order-bba51ffbb1f5) [## JavaScript 算法:顺序唯一

### 返回唯一的项目列表，而不改变元素的原始顺序

levelup.gitconnected.com](/javascript-algorithm-unique-in-order-bba51ffbb1f5) [](/javascript-algorithm-categorize-new-member-8cb175a469) [## JavaScript 算法:对新成员进行分类

### 创建一个函数，根据数组中的数据对门球俱乐部的新成员进行分类

levelup.gitconnected.com](/javascript-algorithm-categorize-new-member-8cb175a469) 

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)