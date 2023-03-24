# 使用这些风格提示编写更干净的 JavaScript

> 原文：<https://levelup.gitconnected.com/write-cleaner-javascript-with-these-style-tips-8de8fc61045a>

![](img/6a02655ed041b79e29fd91307c7941d0.png)

照片由 [Cookie 在](https://unsplash.com/@cookiethepom?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 Pom 拍摄

你做到了，你这个伟大的混蛋。你已经决定了。咬紧牙关。采纳了几乎所有科技 Twitter 的建议。

你，你这个光荣的生物，已经开始学习 JavaScript 了。

现在是时候升级你的代码了。

本文概述了三个风格技巧，用于编写更干净、更简洁的 JavaScript。更整洁的代码更容易阅读(因此也更容易调试)。花时间学习这些技巧将有助于以后节省时间。

让我们开始吧。

# 1.使用箭头函数表达式

via Giphy 和 AllWriteByMe

如果你正在读这篇文章，很可能你已经遇到了 JavaScript 函数。它们看起来像这样:

```
function myFunction(parameter) {
        statement;
}
```

好消息，朋友们:有一种更好的(更简洁的)方法来编写函数。

满足**箭头函数表达式。**

它的名字很拗口，但是相信我，箭头函数表达式是你的朋友。基本上，它用一个箭头(' = > ')代替了函数表达式。

一般格式如下:

```
(myParameter) => {
        statement;
}
```

## 为什么你应该关心

更干净的代码。简单明了。箭头函数表达式是常规函数格式的一个很好的替代形式——它在编码时可以节省时间，并且更容易阅读。它可能看起来不太像——这里的几个单词，那里的一个短语是什么？—但这是一种额外的风格，使您的代码更具可读性。

# 2.使用👏模板👏文字

很像 Python 中的 f-strings，JavaScript 模板文字直观、编写快捷，而且诚实？用起来挺有趣的。

模板文字看起来很像字符串，但是有一个主要的区别:可变性(即可编辑性。那是一个词，对吗？)他们更圆滑，适应性更强，看起来也更前卫。(我的意思是，认真地说——他们使用反斜线。很棒。)

模板文字看起来是这样的:

```
let stringName = 
`Hello ${userName}!`
```

## 为什么你应该关心

*Jazz hands*可重复使用！当用户和值改变时，模板文字让我们重新规划我们的代码。(类似“生命循环”的东西，把小狮子放在骄傲石上，等等。)

字符串是不可变的——一旦你创建了一个字符串，它的内容就不能改变。您可以制作一个新字符串，或者连接(组合)字符串，但原始字符串的内容不会改变。

模板文字的行为类似于字符串，但有一点不同:它们包含变量。当然，字符串是不可变的，但变量不是。这意味着同一行代码可以被无数用户和无数输入值重新使用。

```
**Example) Using template literals to display worked hours****//Create variables you want to use in your string**let employeeName = 'Buffy'
let hoursWorked = 40**//Create template literal** let myString = `${employeeName} worked ${hoursWorked} hours this week.`Output --> Buffy worked 40 hours this week.
```

# 3.使用地图方法

经由吉菲和 picmonkey.com

如果你想遍历一个数组，对它的所有内容求平方，然后创建一个新的数组呢？您可能会使用 for 循环，它可能看起来像这样:

```
**Creating a New Array Using a For Loop**
1    const oldArray = [1, 2, 3, 4, 5, 6, 7]
2    const newArray = []
3    for (let i = 0; i < oldArray.length; i++) {
4          newArray.push(myArray[i]*i)
5    }
```

但是坚持住。

有一种更简洁的方法可以做到这一点。

这就是[映射方法](https://www.freecodecamp.org/news/javascript-map-how-to-use-the-js-map-function-array-method/)的用武之地。

map 方法不需要 for 循环，也不需要第 2 行中的 const 语句。

下面是我们如何使用 map 方法生成相同的新数组:

```
**Creating a New Array Using the Map Method**
1\.    const oldArray = [1, 2, 3, 4, 5, 6, 7]
2\.    const newArray = oldArray.map(i => i * i)
```

## 为什么你应该关心

这两种方法产生相同的输出:一个新数组(创造性地命名为“new array”)，它包含新值:1、4、9、16、25、36 和 49。

区别？在第一个示例中，for 循环方法用两行代码实现了这一点，而 for 循环只用了五行代码。

map 方法、模板文字和箭头函数表达式都是编写简洁代码的好工具。如果您想了解更多信息，这里有一些来自 MDN Web Docs 的优秀资源:

[了解地图方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

[了解模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

[了解箭头函数表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

编码快乐！