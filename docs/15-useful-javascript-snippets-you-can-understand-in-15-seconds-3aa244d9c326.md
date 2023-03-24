# 你可以在 15 秒钟内理解 15 个有用的 JavaScript 代码片段

> 原文：<https://levelup.gitconnected.com/15-useful-javascript-snippets-you-can-understand-in-15-seconds-3aa244d9c326>

## 您可以立即理解的有用 JavaScript 代码片段列表

![](img/742ec1f1c627cb7caee1881b0eee76d4.png)

保罗·埃施-洛朗在 Unsplash[上的照片](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

JavaScript 是你能学到的最流行的语言之一。当我开始学习 JavaScript 时，我总是在 StackOverflow、medium 和其他博客上寻找代码片段。在本文中，我将分享我认为有用的 15 个 JavaScript 片段。

> JavaScript 是互联网的鸭带
> 
> —查理·坎贝尔

# 1.无循环地重复一个字符串

这个 JS 片段将展示如何不使用任何循环来重复一个字符串。我们将使用 JS 构建的方法通过传递一个数字来 **repeat()** ，该数字将充当您需要的数字副本。

```
//Old Methodfor(var i = 0; i<5; i++)
{
  console.log("😃") // 😃😃😃😃😃
}// Best Methodconsole.log("😃".repeat(5)) //😃😃😃😃😃
```

# 2.阵列差异

另一个让您在阵列中脱颖而出的精彩片段。当您处理一个长数组并想知道该数组的相似之处或不同之处时，这很方便。

下面的示例代码将帮助您理解，您可以在您的 JS 项目中自由使用这些代码。

```
//Code Examplefunction ArrayDiff(a, b){
  const setX = new Set(a)
  const setY = new Set(b)return [
    ...a.filter(x=>!setY.has(x)),
    ...b.filter(x=>!setX.has(x))
  ]}
  const Array1 = [1, 2, 3];
  const Array2 = [1, 2, 3, 4, 5];console.log(ArrayDiff(Array1, Array2)) // [4, 5]
```

# 3.字符串是否为 Json

当您需要检查数据是 string 还是 JSON 时，这个代码片段会派上用场。假设您从服务器端获得一个响应，然后解析该数据，您需要检查它是 JSON 还是 string。检查下面的代码片段。

```
//Code Examplefunction isJSON(str)
{
  try
  {
      JSON.parse(str)
  }
  catch
  {
      return false
  }return true}var str = "JavaScript"
console.log(isJSON(str)) //false
```

# 4.简短的 Console.log

厌倦了一遍又一遍地写 console.log()？不要担心，这个代码片段将为您节省大量编写长 console.log()的时间。

```
var cl = console.log.bind(document)
cl(345) 
cl("JAVASCRIPT")
cl("PROGRAMMING") <--Give it a try!-->
```

# 5.全部替换

这个代码片段将向您展示如何在不迭代每个单词的情况下替换字符串中的单词，匹配它，并放置新单词。下面的代码片段使用了 **replaceAll(目标单词，新单词)**方法。

```
//Code Examplevar str = "Python is a Programming Language, Python is a top programming language and favourite of every developer"
str = str.replaceAll("Python", "JavaScript")console.log(str) // JavaScript is a Programming Language, JavaScript 5is a top programming language and favourite of every developer
```

# 6.数字到数字数组

这个代码片段对于将数字转换成数字数组很有用。使用带有`map`的 spread 操作符，我们可以在一秒钟内完成。试一试:

```
//example code
const NumberToArray = number => [...`${number}`].map(i => parseInt(i));console.log(NumberToArray(86734)) //[8,6,7,3,4]
console.log(NumberToArray(1234)) //[1,2,3,4]
console.log(NumberToArray(9000)) //[9,0,0,0]
```

# 7.支票号码是 2 的幂

现在，这段代码将帮助您检查是否是 2 的幂。试着从下面的示例代码中理解它。

```
//example codeconst isPowerTwo = n => !!n &&( n & (n - 1) ) == 0;console.log(isPowerTwo(3)) //true
console.log(isPowerTwo(8)) //true
console.log(isPowerTwo(4)) //true
```

# 8.数字到二进制

这个代码片段将通过使用 **toString()** 方法简单地将数字转换成二进制。看看下面的代码示例。

```
var n1 = 500
console.log(n1.toString(2)) // 111110100var n2 = 4
console.log(n2.toString(2)) // 100var n3 = 5004
console.log(n3.toString(2)) // 1001110001100
```

# 9.返回数组编号的幂集

这个代码片段将返回任意数组的幂集。为了更好地理解，请查看下面的代码片段。

```
//example codeconst PowerSet = array => array.reduce((accumalator, current) => accumalator.concat(accumalator.map(n => [current].concat(n))), [[]]);console.log(PowerSet([1,2]))
```

# 10.从数组中删除元素

当您需要从数组中删除元素时，这个代码片段会派上用场。在下面的代码片段示例中，我们使用了 **array.slice()** 内置方法。

```
//example codeconst DropElement = (array, num = 1) => array.slice(num);console.log(DropElement([2,45,6,7],2)) //[6, 7]
console.log(DropElement([2,45,6,7],1)) //[45, 6, 7]
```

# 11.反转一根绳子

现在你不需要在绳子上循环来反转它。这个代码片段将展示如何使用**扩展操作符(…)** 和 **reverse()** 函数来反转字符串。

这在反转一个大字符串时很方便，因为你需要一个快速的代码片段。查看下面的代码示例。

```
//example codefunction Reverse(str){return [...str].reverse().join('');}console.log(Reverse("data")) //atad
console.log(Reverse("Code")) //edoC
```

# 12.深度展平阵列

展平数组是将任何有序的二维数组转换为一维数组的过程。简而言之，你可以降低数组的维数。你已经看过 flatten 数组代码片段，但是深度 Flatten 数组呢？

当您有一个大的有序数组，而普通的展平无法处理它时，这个代码片段非常有用。为此，你将需要一个深度展平。

```
//example codefunction DeepFlat(array)
{
  return [].concat(...array.map(value=>  (Array.isArray(value) ? DeepFlat(value) : value)));
}console.log(DeepFlat([1,[2,[4,6,6,[9]],0,],1])) // [1, 2, 4, 6, 6, 9, 0, 1]
```

# 13.计算字节大小

每个程序员都有一个目标，那就是他们的 JavaScript 程序将是高效的，并有良好的性能。为此，我们需要确保我们有一些大小合适的数据，不会使我们的内存过载。查看下面的代码片段，了解如何检查任何数据的字节。

```
// byte size calculationconst ByteSize = string => new Blob([string]).size;console.log(ByteSize("Codding")) // 7 
console.log(ByteSize(true)) // 4
console.log(ByteSize("😄")) // 4
```

# 14.阵列到 CSV

CSV 是目前广泛使用电子表格，您可以使用下面显示的简单代码片段将数组转换为 CSV。

```
// Code Exampleconst ArrayToCsv= (array, delimiter =',')=> array.map(value => value.map(num => `"${num}"`).join(delimiter)).join('\n');console.log(ArrayToCsv([['name', 'age'], ['haider', '22'], ['Peter', '23']]))// Output
// "name","age"
// "haider","22"
// "Peter","23"
```

# 15.数组的最后一个元素

现在，您不再需要迭代或循环遍历整个数组并提取最后一个元素。你可以用下面简单的代码片段做同样的事情。

```
//code exampleconst LastElement = array => array[array.length - 1];console.log(LastElement([2,3,4])) // 4
console.log(LastElement([2,3,4,5])) // 5
console.log(LastElement([2,3])) // 3
```

# 最后的想法

这些是 **15 个 JavaScript 代码片段**，我希望你喜欢这篇文章并从中学习到新的东西。请随时与社区分享您的回答或任何有用的片段。

与你的 JavaScript 开发者朋友分享❤️的这篇文章，如果你需要更多的编程经验，可以看看我在下面嵌入的其他文章。

[](/11-javascript-tricks-to-boost-your-skills-93c2fe1cd057) [## 提升技能的 11 个 JavaScript 技巧

### 大多数开发人员不知道这些专业 JavaScript 技巧

levelup.gitconnected.com](/11-javascript-tricks-to-boost-your-skills-93c2fe1cd057) [](/12-javascript-features-youve-probably-never-used-db932c413cdd) [## 您可能从未使用过的 12 个 JavaScript 特性

### 大多数人不知道 JavaScript 令人难以置信的特性

levelup.gitconnected.com](/12-javascript-features-youve-probably-never-used-db932c413cdd) [](/12-python-tricks-to-make-your-life-easier-b4a88e4c6767) [## 让你的生活更轻松的 12 个 Python 技巧

### 节省您宝贵时间的 Python 技巧和诀窍

levelup.gitconnected.com](/12-python-tricks-to-make-your-life-easier-b4a88e4c6767) [](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [## 学习编码的同时在线赚钱的 20 种方法

### 如果你是一名程序员，却没有在网上赚到钱，那你就错过了一个大好机会

levelup.gitconnected.com](/20-ways-to-make-money-online-while-learning-to-code-9aec753b742d) [](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [## 针对日常问题的 25 个有用的 Python 片段

### 以下是我为您的日常 Python 问题提供的 25 个有用且省时的片段

levelup.gitconnected.com](/25-useful-python-snippets-for-everyday-problems-4e1a74d1abae) [](/17-clever-javascript-tricks-that-every-developer-should-use-e7f299e49896) [## 每个开发人员都应该使用的 17 个聪明的 JavaScript 技巧

### 每个开发人员都应该知道的 JavaScript 技巧

levelup.gitconnected.com](/17-clever-javascript-tricks-that-every-developer-should-use-e7f299e49896) [](/12-smart-ways-to-earn-as-a-developer-4131def3b0a5) [## 作为开发人员的 12 种聪明的赚钱方法

### 除非你能在床上赚钱，否则不要呆在床上

levelup.gitconnected.com](/12-smart-ways-to-earn-as-a-developer-4131def3b0a5) [](/15-magical-javascript-tips-for-every-web-developer-3301feb0b70c) [## 给每个 Web 开发者的 15 个神奇的 JavaScript 技巧

### 15 个神奇的 JavaScript 技巧和窍门，节省您作为 Web 开发人员的宝贵时间

levelup.gitconnected.com](/15-magical-javascript-tips-for-every-web-developer-3301feb0b70c) [](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [## 20 个必要的代码片段，让你在 JavaScript 中像专家一样工作

### 你可以在 30 秒或更短时间内学会 20 个 JavaScript 代码片段

levelup.gitconnected.com](/20-essential-snippets-to-code-like-a-pro-in-javascript-c7a6ef4dbddc) [](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [## 掌握 Python 的面向对象编程(OOP)

### 通过掌握面向对象编程(OOP ),学习用 Python 编写更简洁、更模块化的代码。

levelup.gitconnected.com](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [## 使用 NLTK 的 Python 自然语言处理初学者指南

### 自然语言处理是人工智能的一个分支，它帮助计算机理解自然语言

levelup.gitconnected.com](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [## 如何让你的 python 代码运行速度提高 10 倍

### 让您的 python 代码运行速度提高 10 倍的简单提示和指南

levelup.gitconnected.com](/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa) [](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [## PyQt5 教程:用 Python 和 PyQt5 学习 GUI 编程

### Pyqt5 是图形用户界面小部件工具包。它是最强大和最流行的 python 接口之一…

levelup.gitconnected.com](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4) [## 使用 Python 阅读和编辑 PDF 文档

### 在本文中，我们将了解如何使用 python pdf 模块来读写 pdf 文件。PyPDF2 是一个…

levelup.gitconnected.com](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4)