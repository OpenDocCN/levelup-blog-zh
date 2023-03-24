# JavaScript 中的自动类型转换

> 原文：<https://levelup.gitconnected.com/automatic-type-conversion-in-javascript-7a23503979a0>

## JavaScript 如何自我调整以使你的程序能够运行(如果运行的话)

![](img/bcfcbc0f2a6b61e8cf51d7de84cfca27.png)

由[克里斯蒂安·斯特兰德](https://unsplash.com/@kristianstrand?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你可以在你的程序中加入奇怪的计算，JavaScript 会接受它。

```
console.log(8 * null) // 0
console.log("5" - 1) // 4
console.log( "5" + 1) // "51"
console.log("five" * 2) // NaN
```

当运算符被不正确的类型使用时，JavaScript 会将该值转换为计算所需的类型。这叫做*型强制。*类型强制会导致意想不到的结果。

一个例子是当你用字符串做数学时。

```
console.log("5" + 2) // "52" not 7
```

JavaScript 会假设你指的是连接而不是数字相加，它会将整数转换成字符串并将两个值粘合在一起。

如果 JavaScript 不知道把值改成什么类型，就会导致`NaN`。

使用等号运算符时，很容易预测如果两个值相同，输出将为真，但是让我们仔细检查一下:

```
console.log(0 == false) // true
console.log("" == false) // true
```

尽管两个比较的值不同，但由于类型强制，上述两个示例都是正确的。JavaScript 悄悄把`0`和`""`的类型改成布尔 and 输出`true`。如果你希望被比较的项目完全相等，你可以使用`===`。

现在当使用三个相等的比较运算符时，上面的语句将输出`false`:

```
console.log(0 === false) // false
console.log("" === false) // false
```

为了避免任何意外的类型强制，最好使用`===`操作符。

对于`&&`和`||`操作符，有一种东西叫做短路操作。根据操作符和输出，它将只评估右侧或左侧的内容。让我们仔细看看。

使用`||`运算符，JavaScript 将尝试将运算符左侧的值转换为布尔值。如果 JavaScript 似乎无法将左边的值更改为真值，它将默认为右边的其他值。如果两者都为真，它将默认为左边的原始值。

以下是一些例子:

```
console.log(null || "user") // "user"
console.log("Agnes" || "user")
```

您可以使用它来创建默认值。您将获得初始值(左边的值)和替换值(右边的值)。如果其中一个值是空字符串或其他假值，JavaScript 可以返回到第二个值。如果初始值和替换值都为假，JavaScript 将输出替换值。

与`&&`操作符相同，但方向相反。JavaScript 将输出可变为 false 的值，而不是输出 true 的值。如果它们都是真值，那么它将输出替换值。

如果你想阅读更多的 JavaScript 基础文章，你可以看看我最近的文章:

[](https://javascript.plainenglish.io/javascript-basics-functions-44bee1c31846) [## JavaScript 基础:函数

### 在编程中，反复做一个任务是很常见的。假设您想要将一系列数字相加:

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-basics-functions-44bee1c31846) [](/javascript-basics-switch-statement-12f8c6082326) [## JavaScript 基础:Switch 语句

### 当编写 if 语句时，您可能希望包含几个其他 if 语句，但是如果您希望包含一个…

levelup.gitconnected.com](/javascript-basics-switch-statement-12f8c6082326) [](/javascript-basics-ternary-operator-c5e1510c6d2) [## JavaScript 基础:三元运算符

### 在编写 if 语句时，也有一种使用三元运算符来缩短它的方法。

levelup.gitconnected.com](/javascript-basics-ternary-operator-c5e1510c6d2) 

# 分级编码

感谢您成为我们社区的一员！更多内容请参见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随: [Twitter](https://twitter.com/gitconnected) ， [LinkedIn](https://www.linkedin.com/company/gitconnected) ，[迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)