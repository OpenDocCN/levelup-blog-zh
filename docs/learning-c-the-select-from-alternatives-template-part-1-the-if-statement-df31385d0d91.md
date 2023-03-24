# 学习 C:从备选方案中选择模板第 1 部分，if 语句

> 原文：<https://levelup.gitconnected.com/learning-c-the-select-from-alternatives-template-part-1-the-if-statement-df31385d0d91>

![](img/9670c44c9b7f403683667e59254ce231.png)

由[美元吉尔](https://unsplash.com/@dollargill?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

您可以学习的最重要的程序模板之一是*从备选方案中选择*模板。当程序需要通过在不同的选项中进行选择来决定执行什么代码时，就使用这个模板。这方面的一个例子是一个工资程序，它必须根据雇员在工资支付期间工作的小时数来决定是按正常时间支付工资还是按加班时间支付工资。

*从备选项中选择*模板使用两个 C 结构中的一个来实现。在本文中，我将讨论这些结构中的第一个——if 语句。然而，在我教你 if 语句之前，我必须教你如何使用一组用于比较的操作符，称为关系操作符。

# 关系运算符

程序通过比较来决定执行什么代码。例如，一个工资单程序通过比较雇员工作的小时数和被认为是正常小时数的最大小时数(通常为 40)来决定是否给雇员支付加班费。如果工作时间少于或等于 40 小时，该计划支付员工正常工资。如果工作时数大于 40，程序将向员工支付加班费。

c 语言中有六种关系运算符，它们是:

*   `==`等于
*   `!=`不等于
*   `>`大于
*   `>=`大于或等于
*   `<`不到
*   `<=`小于或等于

严格地说，`==`和`!=`被认为是等式运算符，但我喜欢把它们归为关系运算符。

关系运算符是二元的，这意味着它们必须与两边的值一起使用。例如，要比较两个值以查看它们是否相等，您编写的表达式是:

```
(100 == 100)
```

我将表达式放在括号中，因为这些表达式在`if` 语句中使用时必须用括号括起来才有效。

关系运算符用于返回 true 或 false 值的表达式中，这些值将控制程序中的决策方式。您将在下面看到这是如何工作的。

C 没有 Boolean 类型，所以你不需要像在其他语言(包括 C++)中那样给变量赋值关系表达式。反正我不怎么使用这种数据类型，所以对我来说这也没什么损失。

与其花更多的时间讨论关系操作符，不如让我们继续讨论`if` 语句，我们可以看到关系操作符的作用。`if`语句有三种主要形式。我将从最简单的形式开始，我称之为简单的`if`陈述。

# 简单的 if 语句

简单的`if`语句查看一个关系表达式，如果表达式的值为 true，则执行一组程序语句。下面是简单的`if`语句的语法模板:

*if(表达式){
语句；
}*

在这个模板中有一些事情需要注意。首先，我使用 K&R 语法样式来放置花括号，花括号表示表达式的值为 true 时要执行的代码块。第二，我指出，通过在括号中包含复数 s，可以在块中编写一个以上的语句，这表明拥有一个以上的语句是可选的。

下面是一个代码片段的例子，它使用一个简单的`if`语句来决定一个雇员是否应该得到加班费:

```
int hours_worked = 45;
double rate = 11.25, gross_pay = 0.0;
if (hours_worked > 40) {
  gross_pay = (hours_worked – 40) * (rate * 1.5)
               + (hours_worked * rate);
}
```

该程序将在块内执行该语句，因为工作小时数大于 40。如果`hours_worked`的值小于 40，该语句将不被执行。

这个示例程序演示了简单的`if`语句的局限性。如果被求值的表达式为假，我们编写的程序通常需要执行一些其他的任务。要做到这一点，我们需要 if 语句的下一个也是更常见的版本——`if-else`语句。

# if-else 语句

如果给出的关系表达式为真，则使用`if-else`语句执行一组语句，如果表达式为假，则执行另一组语句。下面是`if-else`的语法模板:

*if(表达式){
语句；
}
else {
语句；
}*

如果表达式为真，则执行直接位于`if` 下的块中的语句，然后控制转移到`else`块之后的第一条语句。如果表达式为 false，则执行直接位于`else`下的块中的语句，程序将会转到`else`块外的第一行。

这是上面写的加班费程序，使用一个`if-else`语句重写，并允许交互输入(我省略了所有的预处理器指令，等等)。):

```
int hours_worked;
printf("%s", "Enter the hours worked: ");
scanf("%d", &hours_worked);
double rate = 11.25, gross_pay = 0.0;
if (hours_worked > 40) {
  gross_pay = (hours_worked – 40) * (rate * 1.5) 
               + (hours_worked * rate);
}
else {
  gross_pay = hours_worked * rate;
}
printf("Gross pay is %.2f.\n", gross_pay);
```

