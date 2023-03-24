# 学习 C:从备选方案中选择模板第 2 部分

> 原文：<https://levelup.gitconnected.com/learning-c-the-select-from-alternatives-template-part-2-9ddfaaa6b968>

![](img/5d390e6af3358dd975b50a796a0322eb.png)

照片由[粘土银行](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这是关于在 c 中实现 *Select from Alternatives* 模板的两部分系列文章的第二部分。在这一部分中，我将讨论嵌套的`if`语句、布尔运算符和`switch`语句。

# 嵌套的 if 语句

有时，您需要将`if`语句嵌套在其他`if`语句中，以最好地表达程序解决方案的逻辑。这可能是一项棘手的技术，你需要小心处理嵌套的`if`语句，并彻底调试它们。

当我谈到嵌套的`if`语句时，我会提到外部的`if`和内部的`if`语句。外层的`if`是第一个比较，然后其他比较嵌套在外层的`if`语句中。

这里有一个需要嵌套`if`语句解决的问题。您正在编写一个处理汽车贷款的应用程序。客户获得汽车贷款资格的第一个要求是他们目前有工作。检查该要求成为外部`if`语句。然后，如果客户被雇用，他们也必须赚一定数量的钱，比如每年 10000 美元。对该要求的检查成为内部`if`语句。

下面是用 C 语言解决上述问题的一种方法:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
   char employed;
   int salary;
   printf("Are you currently employed (Y/N)? ");
   scanf("%c", &employed);
   if (employed == 'Y') {
      printf("What is your current salary? ");
      scanf("%d", &salary);
      if (salary >= 10000) {
         printf("Congratulations. You are qualified for a 
                 loan.\n");
      }
      else {
         printf("Sorry. You don't make enough money for a 
                loan.\n");
      }
   }
   else {
      printf("Sorry. You do not qualify for a loan because you 
             are not employed.\n");
   }
   return 0;
}
```

下面是运行该程序三次以测试每种可能性的输出:

```
Are you currently employed (Y/N)? Y
What is your current salary? 11000
Congratulations. You are qualified for a loan.Are you currently employed (Y/N)? Y
What is your current salary? 9000
Sorry. You don't make enough money for a loan.Are you currently employed (Y/N)? N
Sorry. You do not qualify for a loan because you are not employed.
```

# 布尔运算符

解决编程问题需要很多逻辑，而这些逻辑不能仅仅用关系运算符来表达。例如，汽车贷款申请可能使用以下条件作为对申请人的初步筛选:有工作且工资至少为 10，000.00 美元。

或者另一个条件可能是:就业或有人愿意共同签署贷款。

这些条件可以用 C 语言中的布尔运算符来表示。布尔运算符包括:

*   `&&`还有
*   `||`或者
*   `!`不

`&&`和`||`运算符是二元运算符，而`!`运算符是一元运算符。

`&&`和`||`操作符测试两个关系表达式。当且仅当两个表达式都为真时，`&&`操作符将返回真值，而如果任一关系表达式为真，`||`操作符将返回真值。

现在举几个例子。第一个例子将代码放到我在本节介绍中给出的第一个例子中，利用`&&`操作符来决定用户是否有资格获得贷款:

```
int main()
{
   char employed;
   int salary;
   printf("Are you currently employed (Y/N)? ");
   scanf("%c", &employed);
   printf("What is your salary? ");
   scanf("%d", &salary);
   if (employed == 'Y' && salary >= 10000) {
      printf("You qualify for a loan.\n");
   }
   else {
      printf("You do not qualify for a loan.\n");
   }
   return 0;
}
```

下面是该程序三次运行的输出:

```
Are you currently employed (Y/N)? Y
What is your salary? 11000
You qualify for a loan.Are you currently employed (Y/N)? Y
What is your salary? 9000
You do not qualify for a loan.Are you currently employed (Y/N)? N
What is your salary? 0
You do not qualify for a loan.
```

最后一个例子演示了为什么您会选择使用嵌套的`if`来解决这类问题。

现在让我们在本节介绍中给出的第二个例子中测试一下`||`操作符。程序是这样的:

```
int main()
{
   char employed;
   char co_signer;
   printf("Are you currently employed (Y/N)? ");
   scanf("%c", &employed);
   printf("Do you have a co-signer? ");
   scanf("%c", &co_signer);
   if (employed == 'Y' || co_signer == 'Y') {
      printf("You are qualified for a loan.\n");
   }
   else {
      printf("You do not qualify for a loan.\n");
   }
   return 0;
}
```

但是看看当我运行这个程序时会发生什么:

```
Are you currently employed (Y/N)? Y
Do you have a co-signer? You are qualified for a loan.
```

跳过了第二个提示。问题是第二个`scanf` 函数“消耗”了用户按 Enter 键时输入的换行符。这个问题的一个解决方案是将`getchar`函数放在`scanf`函数之间来消耗换行符。这是新的程序:

```
int main()
{
   char employed;
   char co_signer;
   printf("Are you currently employed (Y/N)? ");
   scanf("%c", &employed);
   printf("Do you have a co-signer? ");
   getchar();
   scanf("%c", &co_signer);
   if (employed == 'Y' || co_signer == 'Y') {
      printf("You are qualified for a loan.\n");
   }
   else {
      printf("You do not qualify for a loan.\n");
   }
   return 0;
}
```

以下是该程序三次运行的输出:

```
Are you currently employed (Y/N)? Y
Do you have a co-signer? N
You are qualified for a loan.Are you currently employed (Y/N)? N
Do you have a co-signer? Y
You are qualified for a loan.Are you currently employed (Y/N)? N
Do you have a co-signer? N
You do not qualify for a loan.
```

只有在第三个例子中，当两个提示的答案都是 N 时，用户才被确定没有资格获得贷款。

# switch 语句

实现 *Select from Alternatives* 模板的另一种方法是使用`switch`语句。与`if`语句相比，`switch`语句有一定的局限性，但是在某些情况下，当从备选方案中进行选择时，它是首选的构造。

以下是`switch`语句的语法模板:

*switch(integer expression){
case(constant expression){
语句；
破；
case(constant expression){
语句(s)；
破；
…
默认:
语句；
}*

`switch`语句首先评估一个必须评估为整数值的表达式。下面的每个 case 子句将子句中的常数与顶部的整数表达式进行比较，以确定是否相等。如果比较结果为真，则执行`case`块内的语句，并将控制权转移给`switch`语句外的语句。关键字`break`导致程序完全退出`switch`语句。如果比较结果为假，控制转到下一个`case`子句。

如果没有一个比较结果为真，并且存在一个 `default`子句(`default`子句是可选的)，则执行`default`子句中的语句。

与`if`语句相比，`switch`语句受到限制，因为只有整数表达式可以用于比较，这意味着您不能使用字符串进行比较，尽管您可以使用`char`，因为 `char`变量是作为整数值存储的。

让我们看一个例子。在下面的程序中，用户输入两个数字，然后出现一个菜单来选择要执行的算术运算:加、减、乘或除。

程序如下:

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>int main()
{
   srand(time(0));
   int operand1 = rand() % 100 + 1;
   int operand2 = rand() % 100 + 1;
   int choice;
   printf("The numbers are %d and %d\n", operand1, operand2);
   printf("1\. Addition\n");
   printf("2\. Subtraction\n");
   printf("3\. Multiplication\n");
   printf("4\. Division\n");
   printf("\n");
    printf("Choose an operation: ");
   scanf("%d", &choice);
   return 0;
}
```

以下是该程序几次运行的输出:

```
The numbers are 97 and 9
1\. Addition
2\. Subtraction
3\. Multiplication
4\. DivisionChoose an operation: 1
97 + 9 = 106The numbers are 82 and 23
1\. Addition
2\. Subtraction
3\. Multiplication
4\. DivisionChoose an operation: 2
82 - 23 = 59The numbers are 64 and 90
1\. Addition
2\. Subtraction
3\. Multiplication
4\. DivisionChoose an operation: 3
64 x 90 = 5760The numbers are 32 and 23
1\. Addition
2\. Subtraction
3\. Multiplication
4\. DivisionChoose an operation: 4
32 / 23 = 0.000000
```

啊哦。执行除法时的最后一个输出不正确。32 除以 23 应该是 1.3913 而不是 0.00。发生了什么事？事情是这样的:除法运算符被赋予两个整数，因此它执行整数除法。我们需要修改除法语句，以便除法运算符的至少一个操作数是浮点数。

为此，将`printf`功能中的除法语句改为:

```
(float)operand1 / operand2
```

下面是执行除法的输出:

```
The numbers are 88 and 36
1\. Addition
2\. Subtraction
3\. Multiplication
4\. DivisionChoose an operation: 4
88 / 36= 2.444444
```

从整数除法表达式中获得浮点结果的唯一方法是将表达式的至少一部分转换(即使是临时转换)为浮点值。

这就结束了我对使用 if 语句或 switch 语句实现 *Select from Alternatives* 模板的讨论。在我的下一篇文章中，我将继续讨论下一个重要的 C 编程结构——循环，在这里我将讨论三种类型的循环语句——`while`语句、`do-while`语句和`for`语句。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。