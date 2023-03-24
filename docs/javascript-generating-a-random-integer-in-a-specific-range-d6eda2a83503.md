# JavaScript —生成特定范围内的随机整数

> 原文：<https://levelup.gitconnected.com/javascript-generating-a-random-integer-in-a-specific-range-d6eda2a83503>

这篇简明教程将向你展示如何用几行代码在 JavaScript 中生成一个随机的整数。

![](img/7ac00ae084e41c6b7febc6566bd2bf5e.png)

# GitHub 知识库

要检查使用的代码，请查看我为本文创建的 [GitHub 库](https://github.com/mr-pascal/medium-js-generate-whole-number)。

[](https://github.com/mr-pascal/medium-js-generate-whole-number) [## GitHub-Mr-Pascal/medium-js-generate-整数

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/mr-pascal/medium-js-generate-whole-number) 

# 代码

让我们直接进入代码，它将返回给你一个特定范围内的随机整数，然后深入探究它为什么工作。如果你只是对代码感兴趣，而不关心**为什么**你当然可以就此打住😉。

既然您似乎想知道发生了什么，让我们更详细地检查一下代码:

```
min = Math.ceil(min);
max = Math.floor(max);
```

如果你 100%确定你将总是得到一个整数而不是一个小数作为你的`min`和`max`值的输入，你不需要那两行。这些行确保在输入十进制数字的情况下，我们向上舍入(`min`大小写)或向下舍入(`max`大小写)以将数字范围的外部范围定义为整数而不是小数。

如果我们不这样做，而有人输入一个十进制值，我们也会得到一个十进制值作为输出，而不是一个整数。

**例如:**

`min = 1.1` → `min = 2`

`max = 7.7` → `max = 7`

结果范围→ `[2...7]`

现在让我们来看看代码中更复杂的一行:

`Math.floor(Math.random() * (max — min + 1)) + min;`

让我们从右边的`+ min`语句开始。这定义了我们范围左侧的最小值。我们从`min`开始，然后添加一些随机数直到`max`的最大值。

等式的`max — min`部分定义了我们的最小值和最大值之间的差异，因此，我们最多可以将该值添加到我们的`min`值中，以保持在该范围内。

由于`Math.random()`在`0`和`1`之间产生一个随机数，而`0`是包含的而`1`是排他的，我们给我们的`max — min`差加上一个额外的`+ 1`。理论上，这将导致一个比我们的最大值更高的值，对于`Math.random()`返回值。为了解决这个问题，我们使用`Math.floor()`将结果值向下舍入。

但是为了理解计算的细节，你最好看看下面的例子，在这些例子中，我展示了可能的`Math.random()`值的极值以及“正常”情况。

**示例**

让我们来看看一些例子，以验证数学工作。

以下示例在插入变量值后逐步解析上述最后一行代码。

```
min = 2;
max = 7;Math.floor(Math.random() * (7 - 2 + 1)) + 2
Math.floor(Math.random() * 6) + 2// Math.random() -> 0.7
Math.floor(0.7 * 6) + 2
Math.floor(4.2) + 2
4 + 2
--> 6 (inside our 2...7 range)// Math.random() -> 0.999
Math.floor(0.999 * 6) + 2
Math.floor(5.994) + 2
5 + 2
--> 7 (inside our 2...7 range)// Math.random() -> 0
Math.floor(0.0 * 6) + 2
Math.floor(0.0) + 2
0 + 2
--> 2 (inside our 2...7 range)
```

如你所见，即使有了`Math.random()`的最大值和最小值，结果也停留在有效值的范围内。

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上给我打电话。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)