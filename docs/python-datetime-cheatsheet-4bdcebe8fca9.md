# Python 日期时间备忘单

> 原文：<https://levelup.gitconnected.com/python-datetime-cheatsheet-4bdcebe8fca9>

*您需要的最后一本快速参考指南…*

![](img/583cc7713b01bebcef076a17a84df78c.png)

由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 的 datetime 模块是在代码中处理日期和时间的强大工具。在这篇博文中，我们将介绍一些使用 datetime 模块的不同方法，以使程序员的生活更加轻松。

因此，请将这一页加入书签。此外，一定要让我知道你所知道的所有技巧。

首先，让我们看看如何存储和操作日期和时间。datetime 模块提供了存储和处理日期和时间的类，比如`datetime`、`date`和`time`。您可以使用这些类来存储特定的日期和时间，或者当前的日期和时间。

以下是存储当前日期和时间的示例:

```
import datetime

now = datetime.datetime.now()
print(f"Current date and time: {now}")
```

> 当前日期和时间:2022–12–22 21:20:09.754036

下面是一个存储特定日期和时间的示例:

```
import datetime

new_year = datetime.datetime(2023, 1, 1, 0, 0, 0)
print(f"New Year's Day: {new_year}")
```

您还可以使用`timedelta`类来计算两个日期或时间之间的差异。例如:

```
import datetime

now = datetime.datetime.now()
new_year = datetime.datetime(2023, 1, 1, 0, 0, 0)
time_until_new_year = new_year - now
print(f"Time until New Year's Day: {time_until_new_year}")
```

> 元旦:2023–01–01 00:00:00

除了存储和操作日期和时间，datetime 模块还提供了格式化和解析日期和时间的工具。您可以使用`strftime`方法将`datetime`对象格式化为字符串，使用`strptime`函数将字符串解析为`datetime`对象。

下面是一个将`datetime`对象格式化为字符串的例子:

```
import datetime

now = datetime.datetime.now()
formatted_date_time = now.strftime("%Y-%m-%d %H:%M:%S")
print(f"Formatted date and time: {formatted_date_time}")
```

> 格式化的日期和时间:2022–12–22 21:25:47

下面是一个将字符串解析成`datetime`对象的例子:

```
import datetime

parsed_date_time = datetime.datetime.strptime("2022-12-01 00:00:00", "%Y-%m-%d %H:%M:%S")
print(f"Parsed date and time: {parsed_date_time}")
```

> *解析日期和时间:2022–12–01 00:00:00*

处理日期和时间的另一个常见任务是处理时区。datetime 模块提供了对时区的基本支持，但是您也可以使用 pytz 库来简化时区的使用。

下面是一个获取 UTC 当前日期和时间的示例:

```
import datetime

utc_now = datetime.datetime.utcnow()
print(f"Current date and time in UTC: {utc_now}")
```

> UTC 当前日期和时间:2022–12–22 15:56:58.069018

下面是一个将当前 UTC 时间转换为特定时区的示例:

```
import datetime
import pytz
utc_now = datetime.datetime.utcnow()

local_tz = pytz.timezone("Asia/Kolkata")
local_now = utc_now.astimezone(local_tz)
print(f"Current date and time in local time: {local_now}")
```

> 当地时间的当前日期和时间:2022–12–22 15:58:10.139960+05:30

提取日、月、年

```
import datetime

today = datetime.datetime.now()

year = today.year
month = today.month
day = today.day

print(f"Today is {day}, month - {month} & year {year}")
```

> 今天是 2022 年 12 月 22 日

快乐学习！