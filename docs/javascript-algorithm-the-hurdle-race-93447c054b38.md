# JavaScript 算法:跨栏赛跑

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-the-hurdle-race-93447c054b38>

![](img/87dda13c4760b7f6ea2dee88d5f304bc.png)

对于今天的算法，我们将编写一个名为`hurdleRace`的函数，它将接受一个整数`k`和一个数组`height`。

想象一下在跨栏比赛中，每个跨栏都有不同的高度。你有一个最大高度，你可以自然地跳。你服用一剂神奇的药剂，每服一剂将帮助你增加一个跳跃高度。这个功能的目的是看你需要服用多少剂量的药剂来跨越所有的障碍。

让我们举个例子:

```
let height: [1,2,3,3,2];
let k = 2;
```

对于我们的`height`变量，我们列出了你必须跳过的高度栏。

我们的`k`变量显示了我们能跳的最高高度。

如果你看看数组，3 是我们的最大高度。如果我们从数组中的最高数字中减去我们可以跳过的最高数字，`3 — 2 = 1`我们就可以得到跳过所有关卡所需的药剂剂量。在这个例子中，我们只需要 1 剂。

如果我们的`k`，我们能自然跳过的最大高度大于或等于数组中最高的栏，我们输出`0`。

让我们把这变成代码:

```
let heightSort = height.sort(function(a, b) {
    return a - b;
});
```

我们创建一个名为`heightSort`的变量，它将保存我们排序后的`height`数组。我们使用 sort 方法和一个辅助函数对数组进行排序。关于排序方法和辅助函数的更多信息，您可以在我第一次使用它的算法中阅读:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-sock-merchant-de9ffa754dfc) [## JavaScript 算法:袜子商人

### 对于今天的算法，我们将编写一个名为 sockMerchantthat 的函数，它将接受两个输入，一个整数 n…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-sock-merchant-de9ffa754dfc) 

现在我们已经对数组进行了排序，我们使用 if 语句将数组中的最大数字与我们的最大自然跳跃高度进行比较，`k`:

```
if(heightSort[heightSort.length -1] > k){
    return heightSort[heightSort.length -1] - k;
}else{
   return 0; 
}
```

因为我们对数组进行了排序，所以高度数组从最小到最大排序。最大值将是数组中的最后一个数字。为了得到数组中的最后一项，我们使用了下面的方法:

```
arrayName[arrayName.length - 1];
```

索引将是数组长度减 1(数组使用从零开始的编号)。

在 if 语句的条件中，我们检查最大跨栏高度是否大于我们能跳的最大高度。如果是，用我们的最大自然跳高减去最大跨栏高，返回答案。如果不是，则返回 0。

我们的算法到此结束。以下是完整的代码:

```
function hurdleRace(k, height) {
  let heightSort = height.sort(function(a, b) {
    return a - b;
  });

  if(heightSort[heightSort.length -1] > k){
    return heightSort[heightSort.length -1] - k;
  }else{
   return 0; 
  }
}
```

这里有一些最近的 javascript 算法文章，你可以看看:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-cats-and-a-mouse-fd60fb1811ba) [## JavaScript 算法:猫和老鼠

### 对于今天的算法，我们将创建一个名为 catAndMouse 的函数，它将接受三个整数:x、y 和…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-cats-and-a-mouse-fd60fb1811ba) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-electronics-shop-240a4fcb0e85) [## JavaScript 算法:电子商店

### 对于今天的算法，我们将编写一个名为 getMoneySpent 的函数，它将接受三个输入:两个数组…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-electronics-shop-240a4fcb0e85)