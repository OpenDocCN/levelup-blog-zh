# 学习 do-while 语句以及输入和处理，直到完成模板

> 原文：<https://levelup.gitconnected.com/learning-c-the-do-while-statement-and-the-input-and-process-until-done-template-9f6b60bea048>

![](img/2eca49c729e13d28be0160698ac12cf5.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将使用`do-while`语句演示另一种实现*输入和处理直到完成*模板的方法。`while`语句和`do-while`语句的主要区别在于`do-while`语句在循环的底部测试条件，而不是像`while`语句那样在循环的顶部测试。

# 输入并处理，直到完成模板审核

以下是该模板的伪代码:

*重复以下操作直到完成:
读取数值
处理数值*

例如，我将使用下面的`do-while`语句来演示，通过输入所有考试分数来确定考试的平均分数，然后在输入最后一个考试分数后计算平均值。以下是此问题的一些可能的伪代码:

*当有更多分数要输入时:
提示用户输入分数
从键盘上获得分数
将分数加到累计分数上
计算平均值*

现在让我们看看如何使用`do-while`语句实现这个模板。

# do-while 语句

以下是 do-while 语句的语法模板:

*do {
语句；
} while(条件)；*

同样，这条语句和`while`语句的区别在于，条件是在循环的底部测试的，而不是在循环的顶部。这实际上意味着一条`do-while`语句总是至少要执行一次，因为循环体是在条件之前遇到的。

让我们看第一个简单的例子，从 1 数到 10:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  int number = 1;
  do {
    printf("Number: %d\n", number);
    number++;
  } while (number <= 10);
  return 0;
}
```

这个程序的输出是:

```
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
Number: 6
Number: 7
Number: 8
Number: 9
Number: 10
```

现在让我们看一个更实际的例子。我想要一个程序，允许我输入任何数量的考试分数，然后当我退出输入分数，该程序计算并显示平均分数。下面是使用`do-while`语句的程序:

```
int main()
{
  int grade, total = 0, count = 0;
  do {
    printf("Enter a grade: ");
    scanf("%d", &grade);
    total += grade;
    count++;
  } while (grade != -1);
  double average = (float) total / count;
  printf("The average grade is %f\n", average);
  return 0;
}
```

该程序不计算正确的平均值:

```
Enter a grade: 60
Enter a grade: 70
Enter a grade: 80
Enter a grade: -1
```

问题是-1 的分数被加到总分中，总分数是 4 而不是 3。当然，解决这一问题的一种方法是退回最后一个标记等级，并调整输入的等级总数，从而生成以下程序:

```
int main()
{
  int grade, total = 0, count = 0;
  do {
    printf("Enter a grade: ");
    scanf("%d", &grade);
    total += grade;
    count++;
  } while (grade != -1);
  total++;
  count--;
  double average = (float) total / count;
  printf("The average grade is %f\n", average);
  return 0;
}
```

当然，更好的答案是根本不使用`do-while`语句编写这样的程序。对于这个问题，没有理由不使用`while`语句。

然而，在某些情况下,`do-while` 声明更受青睐。例如，当您想要对来自用户的一些输入进行数据验证时，可以使用`do-while`语句。对于这种情况，您希望输入首先发生，然后根据条件测试该输入。

例如，下面是一个小程序，它验证输入的测试分数是否在 0 到 100 之间:

```
int main()
{
  int grade;
  do {
    printf("Enter a grade: ");
    scanf("%d", &grade);
  } while (grade < 0 || grade > 100);
  printf("You entered %d.\n", grade);
  return 0;
}
```

以下是该程序运行的输出:

```
Enter a grade: -1
Enter a grade: 101
Enter a grade: -123
Enter a grade: 1234
Enter a grade: 88
You entered 88.
```

我可以用一个`while`循环写同样的程序，但是使用一个`do-while`对这种类型的问题更有意义。

有许多应用程序包含可以用`while`语句或`do-while`语句编写的循环。例如，下面是一个程序，它使用一个`while` 语句来计算从 500 美元开始，在利率为 2%的情况下，在银行账户中赚取某个 5000 美元需要多长时间:

```
int main()
{
  const double RATE = 1.02;
  double balance = 500.0;
  int years = 0;
  while (balance < 5000) {
    years++;
    balance *= RATE;
  }
  printf("It will take %d years to reach $5000.\n", years);
  return 0;
}
```

下面是用`do-while`语句编写的相同程序:

```
int main()
{
  const double RATE = 1.02;
  double balance = 500.0;
  int years = 0;
  do {
    years++;
    balance *= RATE;
  } while (balance < 5000);
  printf("It will take %d years to reach $5000.\n", years);
  return 0;
}
```

这是两个程序的输出:

```
It will take 117 years to reach $5000.
```

大多数专业程序员会说使用`while`语句而不是`do-while`语句，因为它对大多数程序员来说更熟悉，但我在这里的观点是证明两种形式都可以处理相同的问题，从`whi` le 语句到`do-while`语句几乎没有变化。

# 何时使用 do-while 语句

我的讨论表明，在某些情况下，`do-while`语句比`while`语句更好。当循环体确实需要在测试条件之前至少执行一次时，应该首选`do-while`语句。一个明显的情况是数据验证例程需要在循环结束时检查用户输入的内容。在这种情况下使用`while`循环通常需要在进入循环之前从用户处获得输入，然后在循环底部再次从用户处获得输入。

作为复习，这里有另一个例子，这次演示了一个用户登录程序片段:

```
int main()
{
  const int PIN = 1234;
  int user_pin;
  do {
    printf("Enter your pin: ");
    scanf("%d", &user_pin);
  } while (user_pin != PIN);
  printf("Pin accepted.\n");
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter your pin: 1235
Enter your pin: 5432
Enter your pin: 2341
Enter your pin: 1234
Pin accepted.
```

关于`do-while`声明的这篇文章到此结束。在我的下一篇文章中，我将讨论 C 中的第三种主要循环类型——`for`语句。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。