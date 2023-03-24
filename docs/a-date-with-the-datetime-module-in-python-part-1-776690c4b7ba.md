# Python |第 1 部分中的 Datetime 模块的日期

> 原文：<https://levelup.gitconnected.com/a-date-with-the-datetime-module-in-python-part-1-776690c4b7ba>

## 如何使用 Python 获得当前日期的月、年和日

![](img/0c4bef67232c695d7fa577ba5c0a8f9c.png)

我们可以使用 **2 类**提取当前日期。

1.  使用`date`类

```
from datetime import datetoday = date.today()
```

1.  使用`datetime`类

```
from datetime import datetimetoday = datetime.today()
```

运筹学

```
from datetime import datetimenow = datetime.now()
```

`date.today()`和`datetime.today()`的主要区别在于 **date** 类只是返回 date 对象。然而， **datetime** 类不仅返回日期，还返回时间。

# 使用 Python 提取当前的日、月和年

```
today = datetime.today()print(f"Today: {today}")
print(f"Year: {today.year}")
print(f"Month: {today.month}")
print(f"Day: {today.day}")
```

**输出**:

```
Today: 2022-10-22 17:13:37.918735
Year: 2022
Month: 10
Day: 22
```

# 使用 Python 提取星期几

```
today = datetime.today()print(f"Today: {today}")
print(f"Weekday: {today.weekday()}")"""
OUTPUT:Today: 2022-10-22 17:32:48.396792
Weekday: 5
"""
```

这个 **weekday()** 方法将同时适用于`date`和`datetime`类。

> 注意:这里 weekday()方法将返回从 **0** 到 **6** 之间的索引。

# 使用 Python 提取当前时间

```
today = datetime.now()print(f"Today: {today}")
print(f"Hour: {today.hour}")
print(f"Minute: {today.minute}")
print(f"Second: {today.second}")
```

**输出**:

```
Today: 2022-10-22 17:41:01.076569
Hour: 17
Minute: 41
Second: 1
```

> 想了解更多？
> 
> 注册我的[时事通讯](https://newsletter.sahilfruitwala.com/)，把最好的文章放进你的收件箱。

直到下一次👋

> 探索我的 Python 101 系列的其他博客

![Sahil Fruitwala](img/30aeed5bb80c6ac4f5bb6f17ea2e30e9.png)

[Sahil Fruitwala](https://sahil-fruitwala.medium.com/?source=post_page-----776690c4b7ba--------------------------------)

## Python 101 系列

[View list](https://sahil-fruitwala.medium.com/list/python-101-series-94cdb35d6ed4?source=post_page-----776690c4b7ba--------------------------------)11 stories![](img/ae0c1a4e37cec9c7900786142aa824c4.png)![](img/4b74f0fd1eec3553695b449d6d6c2aaa.png)![Learn how to read,write and append data into files using python](img/c511e01f9abc8533dbda906fe71d4da5.png)