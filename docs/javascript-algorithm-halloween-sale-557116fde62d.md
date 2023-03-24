# JavaScript 算法:万圣节特卖

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-halloween-sale-557116fde62d>

![](img/502cf39db5d4ac11772b0938d6a7f8b5.png)

今天我们将创建一个名为`howManyGames`的函数，它将接受 4 个整数作为输入:`p`、`d`、`m`和`s`。

快到万圣节了，你想买电子游戏。你去在线视频游戏商店购买游戏。所有游戏的售价都是`p`美元，但是因为正在进行万圣节促销，你购买的第一个游戏将以`p`美元出售，并且你购买的每一个后续游戏都将比前一个游戏便宜`d`美元。这一趋势将会持续下去，直到游戏成本开始低于或等于`m`美元。当这种情况发生时，所有的游戏都将花费`m`美元。

你有 10 美元的预算，所以当你买游戏时，你必须记录你钱包里还有多少钱。该函数的目标是输出用你的钱可以买多少游戏。让我们举个例子:

```
let p = 20;
let d = 3;
let m = 6;
let s = 80;
```

对于我们上面的投入，你购买的第一个游戏将花费 20 美元，之后的所有游戏将比之前的游戏少花费 3 美元。当游戏开始花费 6 美元或更少时，那么之后的所有游戏都将花费 6 美元。我们钱包里总共有 80 美元。

```
20 – 0 = 20
20 – 3 = 17
17 – 3 = 14
14 - 3 = 11
11 - 3 = 8
8 - 3 = 6
...
```

我们从 20 美元开始，然后每买一个游戏就减去 3 美元。我们一直打折，直到我们买了一个总价为 6 美元的游戏。但是为了记录我们的花费，我们把游戏的花费加起来:

```
20 + 17 + 14 + 11 + 8 + 6 = 76
```

如果我们再买一个游戏(另外 6 美元)，我们会超出预算 2 美元，所以我们到此为止。我们数了数我们花了 76 美元的游戏，结果是 7 个。该函数将输出 7。

让我们把这变成代码。

```
let totalCost = p;
let gameCount = 0;
```

`totalCost`变量将保存我们购买游戏的成本。我们将使用该变量并将其与输入`s`进行比较，以了解我们是否在预算范围内。

`gameCount`变量将保存我们在预算内可以购买的游戏数量。

接下来，我们将创建一个 while 循环。这个 while 循环将一直持续下去，直到`totalCost`大于或等于我们钱包里的钱数`s`。在这一点上，如果我们继续下去，我们将超过预算，所以循环将结束。

```
while(totalCost <= s){
    p = p - d; if(p <= m){
      totalCost = totalCost + m;
      gameCount++;
    }else{
      totalCost = totalCost + p;
      gameCount++;
    }

}
```

从循环中的第一行开始，我们开始从开始游戏的成本中减去`p`美元。

然后在 if 语句中，我们检查我们以折扣价购买的游戏的价格是否小于或等于`m`。此时，我们开始跟踪游戏的总成本。如果游戏成本小于等于`m`，那么我们买的每一款游戏都会一直花费`m`。我们将成本添加到`totalCost`变量中。

如果我们买的游戏仍然大于`m`，那么我们把贴现成本加到`totalCost`变量中。在这两种情况下，我们增加变量`gameCount`来跟踪我们购买了多少游戏。

我们继续循环，直到不再超出预算。循环将结束，我们返回`gameCount`。

```
return gameCount;
```

以下是完整的代码:

```
function howManyGames(p, d, m, s) {

  let totalCost = p;
  let gameCount = 0;

  while(totalCost <= s){
    p = p - d;
    if(p <= m){
      totalCost = totalCost + m;
      gameCount++;
    }else{
      totalCost = totalCost + p;
      gameCount++;
    }

  }

  return gameCount;
}
```

我们的代码到此结束。

以下是我的一些其他 JavaScript 算法，你可以看看:

[](https://medium.com/swlh/mini-max-sum-a62de1d7f4f1) [## 最小-最大和

### 对于今天的算法，我们将创建一个名为 miniMaxSum 的函数。在这个函数中，给你一个数组…

medium.com](https://medium.com/swlh/mini-max-sum-a62de1d7f4f1) [](/javascript-algorithm-time-conversion-71dc15dd13b8) [## JavaScript 算法:时间转换

### 对于今天的算法，我们将创建一个名为 timeConversion 的函数。这个函数将接受一个字符串作为…

levelup.gitconnected.com](/javascript-algorithm-time-conversion-71dc15dd13b8) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-migratory-birds-848ad6a99ac3) [## JavaScript 算法:候鸟

### 对于今天的算法，我们将编写一个名为 migratoryBirds 的函数，在这个函数中，我们将接受一个…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-migratory-birds-848ad6a99ac3)