下面是该程序两次运行的输出:

```
Enter the hours worked: 45
Gross pay is $590.63.Enter the hours worked: 40
Gross pay is $450.00.
```

这里还有一个问题。写一个程序来确定一个数是奇数还是偶数。在程序中使用模数运算符。下面是解决这个问题的代码:

```
int number;
printf("Enter a number: ");
scanf("%d", &number);
if (number % 2 == 0) {
  printf("%d is even.\n", number);
}
else {
  printf("%d is odd.\n", number);
}
printf("End of program.");
```

以下是该程序两次运行的输出:

```
Enter a number: 22124
22124 is even.
End of program.Enter a number: 13311
13311 is odd.
End of program.
```

这两个问题表明了可以用 if-else 语句解决的问题类型。但是还有许多其他问题需要从几个选项中做出选择。为此，我们需要第三种版本的`if`语句，即`if-else if` 语句。不过，首先让我介绍一下 *Select from Alternatives* 模板，以演示对`if-else if`语句的需求。

# 从备选方案中选择项目模板

大多数涉及决策的编程问题都需要从几个选项中进行选择。我喜欢用的例子是给考试分数分配一个字母等级，因为我们都曾在生活中的某个时刻受到学校等级的伤害。

许多大学都有一个字母等级的分级表，如下所示:

90–100 A
80–89 B
70–79 C
60–69D
0–59 F

解决这个问题的逻辑是这样的:如果测试分数在 90 到 100 之间，赋字母 a 级，如果测试分数在 80 到 89 之间，赋字母 b 级，如果测试分数在 70 到 79 之间，赋字母 c 级，如果测试分数在 60 到 69 之间，赋字母 d 级，如果测试分数在 0 到 59 之间，赋字母 f 级。

这是一个在备选方案中进行选择的明显例子。我可以使用这样的模板编写分配字母等级的伪代码:

*如果分数为 90 到 100，将 A 分配给字母等级
否则如果分数为 80 到 89，将 B 分配给字母等级
否则如果分数为 70 到 79，将 C 分配给字母等级
否则如果分数为 60 到 69，将 D 分配给字母等级
否则如果分数为 0 到 59，将 F 分配给字母等级*

这就是*从选项中选择*模板的工作方式，现在我准备向您介绍`if-else if`语句。

# if-else if 语句

当你需要从几个备选方案中进行选择时，比如上一节所述的*从备选方案中选择*模板，你应该使用一个`if-else if`语句。以下是这种 if 语句的语法模板:

*if(表达式){
语句；
}
else if(表达式){
语句；
}
else if(表达式){
语句；
}
…
else {
语句；
}*

省略号表示如果需要，可以有更多的表达式可供选择。`else`块是可选的，如果您不想在其他块都不执行的情况下发生任何事情，可以省略它。

让我们通过实现上述*从备选方案*模板中选择部分的伪代码来看一个例子。这里有一个程序，提示用户输入一个测试分数，程序显示该分数所获得的字母等级:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  int score;
  char letterGrade;
  printf("Enter a test score: ");
  scanf("%d", &score);
  if (score >= 90) {
    letterGrade= 'A';
  }
  else if (score >= 80) {
    letterGrade = 'B';
  }
  else if (score >= 70) {
    letterGrade = 'C';
  }
  else if (score >= 60) {
    letterGrade = 'D';
  }
  else {
    letterGrade = 'F';
  }
  printf("A score of %d earns a letter grade of %c.",
          score, letterGrade);
  return 0;
}
```

下面是该程序两次运行的输出:

```
Enter a test score: 84
A score of 84 earns a letter grade of B.Enter a test score: 72
A score of 72 earns a letter grade of C.
```

有几种不同的方法可以编写这个程序。一种方法是对从 0 到 59 的分数进行显式检查，并使用`else`块处理无效的测试分数。一旦我们学会了如何使用循环，我们就可以将输入放入循环中，以便在进入`if` 语句之前检查无效的测试分数，但是我还没有讨论过循环，所以我没有以这种方式编写程序。我也可以检查范围，但是我还没有引入布尔运算符，所以我也没有使用这种技术。另外，这种技术比我在这里使用的方法效率更低。

这就是我想在这篇文章中介绍的全部内容。在本系列关于*从选项中选择*模板的第二部分中，我将介绍用于创建更复杂的逻辑表达式的逻辑操作符，并且我将介绍 switch 语句，作为有时更简洁的实现*从选项中选择*模板的方式。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。