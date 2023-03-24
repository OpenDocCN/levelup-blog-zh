# Python 中小数点的初学者指南

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-decimal-points-in-python-467c36d2a85a>

## 控制小数值的技巧

![](img/1a704c9bfa47ea128f4d47bccabbac0d.png)

在任何编程语言中，处理小数点都是一项至关重要的技能——尤其是在关注数据科学的时候。

这里有一个简单的指南，涵盖了 Python 中小数点的舍入、截断和格式化的基础知识。

# 舍入

就像在小学一样，我们要讲的第一件事是四舍五入。

为了模仿标准的“five and up”规则，我们将使用独立的`round()`函数。

```
myNum = 3.14159265359print(round(myNum,2) # 3.14
print(round(myNum,4) # 3.1416
```

如果你想强制向上或向下舍入，会发生什么？预装数学库有两种方法可以帮你解决。

为了总是向上取整，我们将使用`ceil()`方法 ceiling 的缩写。

```
from math import ceilmyNum = 3.14159265359print(ceil(myNum)) # 4
```

为了总是向下舍入，我们将使用`floor()`方法——与上限相反。

```
from math import floormyNum = 3.14159265359print(floor(myNum)) # 3
```

# 缩短

处理小数的下一个技巧是去除小数，称为截断。没有智能舍入，只是删除所有小数点。

为此，我们使用数学库的另一个方法，名为`trunc()`。

```
from math import truncmyNum = 3.14159265359print(trunc(myNum)) # 3
```

但是，如果我们只想截断一些小数呢？我们需要围绕`trunc()`方法构建一个自定义函数，指定保留多少位小数。

```
from math import truncdef truncate(n,d=0):
  if d>0:
    return trunc(n * 10**d) / (10**d)
  else:
    return trunc(n)myNum = 3.14159265359print(truncate(myNum)) # 3
print(truncate(myNum,4)) # 3.1415
```

基本上，该函数在截断前移动小数 d 空格，然后在返回前重新调整小数。我们为`d`定义了一个默认值，这样如果没有传递第二个参数，我们的函数将像普通的`trunc()`方法一样工作。

# 打印小数

我们转到第三项技能，打印小数。

现在，如果你已经使用了上述任何一种技术来格式化你的十进制数值，那么这就很容易了。我们简单地使用一个 f 字符串，并将变量放在花括号中。

```
myNum = 3.14159265359myNum = round(myNum,2)print(f"My number is {myNum}") # My number is 3.14
```

然而，如果你想保留原来的数字，只是为了打印而格式化，那么你需要做的就是把你的表达式写在 f 字符串的花括号里。

```
myNum = 3.14159265359print(f"My number is {round(myNum,2)}") # My number is 3.14
```

# 结论

这就是我们开始处理十进制数值的快速而简单的指南。掌握小数还有更多的内容，包括理解浮点数和使用高级工具，如 Decimal 类，但我们将把这些主题留待以后讨论。

我希望这篇文章对你有所帮助，请在下面留下你的反馈，分享你在 Python 中使用小数点的经验。