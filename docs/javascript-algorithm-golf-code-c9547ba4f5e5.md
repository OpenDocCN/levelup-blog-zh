# JavaScript 算法:高尔夫代码

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-golf-code-c9547ba4f5e5>

## 我们将编写一个函数，根据你完成一洞所需的击球次数返回你的得分项。

![](img/ab2539cd75a74b1edc8b0b6d4aa41035.png)

今天我们将编写一个名为`golfScore`的函数，它将接受两个整数参数(`par`和`strokes`)作为输入。

在高尔夫球比赛中，每个洞都有标准杆。标准杆数是高尔夫球手将球打入洞内以完成比赛的平均杆数。根据你画了多少笔，有不同的名字。

该函数的目标是根据球员击球入洞的次数返回这些名字。

如果笔画= `1`，返回`"Hole-in-one!"`

如果笔画≤ `par — 2`，返回`"Eagle"`

如果 strokes = `par — 1`，返回`"Birdie"`

如果 strokes = `par`，返回`"Par"`

如果 strokes = `par + 1`，返回`"Bogey"`

如果 strokes = `par + 2`，返回`"Double Bogey"`

如果笔画≥ `par + 3`，返回`"Go Home!"`

看完上面的列表后，我们将创建一系列与上面列表相对应的 if 语句。把上面的列表想象成伪代码。我们唯一要做的就是把它变成实际的代码。

```
if (strokes == 1) {
    return "Hole-in-one!";
} else if (strokes <= par - 2) {
    return "Eagle";
} else if (strokes == par - 1) {
    return "Birdie";
} else if (strokes == par) {
    return "Par";
} else if (strokes == par + 1) {
    return "Bogey";
} else if (strokes == par + 2) {
    return "Double Bogey";
} else {
    return "Go Home!";
}
```

这就结束了我们的功能。以下是剩余的代码:

```
function golfScore(par, strokes) {
    if (strokes == 1) {
        return "Hole-in-one!";
    } else if (strokes <= par - 2) {
        return "Eagle";
    } else if (strokes == par - 1) {
        return "Birdie";
    } else if (strokes == par) {
        return "Par";
    } else if (strokes == par + 1) {
        return "Bogey";
    } else if (strokes == par + 2) {
        return "Double Bogey";
    } else {
        return "Go Home!";
    }
}
```

如果你想阅读更多的 JavaScript 算法文章，这里有一些最近的:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-migratory-birds-848ad6a99ac3) [## JavaScript 算法:候鸟

### 对于今天的算法，我们将编写一个名为 migratoryBirds 的函数，在这个函数中，我们将接受一个…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-migratory-birds-848ad6a99ac3) [](https://codeburst.io/javascript-algorithm-designer-pdf-viewer-486c68638991) [## JavaScript 算法:设计器 PDF 查看器

### 对于今天的算法，我们将编写一个名为 designerPdfViewer 的函数，它将接受两个输入:一个…

codeburst.io](https://codeburst.io/javascript-algorithm-designer-pdf-viewer-486c68638991) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-intersection-22d6eaa4c523) [## JavaScript 算法:寻找交集

### 对于今天的算法，我们要写一个叫 FindIntersection 的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-intersection-22d6eaa4c523)