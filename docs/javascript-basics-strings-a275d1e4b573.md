# JavaScript 基础:字符串

> 原文：<https://levelup.gitconnected.com/javascript-basics-strings-a275d1e4b573>

## JavaScript 中的字符串

![](img/2c80cf91e4e7cdbfe6ba986310f8e0dc.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Surendran MP](https://unsplash.com/@sure_mp?utm_source=medium&utm_medium=referral) 拍摄

在 JavaScript 中，字符串用于表示文本。要在 JavaScript 中编写字符串，请将字符串括在以下引号中:

```
'The ocean is brown.'
"The ocean is brown."
`The ocean is brown.`
```

只要第一个和最后一个引号匹配，就可以使用任何一个引号。

任何内容都可以放在引号内。你甚至可以在引号内放一个数学等式，但是要知道 JavaScript 不会解决这个数学问题。它只会按原样输出等式。

```
"2 + 4" // will output 2 + 4
2 + 4 // will output 6
```

您可以在两个字符串之间放置一个`+`,但它只会将它们连接起来或将两个字符串放在一起。

```
"2 + 4"
"2" + " + " + 4
```

在上面的例子中，第一个和第二个例子是同样的东西，但是书写方式不同。第二个示例将输出第一个示例。如果你想解一个数学方程，同时把它保存在一个字符串中，你可以使用[模板文字](https://endubueze00.medium.com/javascript-basics-string-concatenation-with-variables-and-interpolation-deba239debbe):

```
`5 plus 5 equals ${5 + 5}`
```

上面的示例将输出:

```
5 plus 5 equals 10
```

简而言之，您在`${}`中包含的任何东西，无论是数学语句还是变量，JavaScript 都会输出值并将其转换为字符串。

要在字符串中插入新行，可以使用转义字符。通过在字符后插入反斜杠`\`来使用转义字符。反斜杠后面的字符有一个特殊的含义，JavaScript 读起来和其他任何字符都不一样。

要创建一个新行，在反斜杠后面，您将添加一个`n`。

```
"This is the first line of text**\n**And this is the second line."
```

一旦 JavaScript 看到了`\n`，它将看到`n`字符并在该文本后创建一个新行。即使它们在不同的线中，线也没有分开或结束。他们仍然是一个字符串。

上面的文本将输出为:

```
This the first line of text
And this is the second line.
```

如果您想阅读更多的 JavaScript 基础知识文章，可以看看这些文章:

[](https://javascript.plainenglish.io/javascript-basics-numbers-352d49a61335) [## JavaScript 基础:数字

### JavaScript 中的数字

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-basics-numbers-352d49a61335) [](https://codeburst.io/javascript-basics-default-parameters-and-return-statements-e5f2f07daf6a) [## JavaScript 基础知识:默认参数和返回语句

### 在我们上一篇 JavaScript 基础文章中，我们讨论了如何使用 JavaScript 函数。

codeburst.io](https://codeburst.io/javascript-basics-default-parameters-and-return-statements-e5f2f07daf6a) [](https://javascript.plainenglish.io/javascript-basics-functions-44bee1c31846) [## JavaScript 基础:函数

### 在编程中，反复做一个任务是很常见的。假设您想要将一系列数字相加:

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-basics-functions-44bee1c31846)