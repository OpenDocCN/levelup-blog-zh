# JavaScript 算法:看电影的美好时光

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-beautiful-days-at-the-movies-81561dd03212>

![](img/480cffaa491cfea05e7841539850fe61.png)

对于今天的算法，我们将编写一个名为`beautifulDays`的函数，它将接受三个整数作为输入:`i`、`j`和`k`。

想象你在玩一个整数游戏，在这个游戏中你找到了一个数和它的倒数之间的区别。一个例子是得到数字 16，然后反过来得到数字 61。61 减 16 得 45。现在想象一下用这个游戏来知道什么时候去看电影。

你想去看电影，但你只会在“美好的一天”去看。美好的一天是由美好的数字决定的。一个美丽的数字是当你找到一个数和它的倒数之差，并且结果能被一个特定的数整除`k`。

这个函数的目标是获取一个数字范围[i…j],并且只在这个范围内找到漂亮的数字。该函数将输出该范围内美丽数字的总数。让我们举个例子:

```
let i = 13;
let j = 16;
let k = 6;
```

你想在第 13、14、15 和 16 天去看电影。这就是范围。现在我们需要知道哪些日子是美好的。我们取范围内的第一个数字，13:

```
31 — 13 = 18
18 / 6 = 3
```

我们知道第 13 天是美好的，因为在取了这个数和它的倒数之差之后，我们用它除以`k`，结果等于一个整数 3。

我们对该范围内的其余数字进行同样的处理:

14:

```
41 — 14 = 27
27 / 6 = 4.5 
// not a whole number so 14 is not beautiful
```

15:

```
51 — 15 = 36
36 / 6 = 6
// a whole number so 15 is beautiful
```

16:

```
61 — 16 = 45
45 / 6 = 7.5
// not a whole number so 16 is not beautiful
```

在查看了我们范围内的所有数字后，我们发现只有 13 和 15 这两天被认为是美丽的。该函数将返回 2。

让我们把这变成代码:

```
let beautiful = [];
```

我们首先创建一个空数组`beautiful`，它将保存所有漂亮的数字。

接下来，我们循环遍历这个范围，包括 I 和 j。

```
for(let start = i; start <= j; start++){

    let numString = start+"";
    let reverse = numString.split('').reverse().join('');if((start - reverse*1) % k === 0){
      beautiful.push(start)
    }

}
```

从第一条语句开始，我们将范围内的每个数字转换为一个字符串。我们通过将数字连接到一个空字符串来做到这一点。

```
let reverse = numString.split('').reverse().join('');
```

接下来，为了反转字符串，我们需要准备字符串，以便可以使用 JavaScript `reverse()`方法。相反的方法适用于数组，所以我们使用`split()`方法将一个字符串分割成一个子字符串数组。

我们在 split 方法中使用空字符串`''`作为分隔符，因为我们希望字符串在每个字符之间分开。现在我们的字符串是一个数组，我们可以反转它。下一步是什么？

我们通过将数组中的所有字符连接在一起形成一个字符串来将数组转换回字符串。我们再次使用空字符串作为分隔符，因为当我们连接字符串时，我们不希望任何东西来分隔每个字符。

```
if((start - reverse*1) % k === 0){
      beautiful.push(start)
}
```

在我们的 if 语句中，我们检查范围中的当前数字是否漂亮。我们将从原始数字中减去反转的数字。为了做减法，我们需要将反转的数字转换回整数。我们通过将字符串乘以 1 来实现这一点，数字又是一个整数。现在我们可以做减法了。我们使用模`%`运算符来确定我们的减法结果是否被`k`整除。被整除的数将输出`0`,意思是没有余数。如果它被平分，那么我们将范围内的数字添加到`beautiful`数组中。

当我们完成我们的循环时，我们输出范围内美丽数字的数目。我们通过输出我们的`beautiful`数组的长度来得到这个。

```
return beautiful.length;
```

以下是剩余的代码:

```
function beautifulDays(i, j, k) {
  let beautiful = [];for(let start = i; start <= j; start++){let numString = start+"";
    let reverse = numString.split('').reverse().join('');if((start - reverse*1) % k === 0){
      beautiful.push(start)
    }
  }
  return beautiful.length;
}
```

如果您觉得这个算法有帮助或有用，请查看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-angry-professor-89570ba8d863) [## JavaScript 算法:愤怒的教授

### 对于今天的算法，我们将编写一个名为 angryProfessor 的函数，它将接受两个输入:一个整数…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-angry-professor-89570ba8d863) [](https://medium.com/javascript-in-plain-english/solve-me-first-7a2f53789228) [## JavaScript 中的算法:先解决我

### 在 JavaScript 中完成一个名为 solveMeFirst 的函数，这个函数做的是计算

medium.com](https://medium.com/javascript-in-plain-english/solve-me-first-7a2f53789228) [](https://medium.com/@endubueze00/staircase-javascript-algorithm-ec6ae8c00ec2) [## 阶梯| Javascript 算法

### 在今天的算法中，我们将创建一个名为楼梯的函数来构建一组楼梯。如果你已经…

medium.com](https://medium.com/@endubueze00/staircase-javascript-algorithm-ec6ae8c00ec2)