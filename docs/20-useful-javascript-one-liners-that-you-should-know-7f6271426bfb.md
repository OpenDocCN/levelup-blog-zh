# 你应该知道的 22 个有用的 JavaScript 一行程序

> 原文：<https://levelup.gitconnected.com/20-useful-javascript-one-liners-that-you-should-know-7f6271426bfb>

## 这使得 JavaScript 成为网络之王

![](img/c946ce4b92648b57f07614f27563cf3e.png)

全世界有超过 1000 万的 Javascript 开发人员，而且这个数字每天都在增加。尽管 JavaScript 更出名的是它的动态特性，但它还有许多其他的优秀特性。在这篇博客中，我们将看到你应该知道的 20 个 JavaScript 一行程序。

# 1.随机 ID 生成

当您正在执行原型开发并且需要唯一的 id 时，这个可以是您的首选功能。

```
const a = Math.random().toString(36).substring(2);
console.log(a)
----------------------------
*72pklaoe38u*
```

# 2.生成一个范围内的随机数

在很多情况下，我们需要在一个范围内产生一个随机数。`Math.random`函数可以为我们生成一个随机数，然后我们可以将它转换成我们想要的范围。

```
max = 20
min = 10
var a = Math.floor(Math.random() * (max - min + 1)) + min;
console.log(a)
-------------------------
*17*
```

# 3.打乱数组

在 JavaScript 中，我们没有像 python 有`random.shuffle()`那样的模块，但是仍然有一种方法可以在一行代码中混洗一个数组。

```
var arr = ["A", "B", "C","D","E"];
console.log(arr.slice().sort(() => Math.random() - 0.5))
------------------------------
*[ 'C', 'B', 'A', 'D', 'E' ]*
```

# 4.获取随机布尔值

Javascript 中的`Math.random`函数可以用来生成一个范围内的随机数。要生成一个随机布尔值，我们需要得到一个 0 到 1 之间的随机数字，然后我们检查它是高于还是低于 0.5。

```
const randomBoolean = () => Math.random() >= 0.5;
console.log(randomBoolean());
---------------------------------------
*false*
```

# 5.生成随机十六进制代码

作为一名 web 开发人员，您可以使用这个一行程序来挑战自己。这个一行程序将生成一个随机的十六进制代码。您可以使用一行程序生成 3-6 个颜色代码，这将为您创建一个调色板。

# 6.反转一根绳子

有很多方法可以反转一个字符串，但这是我在互联网上找到的最简单的方法之一。

```
const reverse = str => str.split('').reverse().join('');
console.log(reverse('javascript'));
----------------------------------------
*tpircsavaj*
```

# 7.交换两个变量

```
a = 5
b = 7
---------Method : 1---------
b = [a, a = b][0]; // One Liner ----------Method : 2-----------
[a,b] = [b,a];console.log("A=",a)
console.log("B=",b)
```

上面的代码展示了一些不使用第三个变量，只用一行代码交换两个变量的简单方法。

# 8.多变量赋值

像 Python 一样，JavaScript 也支持使用这种巧妙的析构技术在一行代码中同时分配多个变量。

```
var [a,b,c,d] = [20,14,30,"COD"]
console.log(a,b,c,d)
------------------------------------
*20 14 30 COD*
```

# 9.检查偶数和奇数

这是每个程序员在开始他们的编码生涯时面临的基本问题之一。有许多方法可以做到这一点，其中一个最简单的方法是使用 arrow 函数，只用一行代码就可以写完整的代码。

```
**const isEven = *num* => num % 2 === 0;**
console.log(isEven(2));
---------------------------------
*true*console.log(isEven(3));
----------------------------------
*false*
```

# 10.嘶嘶作响

这个问题是用来检验程序员核心的著名面试问题之一。在这个测验中，我们需要编写一个程序来打印从 1 到 100 的数字。但是对于三的倍数，打印“**嘶嘶**而不是数字，对于五的倍数，打印“**嗡嗡**”。

```
for(i=0;++i<10;console.log(i%5?f||i:f+'Buzz'))f=i%3?'':'Fizz'
----------------------------------
*1
2
Fizz
4
Buzz
Fizz
7
8
Fizz*
```

# 11.回文

回文是一个字符串或一个数字，当它被颠倒时看起来完全一样。比如:阿爸，121 等。

# 12.检查数组中的所有元素是否满足特定条件

```
**const hasEnoughSalary = (salary) => salary >= 30000**const salarys = [70000, 19000, 12000, 30000, 15000, 50000]
result = salarys.every(hasEnoughSalary) 
console.log(result)
-------------------------------
*false*const salarys = [70000, 190000 ,120000, 30000, 150000,50000]
result = salarys.every(hasEnoughSalary) // Results in false
console.log(result)
---------------------------------
*true*
```

# 13.计算两个给定日期之间的天数

为了计算两个日期之间的天数，我们首先找到两个日期之间的绝对值，然后用它除以 86400000，8640000 等于一天中的毫秒数，最后，我们将结果四舍五入并返回。

# 14.将字符串转换为数字

将字符串转换成数字的一个非常直接的方法是使用类型转换。

```
toNumber = str => +str;
toNumber = str => Number(str);
result = toNumber("2");
console.log(result)
console.log(typeof(result))
----------------------------------
*2
number*
```

# 15.合并多个数组

```
const cars = ['🚓', '🚗'];
const trucks = ['🚚', '🚛'];**----- METHOD : 1 -------**
const combined = cars.concat(trucks);
console.log(combined)
--------------------------------------------------
*[ '🚓', '🚗', '🚚', '🚛' ]***----- METHOD : 2 --------**
const combined = [].concat(cars,trucks);
console.log(combined)
--------------------------------------------------
*[ '🚓', '🚗', '🚚', '🚛' ]*
```

# 16.将数字截断到小数点后一位

在`Math.pow()`的帮助下，你可以将一个数字截断到某个小数点的方法。

# 17.滚动到页面顶部

这个`window.scrollTo()`方法可以帮助你完成任务。它需要页面上滚动到的位置的 x 和 y 坐标。如果您将这些设置为(0，0)，那么它将滚动到页面的顶部。

```
**const goToTop = () => window.scrollTo(0, 0);
goToTop();**
```

# 18.将华氏温度转换为摄氏温度(反之亦然)

无论您选择华氏温度还是摄氏温度，将所有温度参数转换成一个单一的单位总是一个更好的主意。

# 19.特定 Cookie 的值

# 20.将文本复制到剪贴板

将文本复制到剪贴板非常有用，也是一个很难解决的问题。你可以在网上找到各种各样的解决方案，但是下面这个可能是最小最聪明的解决方案之一。

```
const copyTextToClipboard = async (text) => {
  await navigator.clipboard.writeText(text)
}
```

# 21.删除 HTML 标签

这个一行程序使用正则表达式删除任何类似于`<xxx>`的字符串，其中 x 可以是任何字符，包括`/`

```
**"<b>A</b>".replace(/<[^>]+>/gi, "");**
```

# 22.克隆阵列

它将返回原始数组的副本。

```
oldArray = [1,4,2,3]
var newArray = oldArray.slice(0);
console.log(newArray)
------------------------------------
[1,4,2,3]
```

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)