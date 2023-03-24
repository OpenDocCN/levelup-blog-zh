# 用 Python 构建年龄计算器程序

> 原文：<https://levelup.gitconnected.com/build-age-calculator-program-in-python-aff2283018f0>

## 本文将探索如何使用 Python 构建一个简单的年龄计算器

![](img/692d80b39ec11ccaf30044e73129125c.png)

洛根·韦弗| @LGNWVR 在 [Unsplash](https://unsplash.com/s/photos/age?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

**目录**

*   介绍
*   用 Python 构建年龄计算器
*   完全码
*   结论

# 介绍

在本教程中，我们将使用 Python 构建一个简单的程序来计算用户的年龄。

对于开始学习 Python 的初学者和那些想要创建他们的第一个小项目的人来说，这是一个完美的教程。

在本教程中，我们将使用 [datetime](https://docs.python.org/3/library/datetime.html) 模块，它提供了处理日期和时间的类。它已经内置在 Python 中，所以不需要额外安装。

# 用 Python 构建年龄计算器

让我们从导入所需的依赖项开始:

```
#Import the required dependency
from datetime import date
```

然后我们会要求用户输入他们的生日，然后我们会用他们的生日创建一个 *datetime* 对象:

```
#Ask user to input year, month, and day of their birthday
#and output a datetime object of their birthday
def get_user_birthday():
    birth_year = int(input('Please enter your birth year: '))
    birth_month = int(input('Please enter your birth month: '))
    birth_day = int(input('Please enter your birth day: '))

    #Convert user input into datetime object
    user_birthday = date(birth_year, birth_month, birth_day)

    return user_birthday
```

现在我们可以计算用户的年龄:

```
#Define function to calculate user's age
def calculate_age(user_birthday):
    #Get current date
    today = date.today()
    #Calculate the years difference
    year_diff = today.year - user_birthday.year
    #Check if birth month and birth day precede current month and current day
    precedes_flag = ((today.month, today.day) < (user_birthday.month, user_birthday.day))
    #Calculate the user's age
    age = year_diff - precedes_flag
    #Return the age value
    return age
```

现在我们可以执行代码了:

```
if __name__ == "__main__":
    current_age = calculate_age(user_birthday)
    print(f"Your age is: {current_age}")
```

用生日样本测试代码:

```
Please enter your birth year: 1999
Please enter your birth month: 02
Please enter your birth day: 01
```

您应该得到:

```
Your age is: 23
```

# 完全码

```
#Import the required dependency
from datetime import date

#Ask user to input year, month, and day of their birthday
#and output a datetime object of their birthday
def get_user_birthday():
    birth_year = int(input('Please enter your birth year: '))
    birth_month = int(input('Please enter your birth month: '))
    birth_day = int(input('Please enter your birth day: '))

    #Convert user input into datetime object
    user_birthday = date(birth_year, birth_month, birth_day)

    return user_birthday

#Define function to calculate user's age
def calculate_age(user_birthday):
    #Get current date
    today = date.today()
    #Calculate the years difference
    year_diff = today.year - user_birthday.year
    #Check if birth month and birth day preced current month and current day
    precedes_flag = ((today.month, today.day) < (user_birthday.month, user_birthday.day))
    #Calculate the user's age
    age = year_diff - precedes_flag
    #Return the age value
    return age

if __name__ == "__main__":
    user_birthday = get_user_birthday()
    current_age = calculate_age(user_birthday)
    print(f"Your age is: {current_age}")
```

# 结论

在本文中，我们介绍了如何用 Python 创建年龄计算器。我也鼓励你看看我在 [Python 编程](https://pyshark.com/category/python-programming/)上的其他帖子。

如果你有任何问题或者对编辑有任何建议，请在下面留下你的评论。

*原载于 2022 年 12 月 6 日 https://pyshark.com*[](https://pyshark.com/build-age-calculator-in-python/)**。**