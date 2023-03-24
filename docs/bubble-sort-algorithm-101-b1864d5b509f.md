# 冒泡排序算法 101

> 原文：<https://levelup.gitconnected.com/bubble-sort-algorithm-101-b1864d5b509f>

用 JavaScript 实现 ***冒泡排序算法*** 的简单指南。

![](img/4c64b3d976853c98ad4c7feb75da0745.png)

照片由[莎伦·麦卡琴](https://unsplash.com/@sharonmccutcheon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 什么是冒泡排序算法(BSA)？

***BSA*** 是理解和实现起来相对简单的算法。 ***它的设计目的是对一组未排序的数字进行排序。不幸的是，它不是一个特别有效的排序算法，并且很少(如果曾经)在企业级代码中使用。***

## 它是如何工作的？

***BSA*** 的工作原理是将数组中的每个元素与其紧邻元素进行比较，以确定哪个元素更大。两个元素中较大的一个在右边，较小的一个在左边。重复该过程，直到整个阵列被排序并且不再发生交换。

这是一个非常棒的可视化过程(只需点击页面顶部的气泡排序按钮就能看到它的工作):

 [## 比较排序可视化

### 编辑描述

www.cs.usfca.edu](https://www.cs.usfca.edu/~galles/visualization/ComparisonSort.html) 

这里有一个很好的逐步说明，您可以按照自己的步调来执行:

[](https://www.hackerearth.com/practice/algorithms/sorting/bubble-sort/visualize/) [## 冒泡排序可视化|排序|算法|黑客地球

### 关于冒泡排序的详细教程，以提高您对{{ track }}的理解。也尝试练习问题来测试&…

www.hackerearth.com](https://www.hackerearth.com/practice/algorithms/sorting/bubble-sort/visualize/) 

因此，希望您对 ***BSA*** 的样子以及它如何对一个数字列表或数组进行排序有一个很好的了解。现在让我们深入研究这个算法的一个基本例子，使用一个 JavaScript 数组来巩固这个概念。

```
**let unsortedArray = [2,5,4,3];****// Start bubble sort algorithm
1\. Is 2 > 5
2\. No, so move to the next element in the array
3\. Is 5 > 4
4\. Yes, so swap the elements
5\. Updated array --> [2,4,5,3];
6\. Restart the process
7\. Is 2 > 4
8\. No, so move to the next element in the array
9\. Is 4 > 5 
10\. No, so move to the next element in the array
11\. Is 5 > 3
12\. Yes, so swap the elements
13\. Updated array --> [2,4,3,5]
14\. Restart the process
15\. Is 2 > 4
16\. No, so move to the next element in the array
17\. Is 4 > 3
18\. Yes, so swap the elements
19\. Updated array --> [2,3,4,5]
20\. Restart the process
21\. We run through each comparison again and no swaps are made,
    therefore we have a sorted array**
```

> **挑战#1:使用上面的步骤自己实现一个 BSA！**

# 履行

好了，这是你们期待已久的时刻！下面是一个 ***BSA*** 在 JavaScript 中的两个实现，一个使用 ***递归*** 另一个使用 ***迭代*** 。

## 但是我们首先需要的是一个 ***交换函数*** ，它将帮助我们在满足特定条件时移动数组中的元素。

## 交换功能

```
// swap the elements at the first and second index given **const swap = (arr, first, second) => {
  let temp = arr[first];
  arr[first] = arr[second];
  arr[second] = temp;
};**
```

## 冒泡排序(递归)

让我们来看看这里发生了什么:

1.  虚拟检查，如果数组只有一个元素，它的排序。
2.  创建一个默认设置为 false 的检查变量
3.  遍历数组。如果第一个元素大于下一个相邻的元素，交换它们并将 check 改为 true
4.  循环完成后，如果 check 仍然为 false，这意味着没有进行交换，并且数组已排序，则返回数组
5.  如果 check 为真，则发生交换，我们再次运行 bubbleSortRecursive()，直到数组排序完毕

```
**const bubbleSortRecursive = (array) => {** // Dummy check for single element in the array **if (array.length <= 1) return array;** // Boolean represents whether elements in the array have 
  // been swapped
  **let check = false;**// Run through array and swap elements if second element 
  // is greater than the first **for(let i = 0; i < array.length; i++) {
    if (array[i + 1] < array[i]) {
      swap(array, i, i + 1);
      check = true
    }
  };** // If the elements haven't been swap the array is 
  // sorted, return array
  // else recursively call bubbleSortRecursive()
  **if (!check) {
    return array
  } else {
    return bubbleSortRecursive(array);
  }
};**
```

嘭！一个漂亮、干净的递归实现！

## 冒泡排序(迭代)

这是上面函数的迭代版本。逻辑有点复杂，所以让我们一起来看一下:

1.  创建一个停止变量，该变量将根据外部循环运行的次数进行更新，并缩短内部循环扫描的数组的距离。 ***为什么会这样？*** 因为 ***BSA*** 总是把最大的元素推到数组末尾。所以在我们第一次迭代之后，最大的数字已经在数组的末尾了。我们可以从未来的扫描中消除它，以降低时间复杂度并创建有效的退出条件。
2.  我们在外部循环中遍历整个数组来检查每个元素。
3.  对于每个外部循环，我们运行一个内部循环来执行相邻元素的实际比较。如果第一个元素大于下一个元素，使用 ***交换功能*** 就地交换它们。
4.  一旦整个过程完成，返回排序后的数组。

```
**const bubbleSort2 = (array) => {**// Initialize a stop variable that will be used as
  // the stopping point for the inner loop.
  // Once an inner cycle is complete, the last variable 
  // will become the largest number in the array.
  // We can therefore ignore it and leave it in place, 
  // and shorten the area our loop scans during the next
  // iteration. Hence the array.length - i; **let stop;** **for (let i = 0; i < array.length; i++) {
    for (let j = 0, stop = array.length - i; j < stop; j++) {
      if (array[j] > array[j + 1]){
        swap(array, j, j + 1);
      }
    }
  };
  return array;
};**
```

# 结论

太好了，我们又完成了另一个算法教程，干得好。

对于那些对*BSA 的 ***时间复杂度*** 感兴趣的人，准备好失望吧…对于 ***BSA*** 来说，最好的情况是 **O(n)** 并且它可能变得更糟，当未排序的数组前端加载所有最大的数，后端加载所有*

*很有可能你永远不会在产品级代码中使用这种算法，但是知道这一点是很好的，并且肯定会在初级开发人员的技术面试中有所帮助。*

*这里有几个很好的资源可供选择的解释，对于那些想深入研究的人来说:*

***一篇关于气泡排序的伟大文章由** [**凯尔·詹森**](https://medium.com/@kylejensen) **—***

*[](https://medium.com/javascript-algorithms/javascript-algorithms-bubble-sort-3d27f285c3b2) [## Javascript 算法—冒泡排序

### Javascript 算法系列中的下一个算法是冒泡排序。像插入排序一样，冒泡排序是一种比较…

medium.com](https://medium.com/javascript-algorithms/javascript-algorithms-bubble-sort-3d27f285c3b2) 

**一篇关于冒泡排序的惊人清晰的维基百科文章—**

[](https://en.wikipedia.org/wiki/Bubble_sort) [## 冒泡排序

### 冒泡排序，有时也称为下沉排序，是一种简单的排序算法，它重复遍历…

en.wikipedia.org](https://en.wikipedia.org/wiki/Bubble_sort) 

非常感谢您的阅读，如果您想要上面的完整代码库，请查看我的 Github Repo 并尝试我的实现:

[](https://github.com/twjsanderson/bubbleSortAlgo) [## twjsanderson/bubbleSortAlgo

### 在 GitHub 上创建一个帐户，为 twjsanderson/bubbleSortAlgo 的开发做出贡献。

github.com](https://github.com/twjsanderson/bubbleSortAlgo) 

***好好玩，继续编码！****