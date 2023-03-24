# JavaScript 算法:龙族生存

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-dragon-survival-a06404e65595>

我们写了一个函数，它将决定我们的童话英雄是否有足够的子弹击落每一条火龙

![](img/f199874e1ab2a0d157a9b7689a8a9001.png)

我们将编写一个名为`hero`的函数，它将接受两个整数(`bullets`和`dragons`)作为参数。

我们有个家伙接近了一座城堡。一个女人在城堡的一座塔里大喊“救命！”。有几条龙正在攻击城堡。我们在某种童话故事里，你知道在童话故事里，当你从燃烧的城堡里救出一个漂亮的女人会发生什么。你发现她是国王的女儿，如果你救了她，你就可以娶她，并最终在她父亲死后统治这片土地(以及其他典型的童话情节)。

回到正题，每条龙需要两颗子弹才能把它们放倒。想要帮助公主的家伙的枪里有一定数量的子弹。给定一个具体的子弹数量，这个功能的目标是确定他是否有足够的子弹来打倒攻击城堡的龙的数量。如果他有足够的能力把他们都干掉，就返回`true`，如果没有就返回`false`。

举个例子，

```
let bullets = 6;
let dragons = 3;
// truelet bullets = 7;
let dragons 4;
// false
```

第一个例子，有`3`龙。如果每条龙需要 2 颗子弹才能打败它们，这意味着他需要`3 * 2 = 6`颗子弹。既然他有`6`子弹，他就有足够的子弹放倒`3`龙。函数返回`true`。

另一方面，在第二个例子中，有`4`龙。他需要`4 * 2 = 8`子弹来干掉所有`4`龙。他只有 7 发子弹，所以他不可能打败所有的龙。该函数返回`false`。

现在我们知道发生了什么，让我们为它写一个函数。我们首先创建一个名为`bulletsToKill`的变量。这个变量将保存击败恶龙所需的子弹数量。

```
let bulletsToKill = 2 * dragons;
```

之后，我们返回一个条件语句，检查所需的子弹数是否小于或等于拥有的子弹数。该语句将返回`true`或`false`。

```
return bulletsToKill <= bullets
```

下面是该函数的其余部分:

```
function hero(bullets, dragons){
  let bulletsToKill = 2 * dragons;
  return bulletsToKill <= bullets;
}
```

如果你觉得这个算法有帮助，可以看看我最近的 JavaScript 算法解决方案:

[](/javascript-algorithm-using-objects-for-lookups-d28f34a59504) [## JavaScript 算法:使用对象进行查找

### 我们将把一个 switch 语句转换成一个 JavaScript 对象，并根据给定的…

levelup.gitconnected.com](/javascript-algorithm-using-objects-for-lookups-d28f34a59504) [](https://medium.com/@endubueze00/javascript-algorithm-dna-to-rna-conversion-cceb6abc1336) [## JavaScript 算法:DNA 到 RNA 的转换

### 我们编写一个函数，给定一个 DNA 字符串序列，并将其转换成 RNA 序列。

medium.com](https://medium.com/@endubueze00/javascript-algorithm-dna-to-rna-conversion-cceb6abc1336) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-hydration-count-9793a40e9a03) [## JavaScript 算法:水合计数

### 我们要写一个函数，返回一个叫阿奇的年轻人应该喝多少升水…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-hydration-count-9793a40e9a03)