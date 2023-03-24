# JavaScript 算法:乌托邦树

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-utopian-tree-b38100fff575>

![](img/e3d2b0753fc8a0c723fbdd3894ffc628.png)

对于今天的算法，我们将编写一个名为`utopianTree`的函数，它将接受一个输入:一个整数`n`。

我们有一棵乌托邦树，每年经历两次生长周期。在春天，它的高度加倍，在夏天，它的高度增加 1(无论你想用什么测量系统)。该函数的目的是在一定数量的生长周期后输出树的高度。

如果我们想知道 5 个生长周期后树的高度，下面是我们的计算方法:

```
let n = 5;
```

我们的输入`n`是将保持增长周期数的变量。我们从生长周期`0`开始计算树的高度。

在`0`周期，树的初始高度是`1`。

在第 1 周期，树的高度加倍，所以它的高度现在是`2`。

在第 2 周期，树的高度增加了`1`。树的高度是`3`。

在第 3 周期，树的高度再次翻倍，所以它的高度现在是`6`。

在第 4 周期，树的高度增加了`1`。树的高度现在是`7`。

在最后一个周期，树的高度加倍，使得树的高度`14`。

该功能将输出`14`。

当我们看到我们如何得到我们的答案时，我们开始看到一个模式。从周期`1`开始，如果周期数是奇数，树的高度加倍。如果是偶数，树的高度只增加 1。

让我们把这变成代码。我们创造变量:

```
let cycle = 1;
let height = 1;
```

当我们从 1 数到`n`时，我们的`cycle`变量将帮助我们跟踪树在每个生长周期的高度。我们知道树在第 0 周期的初始高度，所以我们想从第 1 周期开始测量树的高度。

`height`变量将保存树的高度。该函数将输出此变量。我们从 1 开始计数，因为这是树的初始高度。

接下来，我们使用 while 循环:

```
while (cycle <= n){
    if(cycle % 2 !== 0 ){
      height *= 2;
    }else{
      height++;
    }
    cycle++;
}
```

我们希望 while 循环一直循环下去，直到变量`cycle`不大于输入`n`为止。

在上面的例子中，我们注意到如果生长周期是奇数，树的高度将会加倍。所以不管树的当前高度是多少，我们都要乘以 2。

*注意:if 语句中的等式`height *= 2;`，是`height = height * 2;`的一种简写方式。

如果生长周期是偶数，我们将树的高度增加 1。

在循环通过 if 语句后，我们增加循环变量，这样我们就可以在下一个生长周期中测量树的高度。

循环完成后，我们返回高度变量。

```
return height;
```

我们的算法到此结束。以下是剩余的代码:

```
function utopianTree(n) {
  let cycle = 1;
  let height = 1;

  while (cycle <= n){
    if(cycle % 2 !== 0 ){
      height *= 2;
    }else{
      height++;
    }
    cycle++;
  }

  return height;}
```

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://codeburst.io/javascript-algorithm-designer-pdf-viewer-486c68638991) [## JavaScript 算法:设计器 PDF 查看器

### 对于今天的算法，我们将编写一个名为 designerPdfViewer 的函数，它将接受两个输入:一个…

codeburst.io](https://codeburst.io/javascript-algorithm-designer-pdf-viewer-486c68638991) [](/javascript-algorithm-the-hurdle-race-93447c054b38) [## JavaScript 算法:跨栏赛跑

### 对于今天的算法，我们将编写一个名为跨栏赛跑的函数，它将接受一个整数 k 和一个数组高度。

levelup.gitconnected.com](/javascript-algorithm-the-hurdle-race-93447c054b38)