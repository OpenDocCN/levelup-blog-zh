# 学习 Python:积累值直到完成模板

> 原文：<https://levelup.gitconnected.com/learning-python-the-accumulate-values-until-done-template-7a3c001621f>

![](img/139d4170ad1d5f231ff0e3c3ae6a81c7.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

计算机程序做的最常见的事情之一是累加值，然后对结果做一些事情。一个例子是确定一年中每月进行的一组存款的总和；另一个例子是考试后的平均成绩；最后一个例子是确定过去一周的平均高温。有一个模板描述了这个过程——*累加值直到完成*模板。在本文中，我将演示如何使用一个`while`循环和一个`for`循环来实现这个模板。

# “完成前累计值”模板

这个模板是通过一个循环实现的，该循环向用户请求值，直到没有更多的值可以输入为止。下面是我们如何用伪代码描述这个模板:

*初始化变量以存储结果
重复输入请求，直到完成
将值累积到开始定义的变量中*

这是模板的大纲，尽管您可能希望在接收到所有输入后对累积值做一些事情，例如计算输入值的平均值。

# 用 for 循环实现累加值直到完成模板

有两种方法可以实现这个模板。如果你知道你将只输入一定数量的值，你可以使用一个`for`循环，使用`range`函数来控制接收到的输入数量。

这里有一个例子。比方说，如果你第一个月投资 50 美元，每月增加 10 美元，你想确定一年内你的储蓄账户上会积累多少钱(未计利息)。下面是解决这个问题的程序:

```
deposit = 50
balance = 0
for month in range(1, 13):
  balance = balance + deposit
  deposit = deposit + 10
print("After 1 year, you have",balance,"in your account.")
```

这个程序的输出是:

```
After 1 year, you have 1260 in your account.
```

您可以修改这个程序，提示用户输入一个金额，添加到每月存款中，如下所示:

```
deposit = 50
balance = 0
for month in range(1, 13):
  added = int(input("How much to add to balance? "))
  deposit = deposit + added
  balance = balance + deposit
print("After 1 year, you have $",balance,"in your account.")
```

这是另一个使用`for`循环来累加值的问题。写一个程序，确定你家乡过去一周的平均高温。这个程序适合使用 for 循环，因为我们知道一周有 7 天，所以循环需要迭代 7 次。

程序是这样的:

```
sumTemps = 0
daysInWeek = 7
for day in range(1, 8):
  temp = int(input("Daily high temperature: "))
  sumTemps = sumTemps + temp
avgTemp = sumTemps / daysInWeek
print("The average high temperature was", avgTemp)
```

下面是该程序运行一次的输出:

```
Daily high temperature: 71
Daily high temperature: 73
Daily high temperature: 71
Daily high temperature: 80
Daily high temperature: 77
Daily high temperature: 81
Daily high temperature: 68
The average high temperature was 74.42857142857143
```

现在让我们来看看如何使用`while`循环实现这个模板。

# 用 while 循环实现累加值直到完成模板

`while`循环也可用于实现*累加值直到完成*模板。上面写的两个程序都可以用一个`while`循环重写。下面是累积存款的程序:

```
deposit = 50
balance = 0
month = 1
while month < 13:
  balance = balance + deposit
  deposit = deposit + 10
  month = month + 1
print("After 1 year, you have",balance,"in your account.")
```

这是计算每周高温的程序:

```
sumTemps = 0
day = 1
daysInWeek = 7
while day < 8:
  temp = int(input("Daily high temperature: "))
  sumTemps = sumTemps + temp
  day = day + 1
avgTemp = sumTemps / daysInWeek
print("The average high temperature was", avgTemp)
```

请记住，对于`while`循环，您必须确保修改条件中正在测试的变量，以确保条件最终变为假。

然而，更有可能的是，你将在一个没有明确结束的程序中使用一个`while`循环。例如，问题可能是为未知数量的测试计算平均测试等级。在这种情况下，您可以在程序中使用 sentinel 值来确定何时停止。

下面是这样一个 Python 程序，使用值-99 作为标记值:

```
sumGrades = 0
numGrades = 0
grade = int(input("Enter a grade (-99 to stop): "))
while grade != -99:
  sumGrades = sumGrades + grade
  numGrades = numGrades + 1
  grade = int(input("Enter a grade (-99 to stop): "))
average = sumGrades / numGrades
print("The average grade is", average)
```

下面是这个程序的一次运行:

```
Enter a grade (-99 to stop): 81
Enter a grade (-99 to stop): 77
Enter a grade (-99 to stop): 84
Enter a grade (-99 to stop): 75
Enter a grade (-99 to stop): 93
Enter a grade (-99 to stop): 99
Enter a grade (-99 to stop): 67
Enter a grade (-99 to stop): -99
The average grade is 82.28571428571429
```

sentinel 值也可以基于询问用户是否想要退出输入数据的提示。下面是一个程序，它可以确定一组每日低温的平均低温:

```
numTemps = 0
sumTemps = 0
quit = "n"
while quit == "n":
  temp = int(input("Enter the daily low temperature: "))
  sumTemps = sumTemps + temp
  numTemps = numTemps + 1
  quit = input("Do you want to quit (y/n)? ")
avgTemp = sumTemps / numTemps
print("The average daily low temperature is",avgTemp)
```

下面是这个程序的一次运行:

```
Enter the daily low temperature: 45
Do you want to quit (y/n)? n
Enter the daily low temperature: 47
Do you want to quit (y/n)? n
Enter the daily low temperature: 44
Do you want to quit (y/n)? n
Enter the daily low temperature: 45
Do you want to quit (y/n)? n
Enter the daily low temperature: 46
Do you want to quit (y/n)? n
Enter the daily low temperature: 47
Do you want to quit (y/n)? y
The average daily low temperature is 45.666666666666664
```

*累加值直到完成*模板是一个你可以反复使用来解决计算问题的模板。正如我所演示的，根据问题的具体情况，您可以使用`for`循环或`while`循环来实现模板。如果问题需要你在一段特定的时间内累加值或者累加一定数量的值，那么使用带有`range`函数的`for`循环。如果无法确定时间段或事先不知道值的数量，则使用`while`循环来实现该模板。

感谢您的阅读，如果您有任何意见和建议，请发邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)给我。