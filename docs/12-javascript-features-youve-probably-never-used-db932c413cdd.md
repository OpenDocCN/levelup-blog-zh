# 您可能从未使用过的 12 个 JavaScript 特性

> 原文：<https://levelup.gitconnected.com/12-javascript-features-youve-probably-never-used-db932c413cdd>

## 大多数人不知道 JavaScript 令人难以置信的特性

![](img/754650ae3ec0820ad106be49be59d614.png)

当我开始我的 JavaScript web 开发生涯时。我喜欢寻找技巧和提示来缩短我的代码编写时间和精力。我主要在 Quora 和 StackOverflow 等热门网站上搜索 JavaScript 代码片段。

在本文中，我将向您展示 12 个 JavaScript 特性，您可能从未使用过，也没有兴趣学习。

# 👉1# —短回路

你知道在 JavaScript 中你可以用一行代码缩短循环吗？这意味着您现在可以为循环编写更少的代码。

```
var names = ["John", "Trevor", "Steve", "Jacob"]// long method
for(var i = 0;  i < names.length; i++) { 
  console.log(names[i])
}//short single line method
for(let name of names) console.log(name)
```

# 👉2 #—根据长度调整数组的大小

你知道我们可以用 JavaScript `length`方法调整数组大小吗？长度不仅仅是为了得到数组的大小。如果我们将一个数组的长度设置为任意值，它将对数组进行切片。

```
var array1 = [1, 2, 3, 4, 5, 6]
var array2 = ["Python", "JavaScript", "C++", "Dart"]array1.length=3
array2.length=1console.log(array1) // [1, 2, 3]
console.log(array2) // ["Python"]
```

# 👉3 #—函数参数

你不需要定义函数参数，你可以直接使用函数参数作为数组对象，而不需要在函数实现时声明参数。

```
function add() // no paramter is defined
{
  var sum = 0
  for(var i=0; i < arguments.length; i++)
  {
      sum=sum + arguments[i]

  }
  console.log("Total Sum : ", sum) // Total Sum : 9
}
// calling function
add(1, 3, 5)
```

# 👉4 #—JavaScript 中的时间戳

你知道吗，在 JavaScript 中，我们有很多方法使用 Date 方法来获取日期。查看下面的代码示例。

```
// original method
var date = new Date()
timestamp = date.getDate()
console.log(timestamp)// shorter method
timestamp = new Date().getDate()
console.log(timestamp)// shortest method
timestamp += new Date();
console.log(timestamp)
```

# 👉5 #—删除数组中的值

通常，我们使用 delete 方法从数组中移除一个项。但这就是在数组中打洞的方法。它会将 undefined 放在移除项目索引上。

我们可以使用`splice`方法做一些工作，但是它完全从数组中删除了索引，没有留下任何漏洞。

```
// synatax :  splice(array index, number of value to delete )var array = [1, 2, 3, 4, 5, 6]//delete method
delete array[4]//splice method
array.splice(4,1) 
console.log(array) // [1, 2, 3, 4, 6]
```

# 👉6 #—JavaScript 中的 IN 运算符

通过使用`in`操作符，你可以检查对象中是否存在一个键。当你检查一个特定的键是否存在于一个对象中时，这很方便。

```
var a = 4
var b = 5
var list = {1:7, 3:9, 4:0, 2:9}console.log(a in list) //true
console.log(b in list) // false
```

# 👉7# —JavaScript 字符串填充

JavaScript 填充用于在字符串文本中添加填充。我们可以在字符串的开头或结尾添加填充。这里是`padStart`和`padEnd`的语法。

```
padStart(targetLength, padString(optional))padEnd(targetLength, padString(optional))
```

PadString 在两种填充方法中都是可选参数。下面是理解他们工作的代码示例。

```
console.log("123".padStart(5)) //   123
console.log("123".padStart(5, "0")) // 00123console.log("123".padEnd(5, "0")) // 12300
console.log("123".padEnd(10, "0")) // 1230000000
```

# 👉8# —电力**运营商

这个特性将为你节省大量的数学计算时间。你可能用`Math.pow()`函数来计算一个数的幂。但是我们可以使用**运算符来代替它。

```
// old method
var p = Math.pow(2,5)
console.log(p) // 32// new method
var p = 2**5
console.log(p) // 32
```

