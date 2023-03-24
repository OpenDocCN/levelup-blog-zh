# JavaScript 算法:简单的字符串反转

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-simple-string-reversal-df44d83c9a5a>

## 反转两个给定索引之间的字符串部分

![](img/0da23be985ceb5264e5a208e5cafba69.png)

[蒙蒂·艾伦](https://unsplash.com/@monty_a?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们将编写一个名为`solve`的函数，它将接受一个字符串`st`和两个整数`a`和`b`作为参数。

给你一个字符串和两个索引。该函数的目标是仅反转这些索引之间(包括这些索引)的字符串部分，并返回结果字符串。

示例:

```
solve("codewars",1,5) //output: "cawedors"
```

在上面的例子中，我们取了字符串，只颠倒了从字符 1 到(包括)5 的部分。

```
"codewars""c"   "**odewa**"    "rs""c"   "**awedo**"    "rs""cawedors"
```

在反转子串之后，字符串被放回一起并返回。

让我们开始写函数。

首先，我们要将字符串转换成数组。我们把这个阵列分配给`strArr`。

```
let strArr = st.split("");
```

接下来，我们将把数组分成三个部分。第一部分将保存起始索引前面的子字符串，`a`。如果你看看上面例子中的字符串，那就是字母“c”

我们将这部分字符串赋给一个名为`start`的变量。

```
let start = strArr.slice(0, a);
```

第二部分将保存结束索引之后的子字符串`b`。如果你看看上面例子中的字符串，那就是“rs”。

我们将该子串分配给`end`。

```
let end = strArr.slice(b+1);
```

第三部分是我们要反转的子串。使用`slice()`和`reverse()`方法的组合，我们将从数组中提取子串并反转它。

我们将反转子串分配给`reverseSection`。

```
let reverseSection = strArr.slice(a,b+1).reverse();
```

最后，我们使用`concat()`方法连接数组`start`、`reverseSection`和`end`。我们还在最后使用`join()`将数组改回字符串。

我们归还它。

```
return start.concat(reverseSection.concat(end)).join("");
```

下面是完整的函数:

如果您觉得这个算法有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://javascript.plainenglish.io/javascript-algorithm-equal-sides-of-an-array-e4e06055bf4a) [## JavaScript 算法:数组的等边

### 返回一个数组索引，其中数组左边索引的和等于右边索引的和

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-equal-sides-of-an-array-e4e06055bf4a) [](/javascript-algorithm-categorize-new-member-8cb175a469) [## JavaScript 算法:对新成员进行分类

### 创建一个函数，根据数组中的数据对门球俱乐部的新成员进行分类

levelup.gitconnected.com](/javascript-algorithm-categorize-new-member-8cb175a469) [](https://javascript.plainenglish.io/javascript-algorithm-counting-sheep-152be7d78da9) [## JavaScript 算法:数羊

### 数一排绵羊的数量

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-counting-sheep-152be7d78da9)