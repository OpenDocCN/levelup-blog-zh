# 解释了合并排序算法

> 原文：<https://levelup.gitconnected.com/merge-sort-algorithm-explained-93d1ecd6c736>

![](img/1eb9b5f8c32ef31faaa007e7647eb0f2.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [hobijist3d](https://unsplash.com/@hobijist3d?utm_source=medium&utm_medium=referral) 拍摄的照片

合并排序是对大型数据集进行排序时最常用的排序算法之一。它的工作原理是将一个数组分成两半，对每一半进行排序，然后将它们重新合并在一起。合并排序是由约翰·冯·诺依曼在 1945 年发明的分治算法。

要开始合并排序过程，我们首先需要将数组分成两半。这可以通过找到数组的中点并将数组分成两个子数组来完成:一个从数组的开始到中点，另一个从数组的中点到结尾。

接下来，我们通过对两个子数组中的每一个调用合并排序函数来对它们进行递归排序。这个过程一直持续到我们到达只有一个元素的数组的基本情况，这个元素已经被排序了。

一旦我们对两个子数组进行了排序，我们就可以通过比较每个子数组的元素并将较小的元素添加到新的合并数组中，以排序的方式将它们合并在一起。重复该过程，直到来自两个子阵列的所有元素都被添加到合并的阵列中。

以下是用 JavaScript 编写的合并排序算法的一个示例:

```
function mergeSort(array) {
  if (array.length <= 1) {
    return array;
  }

  // divide the array into two halves
  const midpoint = Math.floor(array.length / 2);
  const leftHalf = array.slice(0, midpoint);
  const rightHalf = array.slice(midpoint);

  // recursively sort each half
  const leftSorted = mergeSort(leftHalf);
  const rightSorted = mergeSort(rightHalf);

  // merge the sorted halves back together
  return merge(leftSorted, rightSorted);
}

function merge(leftHalf, rightHalf) {
  const merged = [];
  let leftIndex = 0;
  let rightIndex = 0;

  // add the smaller element from each subarray to the merged array
  while (leftIndex < leftHalf.length && rightIndex < rightHalf.length) {
    if (leftHalf[leftIndex] < rightHalf[rightIndex]) {
      merged.push(leftHalf[leftIndex]);
      leftIndex += 1;
    } else {
      merged.push(rightHalf[rightIndex]);
      rightIndex += 1;
    }
  }

  // add the remaining elements from each subarray to the merged array
  return merged.concat(leftHalf.slice(leftIndex)).concat(rightHalf.slice(rightIndex));
}

// example usage:
const sortedArray = mergeSort([5, 2, 8, 1, 9, 3, 7, 6, 4]);
console.log(sortedArray); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

合并排序的一个好处是它是一个稳定的排序，意味着相等元素的顺序被保留。它也是一种非常高效的排序算法，时间复杂度为 O(n*log(n))。这意味着对数组进行排序所需的时间不会因为数组的大小而显著增加。

合并排序是一种高级算法，所以如果你还不理解，不要难过。这就是为什么我写这些文章来把学习曲线拉直一点。

希望这有所帮助！