你会觉得`Math.pow()`还是最好的办法。那么用这个方法解一个很长的数学方程呢。

```
// old method
var p = Math.pow(2,5) + Math.pow(2,5) + Math.pow(2,1) + Math.pow(2,3) + Math.pow(2,4) + Math.pow(2,9)
console.log(p) // 602// new method
var p = 2**5 + 2**5 + 2**1 + 2**3 + 2**4 + 2**9
console.log(p) // 602
```

嗯，比用`Math.pow()`函数更干净易懂。

# 👉9# —包括()

我敢打赌，你们大多数人都使用`indexOf`来查找数组中的元素。不要用那种方法。因为我们有更好的方法来做同样的工作。使用`include`方法代替`indexOf`，后者返回布尔值。

```
var array = ["Python", "JavaScript", "C++", "Dart", "JAVA"]console.log(array.includes("JavaScript")) //True
console.log(array.includes("C#")) // false
```

# 👉10# —重定向到 Url

JavaScript 有一些方法，一旦你执行代码，就可以在浏览器中将你重定向到一个网站。当用户在网站上执行任何操作，JavaScript 将用户重定向到另一个 URL 时，这很方便。

```
const Redirect = (url, asLink = true) => asLink ? (window.location.href = url) : window.location.replace(url);
redirect('https://medium.com/@codedev101')
```

# 👉11# —一元运算符(+)

一元运算符可以方便地将字符串数字转换为数字格式，将日期转换为毫秒。看看下面的代码示例。

```
var strnum = "324.5"var num = +strnum
console.log(num) // 324.5var currentDate = new Date(); 
var millisSince = +currentDate;console.log(millisSince)
```

# 👉12# —将浮点数转换为整数(快速方法)

要将 float 转换成整数，你必须使用`Math.floor()`、`Math.round()`和`Math.ceil()`方法，但是你可以通过使用`|`位 OR 运算符以更快的方式完成转换。查看下面的代码示例。

```
// old way
console.log(Math.floor(23.56))// Quick way
console.log(23.56 | 0)
```

# 最后的想法

我希望您发现 JavaScript 的这些特性和技巧是有用的和有趣的，并且您发现任何新的技巧了吗？我很想在评论区看到它。

如果你觉得这篇文章有帮助，点击下面的❤️按钮，在你的任何社交媒体上分享这篇文章，这样你的朋友也可以从中受益。

# 了解更多信息

如果你喜欢这篇文章，你也会发现我的其他文章很有趣。也看看他们。

[](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [## 针对日常问题的 25 个有用的 Python 片段

### 以下是我为您的日常 Python 问题提供的 25 个有用且省时的片段

levelup.gitconnected.com](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [](/15-python-hidden-features-youve-probably-never-used-bb59bb3138b6) [## 你可能从未用过的 15 个 Python 特性

### 大多数人不知道 Python 的这些不可思议的特性。

levelup.gitconnected.com](/15-python-hidden-features-youve-probably-never-used-bb59bb3138b6) [](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [## 20 个必要的代码片段，让你在 JavaScript 中像专家一样工作

### 你可以在 30 秒或更短时间内学会 20 个 JavaScript 代码片段

levelup.gitconnected.com](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [## 学习编码的同时在线赚钱的 20 种方法

### 如果你是一名程序员，却没有在网上赚到钱，那你就错过了一个大好机会

levelup.gitconnected.com](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [## 掌握 Python 的面向对象编程(OOP)

### 通过掌握面向对象编程(OOP ),学习用 Python 编写更简洁、更模块化的代码。

levelup.gitconnected.com](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [## 如何让你的 python 代码运行速度提高 10 倍

### 让您的 python 代码运行速度提高 10 倍的简单提示和指南

levelup.gitconnected.com](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [](/python-pandas-tutorial-a-complete-introduction-for-beginners-add7013095c2) [## Python 熊猫教程:初学者完全入门

### 在本分步教程中，您将了解如何开始使用 Pandas 和 Python 探索数据集。

levelup.gitconnected.com](/python-pandas-tutorial-a-complete-introduction-for-beginners-add7013095c2) [](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [## PyQt5 教程:用 Python 和 PyQt5 学习 GUI 编程

### Pyqt5 是图形用户界面小部件工具包。它是最强大和最流行的 python 接口之一…

levelup.gitconnected.com](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) 

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)