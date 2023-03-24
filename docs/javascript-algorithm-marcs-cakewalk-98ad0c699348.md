# JavaScript 算法:Marc 的 Cakewalk

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-marcs-cakewalk-98ad0c699348>

![](img/83b5f5c9a2b17942bd4290ba74f967b8.png)

对于今天的算法，我们将编写一个名为`marcsCakewalk`的函数，它将接受一个数组`calorie`作为输入。

一个叫马克的家伙喜欢吃纸杯蛋糕，但他也想保持健康，为此他每天都散步。他吃的每一个纸杯蛋糕都含有一定数量的卡路里，为了消耗这些卡路里，Marc 每天必须走一定的路程。该函数的目标是确定 Marc 在吃了一定数量的纸杯蛋糕后必须行走的最小英里数。

决定他必须为每个纸杯蛋糕走多少英里的方程式是:

```
miles = 2^j x c
```

`j`决定了吃掉的纸杯蛋糕的数量(或者数组索引),而`c`是每个纸杯蛋糕的卡路里数。

让我们在例子中使用这个等式:

```
let calorie = [1,4,5];
```

我们的`calorie`数组包含每个纸杯蛋糕的卡路里数。因为我们想找到 Marc 可以行走的最小英里数，所以我们按降序对数组进行排序。

```
[5,4,1]
```

现在，我们使用上面的等式来计算我们的最小里程数:

```
miles = (2^0)*5 + (2^1)*4 + (2^2)*1
miles = 17
```

我们看到马克在吃完 3 个纸杯蛋糕后，至少要走 17 英里才能保持健康。该功能将返回`17`。

现在让我们用代码做同样的事情。首先，我们在助手函数的帮助下，使用`sort()`方法对数组输入进行降序排序。要更详细地了解排序方法和助手函数的作用，您可以访问我第一次使用它的算法。

```
calorie.sort(function(a,b){
    return b - a;
});
```

我们还声明了一个名为`miles`的变量。我们最初将其设置为`0`，但这是我们将返回的变量。它将携带马克为了保持健康应该走的英里数。

```
let miles = 0;
```

接下来，我们遍历`calorie`数组。

```
for(let i = 0; i < calorie.length; i++){
    miles = miles + ((2**i) * calorie[i]);
}
```

在 for 循环中，我们使用本文开头的等式来计算英里数。

每吃掉一个纸杯蛋糕，前面每个纸杯蛋糕所需的里程数就会增加。

要获得某物的力量，可以使用`base ** exponent`或`Math.pow(base, exponent)`。Math.pow 可读性更好，但双星号方法打字更快。无论哪种方式，都不重要。

循环结束后，我们返回`miles`变量，我们就完成了。

```
return miles;
```

以下是剩余的代码:

```
function marcsCakewalk(calorie) {
  let miles = 0;

  calorie.sort(function(a,b){
    return b - a;
  });

  for(let i = 0; i < calorie.length; i++){
    miles = miles + ((2**i)*calorie[i]);
  }

  return miles;
}
```

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [## JavaScript 算法:交替字符

### 对于今天的算法，我们将编写一个名为 alternatingCharacters 的函数，它将一个字符串 s 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [](/javascript-algorithm-sherlock-and-squares-e1a39b5edef1) [## JavaScript 算法:夏洛克和正方形

### 对于今天的算法，我们将创建一个名为 squares 的函数，它将接受两个整数作为输入:a 和…

levelup.gitconnected.com](/javascript-algorithm-sherlock-and-squares-e1a39b5edef1) [](/javascript-algorithm-utopian-tree-b38100fff575) [## JavaScript 算法:乌托邦树

### 对于今天的算法，我们要写一个名为 utopianTree 的函数，它接受一个输入:一个整数 n。

levelup.gitconnected.com](/javascript-algorithm-utopian-tree-b38100fff575)