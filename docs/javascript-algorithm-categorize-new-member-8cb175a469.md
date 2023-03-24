# JavaScript 算法:对新成员进行分类

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-categorize-new-member-8cb175a469>

## 创建一个函数，根据数组中的数据对门球俱乐部的新成员进行分类

![](img/d26477f9410a53d0c2f400db06187c1b.png)

照片由[阿兰·米勒](https://unsplash.com/@pan2000?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们将编写一个名为`openOrSenior`的函数，它接受一个二维数组`data`作为参数。

西郊门球俱乐部有两类会员，高级会员和公开会员。给你一个数组，每个元素包含两个数字。第一个数字是成员的年龄。第二个数字是他们的障碍。

要成为老年人，该成员必须至少 55 岁，并且残疾程度大于 7。

在评估了来自每个数组元素的两个数字之后，该函数的目标是确定每个成员是“开放”成员还是“高级”成员。将每个人的成员状态输出到一个新数组中。

示例:

```
[[18, 20],[45, 2],[61, 12],[37, 6],[21, 21],[78, 9]]Output: ["Open", "Open", "Senior", "Open", "Open", "Senior"]
```

在第一个数组中，您将看到第一个成员小于 55 岁，仅此一项就将此人归入`Open`类别。但是你看第三个成员，`[61,12]`，那个人已经 55 岁以上了**，**残障超过 7。因此，他们将归入`Senior`一类。

我强调了“和”，因为如果这个人超过了 55 岁**，但**的残疾小于 7，你会把他们归入`Open`类别。为了进入`Senior`类别，他们必须满足年龄和残障最低要求。

为了开始这个函数，我们将使用来自数组对象的`map()`方法。此方法创建一个新数组，其中填充了对调用数组中的每个元素调用所提供函数的结果。

我们将编写一个名为`determineMembership`的函数，我们希望`map()`方法在数组内部使用它。

```
function determineMembership(member){    
    return (member[0] >= 55 && member[1] > 7) ? 'Senior' : 'Open';  }
```

在数组内部，我们检查每个数组元素的第一个和第二个值，并根据我上面提到的标准输出`"Senior"`或`"Open"`。

写完函数后，我们将它应用于在我们的`data`数组中使用的`map`方法，并返回我们的新数组。

```
return data.map(determineMembership);
```

下面是完整的函数:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://javascript.plainenglish.io/javascript-algorithm-counting-sheep-152be7d78da9) [## JavaScript 算法:数羊

### 数一排绵羊的数量

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-counting-sheep-152be7d78da9) [](/javascript-algorithm-array-differences-dc0f012b307a) [## JavaScript 算法:数组差异

### 将两个数组之间的差异放到另一个数组中。

levelup.gitconnected.com](/javascript-algorithm-array-differences-dc0f012b307a) [](https://javascript.plainenglish.io/javascript-algorithm-convert-hours-into-seconds-c2252ad3c171) [## JavaScript 算法:将小时转换成秒

### 将小时转换成秒的函数。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-convert-hours-into-seconds-c2252ad3c171)