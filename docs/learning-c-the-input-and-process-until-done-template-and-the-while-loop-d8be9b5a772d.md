# 学习 C:输入和处理直到完成模板和 while 循环

> 原文：<https://levelup.gitconnected.com/learning-c-the-input-and-process-until-done-template-and-the-while-loop-d8be9b5a772d>

![](img/fa354bf5431816f642137f38dd121584.png)

照片由[纳蕾塔·马丁](https://unsplash.com/@splashabout?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在本文中，我将讨论 C 中的下一个主要结构——循环。我将首先介绍一个与循环相关的新程序模板，即*输入和处理直到完成*模板，然后我将演示如何使用一个 C 循环结构来实现这个模板——`while`语句。

# 输入并处理直到完成模板

在计算机程序中，你最常做的一件事就是作为用户重复输入数据并处理这些数据，直到不再有数据输入。输入和处理直到完成模板提供了如何为这个任务编写代码的大纲。

下面是模板的伪代码:

*重复以下操作直到完成:
读取数值
处理数值*

例如，我将在下面的 C #中演示，通过输入所有的考试分数来确定考试的平均分数，然后在输入最后一个考试分数后计算平均值。以下是此问题的一些可能的伪代码:

*当有更多分数要输入时:
提示用户输入分数
从键盘上获取分数
将分数添加到累计分数中
计算平均值*

现在让我们看看如何使用 while 语句实现这个模板。

# while 语句

我将向您展示的在 C 中实现循环的第一个 C 构造是`while` 语句。`while`语句测试一个条件，并在条件为真时执行一组语句。

下面是`while`语句的语法模板:

*while(表情){
语句(s)；//循环体
}*

我将从一个简单的例子开始——一个从 1 数到 10 的计数程序。程序如下:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  int number = 1;
  while (number <= 10) {
    printf("number is: %d\n", number);
    number++;
  }
  return 0;
}
printf("End of program.\n");
```

这个程序是这样执行的。变量`number`被初始化为 1。在下一行中，测试`number`以查看其值是否小于或等于 10。因此，控制权落入循环体，并调用`printf`函数。下一行将`number`增加 1。然后控制跳回到控制表达式(`number <= 10`)，由于 2 小于或等于 10，循环体再次执行。这一直持续到`number`的值变为 11。然后测试表达式，由于 11 不小于或等于 10，表达式为假，控制跳转到循环体结束后的第一行。

这个程序让我有机会列出当你写一个`while`语句时应该遵循的步骤。第一步是声明一个您希望为真的条件，这样循环体内的语句将会执行。这个问题要求从 1 数到 10，所以我希望循环在变量 number 的值小于或等于 10 时执行。

第二步是对控制表达式中的变量进行初始化或赋值，使表达式的计算结果为 true，并进入循环体。通过将`number`初始化为 1，我确信循环至少会运行一次。

第三步是执行您希望循环执行的任何任务。在这种情况下，任务是显示`number`的当前值。

第四步也是最后一步是修改控制表达式，这样循环将最终停止。如果不执行此步骤，循环将无限期运行，或者直到计算机内存耗尽。这被称为无止境或无限循环。就在测试之前，我通过在循环底部将 `number`加 1 来避免一个死循环。

以下是该程序的输出:

```
number is: 1
number is: 2
number is: 3
number is: 4
number is: 5
number is: 6
number is: 7
number is: 8
number is: 9
number is: 10
```

这种类型的循环被称为*计数控制的*循环，因为你可以查看程序并知道循环将迭代(重复)多少次。因为变量被初始化为 1，程序在变量达到 10 后停止。

计数控制的循环通常使用 for 语句编写，我将在以后的文章中讨论这一点。

另一种类型的回路是*哨兵控制的*回路。这种类型的循环一直运行，直到遇到一个特殊值，导致循环停止。特殊值是 while 语句表达式的一部分。

# 哨兵控制的循环和输入和处理，直到完成模板

*输入和处理直到完成*模板通常用哨兵控制的循环来实现。下面是一个问题的伪代码，使用这个模板可以最好地解决这个问题:

*当有更多成绩要输入时:
提示用户输入成绩
从键盘上获取成绩
将成绩加到总成绩上
计算平均值*

现在让我们编写一个实现这个伪代码的程序:

```
int main()
{
  int grade, count, total = 0;
  char more = 'Y';
  count = 0;
  while (more == 'Y') {
    printf("Enter a grade: ");
    scanf("%d", &grade);
    total += grade;
    count++;
    getchar();
    printf("Enter another grade (Y/N)? ");
    scanf("%c", &more);
  }
  double average = (float)total / count;
  printf("The grade average is %.2f", average);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter a grade: 67
Enter another grade (Y/N)? Y
Enter a grade: 71
Enter another grade (Y/N)? Y
Enter a grade: 83
Enter another grade (Y/N)? Y
Enter a grade: 91
Enter another grade (Y/N)? Y
Enter a grade: 99
Enter another grade (Y/N)? N
The grade average is 82.20
```

这个程序的一个问题是，输入不断被询问用户是否想输入另一个等级的提示打断。使用 sentinel 值的另一种方式是选择一个不能获得的等级，例如-1，输入该等级是程序停止的信号。

下面是用这样一个标记值重写的程序:

```
int main()
{
  int grade, count, total = 0;
  char more = 'Y';
  count = 0;
  printf("Enter a grade: ");
  scanf("%d", &grade);
  while (grade != -1) {
    total += grade;
    count++;
    getchar();
    printf("Enter a grade: ");
    scanf("%d", &grade);
  }
  double average = (float)total / count;
  printf("The grade average is %.2f", average);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter a grade: 81
Enter a grade: 77
Enter a grade: 92
Enter a grade: 85
Enter a grade: -1
The grade average is 83.7
```

哨兵控制回路的另一个用途是用于数据验证。例如，您正在编写的程序需要用户输入一个正数，任何小于 1 的数字都是不可接受的。您可以使用 1 作为标记值，实际上就是说，如果您输入的值小于 1，则该数据无效，请输入一个至少等于 1 的新值。

下面是一个演示在数据验证中使用 sentinel 值的程序:

```
int main()
{
  int data = 0;
  while (data < 1) {
    printf("Enter a number: ");
    scanf("%d", &data);
  }
  printf("You entered a valid number.\n");
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter a number: 0
Enter a number: -1
Enter a number: -100
Enter a number: 0
Enter a number: 2
You entered a valid number.
```

下面是另一个例子，用户需要输入一个介于 1 和 100 之间的值:

```
int main()
{
  int grade = 0;
  while (grade < 1 || grade > 100) {
    printf("Enter a grade value of 1 to 100: ");
    scanf("%d", &grade);
  }
  printf("%d is between 1 and 100.\n", grade);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter a grade value of 1 to 100: -1
Enter a grade value of 1 to 100: 1000
Enter a grade value of 1 to 100: 0
Enter a grade value of 1 to 100: 101
Enter a grade value of 1 to 100: 85
85 is between 1 and 100.
```

我已经偏离了*输入和处理直到完成*模板，但是使用循环执行数据验证是每个程序员都可以使用的重要工具，所以我认为现在向您展示它是很重要的。

这就是本文关于循环和*输入和处理直到完成*模板的内容。在我的下一篇文章中，我们将继续学习如何使用不同的循环结构来实现这个模板——`do-while`语句。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。