# 每个开发人员都应该使用的 17 个聪明的 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/17-clever-javascript-tricks-that-every-developer-should-use-e7f299e49896>

## 每个开发人员都应该知道的 JavaScript 技巧

![](img/9cefa4e9af7f545df694c3b483f47941.png)

由[凯尔·史密斯](https://unsplash.com/@roller1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@roller1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

> 在 JavaScript 中，有一种美丽、优雅、极具表现力的语言，它被掩盖在一大堆善意和错误之下。
> —道格拉斯·克洛克福特

# 👉1 #—删除重复项(专业方式)

您通常使用循环来迭代整个数组，以删除其中的重复项。但是我将向您展示通过编写更少的代码来完成相同工作的专业方法。

```
let array = [100, 200, 200, 120, 238, 201, 201]
let newArray = Array.from(new Set(array));console.log(newArray) // [ 100, 200, 120, 238, 201 ]
```

# 👉2# —数组的最后一个元素

当您需要获取数组的最后一个元素时，slice 方法在 JavaScript 中非常有用。只需将负数作为参数传递，它将从最后一个索引开始分割数组。

切片方法的最大好处是它不会影响您的原始数组。

```
let array = [1, 3, 4, 5, 6, 9, 10, 12]console.log(array.slice(-1)) // [12]
console.log(array.slice(-2)) // [10, 12]
console.log(array.slice(-4)) // [6, 9 ,10, 12]
```

# 👉3# —带有“减少”的数组的平均值

Reduce 方法是遍历数组并获得单个输出的一种非常好的方式。使用 Reduce 方法的一个例子是获取数组中值的平均值。

```
let array = [100, 120, 150, 101, 301]let sum = array.reduce((previous, current)=> current += previous)let average = sum / array.lengthconsole.log(average) // 154.4
```

首先，我们用值声明一个数组，然后我们用 Reduce 方法计算所有值的和，最后，我们用和除以数组的长度得到平均值。

# 👉4 #-具有唯一值的数组

拥有一个重复的数组对我们来说总是一个麻烦。在 JavaScript 中，您可能会使用一个**映射**或**过滤器**来清除数组中的重复值。但是我们有另一个选择，那就是**设置对象**。看看下面的例子。

```
let array = [1, 3, 3, 1, 2, 4, 5, 6 ,5, 2]const uniqueArray = [...new Set(array)]console.log(uniqueArray) // [1, 3, 2, 4, 5, 6]
```

# 👉5# —缩短数组

假设你有一个包含 100 项的数组，你想缩短它。在 JavaScript 中，您可能会使用**拼接方法**，但是我将向您展示另一种截断数组的方法。

```
let array = [100, 200, 300, 400, 500, 600, 700]//shortening your array
array.length = 4console.log(array) // [100, 200, 300, 400]
```

# 👉6# —用扩散方法组合对象

假设您想将多个对象合并成一个。在 JavaScript 中，您可以使用 Spread 方法，这是一种很好的方法。在下面的示例代码中，我使用 spread 方法组合了两个对象。

```
let object1 = {'a' : 1, 'b' : 2, 'c': 3}
let object2 = {'d' : 4, 'e' : 5}//combining the objectsconst combine = {...object1 , ...object2}
console.log(combine) //  {'a' : 1, 'b' : 2, 'c': 3, 'd' : 4, 'e' : 5}
```

# 👉7# —在数组中查找索引

假设你必须在一个数组中找到一个特定元素的索引。您可能会为此使用`findindex()`方法。但是在这篇技巧文章中，我将向您展示另一种以更有效的方式完成同样工作的方法。

```
let array = [1, 4, 78, 90, 23, 123, 100, 230]//IndexOf Method
console.log(array.indexOf(123)) // 5
console.log(array.indexOf(23)) // 4
console.log(array.indexOf(1)) // 0
```

# 👉8# —展平阵列

你有一个大的二维数组，想把它展平成一个一维数组吗？这个提示可能会对你有所帮助。查看下面的示例代码。

```
let array = [1, 2, [3, 4], [5, 6, 7, ]];
const newArray = array.flat()console.log(newArray) // [1, 2, 3, 4, 5, 6, 7]
```

# 👉9# —以一种好的方式交换价值

我敢打赌，你们中许多新的 JavaScript 程序员或任何语言程序员都使用过变量`temp`在两个值之间交换。但是我将向您展示如何在不使用临时变量或第三方变量的情况下交换它们。

```
let a = 5;
let b = 7;
console.log(a, b); // 5 7[a, b] = [b, a];console.log(a, b); // 7 5
```

# 👉10# —用随机范围内的值填充数组

```
const RandomIntArray = (min, max, n = 1) => Array.from({ length: n }, () => Math.floor(Math.random() * (max - min + 1)) + min)RandomIntArray(5, 20, 4) // [ 8, 17, 13, 9]
```

# 👉11# —组合两个阵列

假设你有两个数组，你想把它们组合起来。这个提示将帮助你快速做到这一点。

```
let array1 = [1, 2, 3, 4, 5, 6, 7];
let array2 = [8, 9, 10, 11, 12];const merge = array1.concat(array2)console.log(merge) // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
```

# 👉12# —获取对象的类型

这个提示将帮助你了解任何物体的类型。假设你用 JavaScript 从网站上请求数据，你想知道你收到的数据是什么类型，是字符串还是整数，等等。

```
const GetType = v => v === undefined ? 'undefined' : v === null ? 'null' :   v.constructor.name.toLowerCase();console.log(GetType("Programming")) // String
console.log(GetType(123)) //Integer
```

# 👉13# —当前 Url

这个技巧将帮助您获得 Javascript 正在运行的当前 URL。假设您想要检查您当前看到的网页的 URL，那么您可以使用下面的代码。

```
let GetCurrentURL = () => window.location.href;GetCurrentURL() //[https://medium.com/](https://medium.com/)
```

# 👉14 #—计算数组中的出现次数

假设你有一个大的重复值数组，你想计算一个特定元素的出现次数。

```
const CountOcc = (array, val) => array.reduce((x, v) => (v === val ? x + 1 : x), 0);console.log(CountOcc([3, 3, 4, 1, 2, 5, 3, 6],3)) // 3
console.log(CountOcc([3, 4, 4, 1, 3, 6],4)) // 2
```

# 👉15# —错误处理

错误是每一种编程语言的痛苦。这个技巧将帮助您处理 JavaScript 编程中出现的错误。

```
try {
  try condition
}
catch (exception_var) {
  catch condition
}
```

# 👉16 #—修剪空白区域

这个技巧将指导你使用正则表达式来修剪字符串中的空白。当您有原始数据并且想要删除空白时，这个技巧会很有用。

```
let string = " a b    cd   e   ";
let Trim = string.replace(/\s+/g, '');console.log(Trim)
```

# 👉17 #—数字化整数

这个技巧将向你展示如何将任意整数转换成数组格式。查看下面的代码示例。

```
const DigitizeInt = n => [...`${n}`].map(i => parseInt(i));DigitizeInt(4560) // [4, 5, 6, 0]
DigitizeInt(131) // [1,3,1]
```

# 最后的想法

我希望你觉得这篇文章有用而且有趣。我主要在 stackoverflow 和 Quora 网站上寻找新的 JavaScript 技巧。请随意分享 JavaScript 的技巧和诀窍。与世界分享你的知识。

如果你觉得这篇文章有帮助，请点击下面的❤️按钮，与你的朋友分享这篇文章。

# 了解更多信息

如果您喜欢这篇文章，那么您应该看看我的其他编程文章。

[](/12-javascript-features-youve-probably-never-used-db932c413cdd) [## 您可能从未使用过的 12 个 JavaScript 特性

### 大多数人不知道 JavaScript 令人难以置信的特性

levelup.gitconnected.com](/12-javascript-features-youve-probably-never-used-db932c413cdd) [](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [## 针对日常问题的 25 个有用的 Python 片段

### 以下是我为您的日常 Python 问题提供的 25 个有用且省时的片段

levelup.gitconnected.com](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [## 20 个必要的代码片段，让你在 JavaScript 中像专家一样工作

### 你可以在 30 秒或更短时间内学会 20 个 JavaScript 代码片段

levelup.gitconnected.com](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [## 学习编码的同时在线赚钱的 20 种方法

### 如果你是一名程序员，却没有在网上赚到钱，那你就错过了一个大好机会

levelup.gitconnected.com](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [## 使用 NLTK 的 Python 自然语言处理初学者指南

### 自然语言处理是人工智能的一个分支，它帮助计算机理解自然语言

levelup.gitconnected.com](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [## 掌握 Python 的面向对象编程(OOP)

### 通过掌握面向对象编程(OOP ),学习用 Python 编写更简洁、更模块化的代码。

levelup.gitconnected.com](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [](/build-a-desktop-app-with-python-4a847e3b596c) [## 用 Tkinter 和 Python 构建桌面应用程序

### 在本文中，我们将学习如何使用 python 和 Tkinter 模块开发现代桌面应用程序。一个…

levelup.gitconnected.com](/build-a-desktop-app-with-python-4a847e3b596c) [](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [## 如何让你的 python 代码运行速度提高 10 倍

### 让您的 python 代码运行速度提高 10 倍的简单提示和指南

levelup.gitconnected.com](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa)