# JavaScript 算法:设置警报

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-set-alarm-54a7abd094d7>

## 我们将编写一个函数，它将根据您是否有工作以及是否在度假来确定是否值得设置闹钟。

![](img/0a9586f30ea01fc6e16cbe8f1d4bf17e.png)

我们将编写一个名为`setAlarm`的函数，它将接受两个布尔值(`employed`和`vacation`)作为参数。

该函数的目标是在设置警报时返回 true 或 false。这将取决于你是否被雇用和休假。

以下是所有布尔组合:

```
setalarm(true, true) -> false // employed and on vacation
setalarm(false, true) -> false // unemployed but on vacation
setalarm(false, false) -> false // unemployed but not on vacation
setalarm(true, false) -> true // employed but not on vacation
```

根据输入，如果需要设置闹铃，返回`true`，如果不需要设置闹铃，返回`false`。

我们可以从上面的组合列表中看到，你唯一需要设置闹钟的时间是你在工作而不是在度假的时候。其他的都可以回`false`。

正因为如此，我们可以编写一个简单的一行 return 语句。

```
function setAlarm(employed, vacation){
  return employed && !vacation;
}
```

或者

```
function setAlarm(employed, vacation){
  return (employed && !vacation) ? true : false;
}
```

这两种解决方案都可行，尽管第一个选项可能是首选，因为该函数将返回该语句评估的任何布尔值。你真的不需要 if 语句，除非它能让人安心。

我们的功能到此结束。

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://medium.com/@endubueze00/javascript-algorithm-soccer-goal-totals-93223792b67f) [## JavaScript 算法:足球进球总数

### 我们将解决并研究在 JavaScript 中，在一个函数中添加多个数字总和的许多方法。

medium.com](https://medium.com/@endubueze00/javascript-algorithm-soccer-goal-totals-93223792b67f) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-calculate-body-mass-index-6f14dce4075d) [## JavaScript 算法:计算身体质量指数

### 我们写了一个函数，计算你的身体质量指数，并确定你是否超重，体重不足，肥胖或正常。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-calculate-body-mass-index-6f14dce4075d) [](https://codeburst.io/javascript-algorithm-name-shuffler-e0308ff9b449) [## JavaScript 算法:命名洗牌机

### 我们将编写一个函数来颠倒或交换某人全名的顺序。

codeburst.io](https://codeburst.io/javascript-algorithm-name-shuffler-e0308ff9b449)