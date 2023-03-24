# 20 个必要的代码片段，让你在 JavaScript 中像专家一样工作

> 原文：<https://levelup.gitconnected.com/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc>

![](img/fdb1e2cf9ed5b7142e2dd336f5c17ccf.png)

照片由[埃德加·恰帕罗](https://unsplash.com/@echaparro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@echaparro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在这篇文章中，我将分享 22 个解决实际问题的 JavaScript 片段——这些问题你可能会在日常生活编码中遇到。

## #1 —对数组进行排序

当您希望通过使用 JavaScipt 内置的`sort`方法快速对数组进行排序而不需要编写很长的排序函数时，这个代码片段非常有用。

```
const months = ['March', 'Jan', 'Feb', 'Dec'];
const numbers = [5, 7, 1, 0, 13, 10]
months.sort();
numbers.sort();console.log(months);  //["Dec", "Feb", "Jan", "March"]
console.log(numbers); //[0, 1, 10, 13, 5, 7]
```

## #2—计数出现次数

这个有用的代码片段将计算数组中每个元素的出现次数。当您需要知道每个项目在数组中出现的次数时，这很方便。

```
const Occurrences = (arr, val) => arr.reduce((a, v) => (v === val ? a + 1 : a), 0);console.log(Occurrences([1, 4, 5, 5, 1, 3], 1))  // 2
console.log(Occurrences([2, 5, 5, 6, 7, 6, 6], 6)) // 3
```

## #3—反转字符串

我将使用下面的代码片段向您展示在 JavaScript 中反转字符串的简单方法。

```
const ReverseString = str => [...str].reverse().join('');console.log(ReverseString("JavaScript"))  // tpircSavaJ
```

## #4 —从列表中随机选择

当我们需要从一长串数字中获取一个随机数时，这个代码片段会派上用场。

```
const randomNum = array => array[Math.floor(Math.random() * array.length)];console.log(randomNum([5, 6, 10, 23, 1])) // 23
console.log(randomNum([3, 5, 7, 1, 2])) // 7
```

## #5 —阵列的最小数量

每天我们都要解决一个问题，我们需要在大数组或小数组中找到最小的。为了在不使用任何循环的情况下以简单快速的方式做到这一点，我在下面展示了一个代码示例。

```
const minimumNum = (arr, n = 1) => [...arr].sort((a, b) => a - b).slice(0, n);console.log(minimumNum([4, 3, 2, 1, 0]))   // 1
console.log(minimumNum([12, 34, 12, 5, 7]))  //
```

## #6 —数量最多的阵列

这个代码片段有助于在短时间内找到数组中的最大数值，而无需使用额外的循环。

```
const LargestNum = (arr, n = 1) => [...arr].sort((a, b) => b - a).slice(0, n);LargestNum([12, 34, 78]) // 78
LargestNum([1, 4, 5, 3, 2]) // 5
LargestNum([2, 0, 3]) // 3
```

## #7 —转换为数组

这个代码片段将 JavaScript 中的不同数据类型转换为数组格式。当您需要将一个字符串转换成一个数组以对其应用不同的函数时，这很方便。

```
const ConvertToArray = val => (Array.isArray(val) ? val : [val]);console.log(ConvertToArray("JavaScript")) // [JavaScript]
console.log(ConvertToArray(1000)) // [1000]
console.log(ConvertToArray(true)) // [true]
```

## #8 —字节大小

你总是需要计算任何字符串或整数的大小。

```
const byteSize1 = str => new Blob([str]).size;
const byteSize2 = int => new Blob([int]).size;byteSize1("Coding") // 6
byteSize2(1234) // 4
byteSize2('12344') // 5
```

## #9 —数字化整数

这段代码对于将整数转换成数组格式很有用。简单地说，你可以将一个整数中的每个数字分成一个数组索引**例如** **301 → [3，0，1]**

```
const Digitize = n => [...`${n}`].map(i => parseInt(i));console.log(Digitize(576)) // [5,7,6]
console.log(Digitize(89)) // [8,9
console.log(Digitize(8)) // [6]
```

## # 10—检查空值

假设您正在处理一个 web 后端，并且您正在创建一个检查空值的条件，那么这段代码将会派上用场。

```
const checkNull = val => val === undefined || val === null;checkNull() // true
checkNull(835) // false
checkNull("JavaScript") // false
```

## #11 —删除列表中的重复项

从标题中的这段代码片段你就知道我们将从列表中删除重复项。

```
const RemoveDupli = arr => [...new Set(arr)];console.log(RemoveDupli([2, 2, 2, 5, 1, 0, 2])) // [2,5,1,0]
console.log(RemoveDupli([9, 6, 7, 8, 8, 6]))  // [9,6,7,8]
```

## # 12—交换值

这段代码将展示如何在不创建第三个变量(temp)的情况下以更快的方式交换值。查看下面的代码示例。

```
let a = 5, b = 9;
[a,b] = [b,a];
console.log(a, b)  // 9 5
```

## #13 —错误处理

有时，我们面临的错误来得异乎寻常，难以察觉，因为它们是突然的错误。如果你在一个 API 上工作，有时你会得到 404 错误响应，在这种情况下你可以使用 JavaScript 的错误处理语句。

```
try {
  try_statements
}
catch (exception_var) {
  catch_statements
}
finally {
  finally_statements
}
```

## #14 —合并数组

这段代码将帮助您将两个数组合并成一个数组。这是一种简单快速的方法，不需要迭代每个元素，把它们放在一个新的数组中。

```
const lang1 = ["JavaScript", "Python", "C++"];
const lang2 = ["C#", "Dart", "Java"];const merge = [...lang1, ...lang2];
console.log(merge); // [""JavaScript", "Python", "C++", "C#", "Dart", "Java"]
```

## # 15—清空数组

当你需要在短时间内清空一个数组时，这段代码会很有帮助。检查下面的代码，知道如何做。

```
var array = ["Haider", "Jeff", "Jessica", "Tadashi"];array.length = 0; //making size zero
console.log(array) // [] array is now empty
```

## # 16—缩短数组

你知道有一个简单的方法来缩短你的数组吗，这段代码将通过一个示例代码向你展示如何做到这一点。

```
var items = [4, 5, 7, 8, 2, 1, 3, 6];items.length = 3
console.log(items) //[4, 5, 7]
```

## #17—数据类型转换

这段代码只是通过使用内置的 JavaScript 方法将字符串转换成整数，并将整数转换成字符串。

```
// converting string to number
let string2 = "903"
let num = Number(string2)// converting number to string
let num = 49
let string1 = num.toString();
```

## #18—性能

有时您需要知道代码完成执行需要多少时间。查看下面的代码示例。

```
const startTime = performance.now();something();const endTime = performance.now();
console.log("Cpde take ${startTime - endTime} milliseconds")
```

## #19 —替换字符串中的字符

这段代码使用正则表达式在短时间内替换字符串中的特定字符。

```
var string = "JavaScript"; 

console.log(string.replace(/Script/, "code"));  //Javacode
```

## #20 —修剪字符串中的空白

这段代码将使用 JavaScript 中的 regex 方法删除字符串中的空格。当你从互联网上获得原始数据时，这是很方便的，因为这些数据中有标签和空格。

```
var str = " a b    c  d   e   f g   hi j ";
var newStr = str.replace(/\s+/g, '');console.log(newStr)
```

## 最后的想法

我希望你能从这篇文章中学到一些新的东西。这篇文章旨在帮助你解决一些日常脚本中可能遇到的基本 JavaScript 问题。

## 了解更多信息

我希望你会喜欢我的其他文章有趣和有帮助的。

[](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [## 如何让你的 python 代码运行速度提高 10 倍

### 让您的 python 代码运行速度提高 10 倍的简单提示和指南

levelup.gitconnected.com](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [## 学习编码的同时在线赚钱的 20 种方法

### 如果你是一名程序员，却没有在网上赚到钱，那你就错过了一个大好机会

levelup.gitconnected.com](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [](/build-a-desktop-app-with-python-4a847e3b596c) [## 用 Tkinter 和 Python 构建桌面应用程序

### 在本文中，我们将学习如何使用 python 和 Tkinter 模块开发现代桌面应用程序。一个…

levelup.gitconnected.com](/build-a-desktop-app-with-python-4a847e3b596c) [](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4) [## 使用 Python 阅读和编辑 PDF 文档

### 在本文中，我们将了解如何使用 python pdf 模块来读写 pdf 文件。PyPDF2 是一个…

levelup.gitconnected.com](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4) [](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [## PyQt5 教程:用 Python 和 PyQt5 学习 GUI 编程

### Pyqt5 是图形用户界面小部件工具包。它是最强大和最流行的 python 接口之一…

levelup.gitconnected.com](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) 

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)