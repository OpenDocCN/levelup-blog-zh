# 给每个 Web 开发者的 15 个神奇的 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/15-magical-javascript-tips-for-every-web-developer-3301feb0b70c>

## 15 个神奇的 JavaScript 技巧，节省您作为 Web 开发人员的宝贵时间

![](img/baf3a4bbbe5cb0e699186ae2639ab10e.png)

信用:[伦杰克](https://www.youtube.com/watch?v=_n8x8MhQo1k)

在本文中，我们将讨论 15 个有用的 JavaScript 技巧，帮助每个 web 开发人员节省宝贵的时间。

> 尽管我并不总是喜欢被教导，但我总是乐于学习
> 
> ——温斯顿·丘吉尔

# 提示 1。展平数组的数组

这个技巧将帮助你通过在`flat`中使用`Infinity`来展平一个深度嵌套的数组。

```
var array = [123, 500, [1, 2, [34, 56, 67, [234, 1245], 900]], 845, [30257]]//flatten array of array
array.flat(Infinity)// output:
// [123, 500, 1, 2, 34, 56, 67, 234, 1245, 900, 845, 30257]
```

# 技巧二。易交换变量

你可能会用第三个变量`temp`交换这两个变量。但是这篇技巧将向您展示一种使用析构来交换变量的新方法。

```
//example 1var a = 6;
var b = 7;[a,b] = [b,a]console.log(a,b) // 7 6
```

# 技巧三。按字母顺序排序

排序是编程中的一个常见问题，这个技巧将通过编写一个长代码来按字母顺序对字符串进行排序，从而节省您的宝贵时间。

```
//sort alphabeticallyfunction alphabetSort(arr)
{
  return arr.sort((a, b) => a.localeCompare(b));
}let array = ["d", "c", "b", "a"]
console.log(alphabetSort(array)) // ["a", "b", "c", "d"]
```

# 技巧四。生成数字范围

假设您想要生成一个特定范围内的数字。您将使用的第一种方法是循环。但是这个提示将通过简单的方法为您节省宝贵的时间。

```
let Start = 1000, End = 1500;
//Generating
[...new Array(End + 1).keys()].slice(Start);Array.from({length: End - Start + 1}, (_,i) => Start + i) // [1000, 1001 .... 1500]
```

# 技巧五。缩短控制台日志

厌倦了一遍又一遍的写`console.log()`？这篇技巧将展示如何缩短您的控制台日志并加速您的编码。

```
var c = console.log.bind(document)
c(455)
c("hello world")
```

# 提示 6。以简单的方式缩短数组

对于 web 开发人员来说，这是一个很棒的技巧，可以用一种简单的方式缩短数组。你只需要使用`length`方法，传递一个表示数组新大小的数字。

```
let array = ["A", "B", "C", "D", "E", "F"]array.length=2
console.log(array) // ["A", "B"]
```

# 技巧 7。使用 isNumber

这个技巧将展示如何检查一个值或变量是否包含一个数字(整数、浮点数等)。

```
function isNumber(n) { return !isNaN(parseFloat(n)) && isFinite(n); }console.log(isNumber(900))  // true
console.log(isNumber(23.98))  // true
console.log(isNumber("JavaScript")) // false
```

# 技巧八。使用 isString

这个有用的技巧将向您展示如何检查一个值或数据是否是字符串格式。当您从服务器请求数据并想要检查数据类型时，这很方便。

```
const isString = value => typeof value === 'string';isString('JavaScript'); // true
isString(345); // false
isString(true); // false
```

# 技巧九。检查空值

在编程中，有时我们需要检查一个结果或数据是否为空。

```
const CheckNull= value => value === null || value === undefined;CheckNull(null) // true
CheckNull() // true
CheckNull(123) // false
CheckNull("J") // false
```

# 提示 10。将数组合并成一个

当您需要将任意大小的两个数组合并成一个数组时，这个技巧会很有用。为此，您需要使用 JavaScript `concate`方法。

```
//Example Code
let arr1 = ["JavaScript", "Python", "C++"]
let arr2 = ["Dart", "Java", "C#"]const mergeArr = arr1.concat(arr2)
console.log(mergeArr) // ["JavaScript", "Python", "C++", "Dart", "Java", "C#"]
```

# 提示 11。快速计算性能

这个技巧是我个人最常用来计算我的 JavaScript 程序性能的。

```
const starttime = performance.now();
//some program
const endtime = performance.now();const totaltime = startTime - endTime
console.log("function takes "+totaltime +" milisecond");
```

# 提示 12。删除重复项

您可能会遇到具有重复数据的数组，并使用循环方式来消除这些重复数据。这个技巧将帮助你在不使用任何循环的情况下以一种简单的方式删除重复项。

```
const ReDuplicates = array => [...new Set(array)];console.log(ReDuplicates([200,200,300,300,400,500,600,600])) // [200,300,400,600]
```

# 提示 13。将数字转换成二进制

当您需要将数字转换成二进制时，这个技巧很有用。看看下面的示例代码。

```
var num = 200
console.log(num.toString(2)) // 11001000num = 300
console.log(num.toString(2)) //100101100
```

# 提示 14。字符“e”代表过多的零

这是一个为你节省时间的提示。通过用字符“e”替换所有的零，可以避免在 JavaScript 中编写大量的零。

```
//normal way
var num = 20000000//good way
var num2 = 2e7
console.log(num2) //20000000
```

# 提示 15。使数组为空

这个小技巧将节省你清空数组的时间。我将向您展示使用 JavaScript length 方法清空数组的快速方法。

```
let array = ["A", "B", "C", "D", "E", "F"]array.length=0
console.log(array) // []
```

# 最后的想法

我希望你能发现这篇文章对学习这些技巧有用而且有趣。如果你有好的 JavaScript 技巧，请随意与其他开发者分享。**快乐的 JavaScript 编码。**

如果你觉得这篇文章有帮助，请点击下面的❤️按钮，与你的朋友分享这篇文章。

图片来源:感谢伦杰克检查他的 [**视频**](https://www.youtube.com/watch?v=_n8x8MhQo1k) 也

# 了解更多信息

想要更多的编程剂量。看看我的另一篇编程文章，希望你会觉得读起来有趣。

[](/12-python-tricks-to-make-your-life-easier-b4a88e4c6767) [## 让你的生活更轻松的 12 个 Python 技巧

### 节省您宝贵时间的 Python 技巧和窍门

levelup.gitconnected.com](/12-python-tricks-to-make-your-life-easier-b4a88e4c6767) [](/12-smart-ways-to-earn-as-a-developer-4131def3b0a5) [## 作为开发人员的 12 种聪明的赚钱方法

### 除非你能在床上赚钱，否则不要呆在床上

levelup.gitconnected.com](/12-smart-ways-to-earn-as-a-developer-4131def3b0a5) [](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [## 使用 NLTK 的 Python 自然语言处理初学者指南

### 自然语言处理是人工智能的一个分支，它帮助计算机理解自然语言

levelup.gitconnected.com](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [](/12-javascript-features-youve-probably-never-used-db932c413cdd) [## 您可能从未使用过的 12 个 JavaScript 特性

### 大多数人不知道 JavaScript 令人难以置信的特性

levelup.gitconnected.com](/12-javascript-features-youve-probably-never-used-db932c413cdd) [](/17-clever-javascript-tricks-that-every-developer-should-use-e7f299e49896) [## 每个开发人员都应该使用的 17 个聪明的 JavaScript 技巧

### 每个开发人员都应该知道的 JavaScript 技巧

levelup.gitconnected.com](/17-clever-javascript-tricks-that-every-developer-should-use-e7f299e49896) [](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [## 掌握 Python 的面向对象编程(OOP)

### 通过掌握面向对象编程(OOP ),学习用 Python 编写更简洁、更模块化的代码。

levelup.gitconnected.com](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [## PyQt5 教程:用 Python 和 PyQt5 学习 GUI 编程

### Pyqt5 是图形用户界面小部件工具包。它是最强大和最流行的 python 接口之一…

levelup.gitconnected.com](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [](/build-a-desktop-app-with-python-4a847e3b596c) [## 用 Tkinter 和 Python 构建桌面应用程序

### 在本文中，我们将学习如何使用 python 和 Tkinter 模块开发现代桌面应用程序。一个…

levelup.gitconnected.com](/build-a-desktop-app-with-python-4a847e3b596c) [](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/how-to-crawl-a-web-page-with-scrapy-3b9d12797813) [## 如何用 Scrapy 抓取网页

### Scrapy 是最流行和最强大的 Pythons 刮库之一；它采用“包含电池”的方法来…

levelup.gitconnected.com](/how-to-crawl-a-web-page-with-scrapy-3b9d12797813) [](/sending-email-with-python-c6bdc9a07cb5) [## 使用 Python 发送电子邮件

### 在今天的世界里，电子邮件是我们生活的一部分，无论是商业、学校、大学，还是发送电子邮件…

levelup.gitconnected.com](/sending-email-with-python-c6bdc9a07cb5)