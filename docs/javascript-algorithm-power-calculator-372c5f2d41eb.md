# JavaScript 算法:功率计算器

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-power-calculator-372c5f2d41eb>

## 使用给定的电压和电流计算电量的功能。

![](img/89a8d163dc16499fb134c9f421de1552.png)

托马斯·凯利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今天，我们将编写一个名为`circuitPower`的函数，它将接受两个整数`voltage`和`current`作为参数。

该函数的目标是使用`voltage`和`current`的值来计算功率。该功能需要基本的电路知识。好消息是要计算功率，你需要用电压乘以电流。该函数将输出计算结果。

```
Power = Current x Voltage
```

以下是一些例子:

```
circuitPower(230, 10) ➞ 2300
circuitPower(110, 3) ➞ 330
circuitPower(480, 20) ➞ 9600
```

如果将两个给定的输入相乘，该函数将按照箭头输出您看到的结果。

那还不错。让我们把这变成代码。

因为我们已经知道如何使用电压和电流计算功率，我们将这样做并返回结果。

您可以将该等式赋给一个名为`power`的变量，然后返回`power`或者您可以只返回该等式。

```
return voltage * current;
```

下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-finders-keepers-87c132354c53) [## JavaScript 算法:发现者拥有者

### 我们编写一个函数，它将返回给定数组中通过另一个函数的真值测试的第一个元素

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-finders-keepers-87c132354c53) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-vowel-remover-c8808868ee55) [## JavaScript 算法:元音去除器

### 我们将创建一个函数来删除字符串中的所有元音

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-vowel-remover-c8808868ee55) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-does-my-list-include-this-8a946b2c7839) [## JavaScript 算法:我的列表包括这个吗？

### 我们将编写一个函数来确定数组中是否存在某个值。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-does-my-list-include-this-8a946b2c7839)