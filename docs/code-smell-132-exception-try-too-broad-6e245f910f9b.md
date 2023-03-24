# 代码气味 132 —异常尝试范围太广

> 原文：<https://levelup.gitconnected.com/code-smell-132-exception-try-too-broad-6e245f910f9b>

## 例外是很方便的。但是应该尽可能的窄

![](img/d6e953ece1feb411ceabf62b530ade11.png)

> *TL；DR:处理错误时尽可能具体。*

# 问题

*   失败快速原则违反
*   缺失错误
*   假阴性

# 解决方法

1.  尽可能缩小异常处理程序的范围

# 示例代码

## 错误的

```
import calendar, datetime
try: 
    birthYear= input('Birth year:')
    birthMonth= input('Birth month:')
    birthDay= input('Birth day:')
    #we don't expect the above to fail
    print(datetime.date(int(birthYear), int(birthMonth), int(birthDay)))
except ValueError as e:
    if str(e) == 'month must be in 1..12': 
        print('Month ' + str(birthMonth) + ' is out of range. The month must be a number in 1...12')
    elif str(e) == 'year {0} is out of range'.format(birthYear): 
        print('Year ' + str(birthYear) + ' is out of range. The year must be a number in ' + str(datetime.MINYEAR) + '...' + str(datetime.MAXYEAR))
    elif str(e) == 'day is out of range for month': 
        print('Day ' + str(birthDay) + ' is out of range. The day must be a number in 1...' + str(calendar.monthrange(birthYear, birthMonth)))
```

## 对吧

```
import calendar, datetime

birthYear= input('Birth year:')
birthMonth= input('Birth month:')
birthDay= input('Birth day:')
# try scope should be narrow
try: 
    print(datetime.date(int(birthYear), int(birthMonth), int(birthDay)))
except ValueError as e:
    if str(e) == 'month must be in 1..12': 
        print('Month ' + str(birthMonth) + ' is out of range. The month must be a number in 1...12')
    elif str(e) == 'year {0} is out of range'.format(birthYear): 
        print('Year ' + str(birthYear) + ' is out of range. The year must be a number in ' + str(datetime.MINYEAR) + '...' + str(datetime.MAXYEAR))
    elif str(e) == 'day is out of range for month': 
        print('Day ' + str(birthDay) + ' is out of range. The day must be a number in 1...' + str(calendar.monthrange(birthYear, birthMonth)))
```

# 侦查

[X]手册

如果我们有足够好的测试套件，我们可以执行变异测试来尽可能缩小异常范围。

# 标签

*   例外

# 结论

我们应该尽可能地避免例外。

# 关系

[](https://blog.devgenius.io/code-smell-26-exceptions-polluting-9246aca40234) [## 气味代码 26 —污染例外

### 有许多不同的例外是非常好的。您的代码是声明性的和健壮的。还是没有？

blog.devgenius.io](https://blog.devgenius.io/code-smell-26-exceptions-polluting-9246aca40234) [](https://blog.devgenius.io/code-smell-73-exceptions-for-expected-cases-ea870197d5fc) [## 代码气味 73 —预期情况的例外

blog.devgenius.io](https://blog.devgenius.io/code-smell-73-exceptions-for-expected-cases-ea870197d5fc) 

# 信用

照片来自于 Unsplash 上的[雅各布·布劳恩](https://unsplash.com/es/fotos/Js2Tv3-uLB8)

> 异常处理程序的主要职责是让程序员摆脱错误，让用户大吃一惊。

*Verity Stob*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)