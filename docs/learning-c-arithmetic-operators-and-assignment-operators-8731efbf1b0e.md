# 学习 C:算术运算符和赋值运算符

> 原文：<https://levelup.gitconnected.com/learning-c-arithmetic-operators-and-assignment-operators-8731efbf1b0e>

![](img/6c0b8f5edbfdf22269d83f3db61a2066.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将向您介绍 C 语言的算术运算符以及其他一些使 C 语言编程更容易的运算符。我先从算术开始。在文章的最后，我将编写两个利用*输入、处理、输出*模板的程序来演示所涵盖的内容。

# 算术运算符

c 的算术运算符有:

*   `+`作加法。
*   `-`为减法。
*   `*`作乘法运算。
*   `/`为师。
*   `%`为模数。

这些运算符有一个优先级顺序，其中模、除和乘的优先级高于加和减。您可以使用括号来修改优先顺序，括号具有最高的优先顺序。一个简单的例子将证明:

```
int value = 1 + 2 * 3; /*expression evaluates to 7*/
value = (1 + 2) * 3; /*expression evaluates to 9*/
```

算术运算符是二进制的，这意味着运算符的两边都必须有操作数。`+`和`—`运算符也是一元运算符，它们的意思是指定数值的符号(正或负)。

我不会回顾加法、减法和乘法运算符，因为它们完全按照预期工作，但我会花一些时间讨论除法和模数运算符。

当两个整数呈现给除法运算符时，该运算符执行整数除法。这意味着除法的结果应该是浮点值，小数部分被截断，表达式产生整数结果。

下面是一个示例代码片段:

```
int numer, denom;
numer = 5;
denom = 3;
float result = numer / denom;
```

当这个程序运行时，存储在 result 中的值是 1，而不是 1.6666。但是，如果将除法表达式的一部分作为浮点值，结果将是浮点值。

模数运算符可用于许多不同的计算。例如，您可以通过以 2 为模来测试一个数字是偶数还是奇数，或者用 C:

```
int number = 2;
int result = number % 2; /*result is 0 so number is even*/
```

另一种模数计算是数字提取。你可以通过取一个数的模 10 来提取这个数的位数。这里有一个例子:

```
int number = 123;
int ones = number % 10; /*ones gets the value 3*/
number = number / 10; /*integer division truncates the 3*/
int tens = number % 10; /*ens gets the value 2*/
number = number / 10; /*integer division truncates the 2*/
int hundreds = number; /*the 1 digit is all that is left*/
```

# 递增和递减运算符

在许多应用程序中，您希望在变量中累积一个值。您可以在循环和其他需要重复语句的程序中这样做。例如，如果您需要计算您的储蓄帐户达到某个值需要多少年，您可以像这样添加到存储计数的变量中:

```
count = count + 1;
```

程序员经常使用这种类型的语句，C 有一个特殊的操作符可以使变量递增— `++`，称为*递增操作符*。您可以通过两种方式使用增量运算符:

```
int count = 0;
++count;/*count is now 1*/
count++;/*count is now 2*/
```

当运算符以这种方式用于变量时，增量运算符的位置并不重要。但是在这样的陈述中，位置确实很重要:

```
int count = 0;
++count;/*count is now 1*/
int count2 = count++;/*count2 is 1 and count is now 2*/
```

当增量运算符用于其后缀位置时，变量的当前值用于评估语句，然后**和**的值递增。这里是相反的情况:

```
int count = 0;
++count;/*count is now 1*/
int count2 = ++count;
/*count is incremented to 2 first and 2 is assigned to count2*/
```

这称为前缀放置，当使用它时，变量的值首先递增，新值用于语句求值。

减量运算符的工作方式完全相同，只是它将变量的值减一，而不是增加一。以下是一些例子:

```
int counter = 5;
counter--; /*value is now 4*/
int temp = counter--; /* temp gets 4 then counter becomes 3*/
--temp; /*temp is now 3*/
```

这两个操作符使得变量的递增和递减变得更加容易。现在让我们看看其他一些运算符，它们对其他类型的算术做同样的事情。

# 复合赋值运算符

还有其他算术运算可以与赋值结合使用，以提高 C 语句的效率。这些被称为复合赋值运算符。他们在这里:

*   `+=`添加然后赋值。
*   `-=`减去然后赋值。
*   `*=`相乘然后赋值。
*   `/=`分而治之。
*   `%=`模数然后赋值。

以下是一些例子:

```
int number = 1;
number += 5; /*number is now 6*/
number -= 2; /*number is now 4*/
number *= 3;/*number is now 12*/
number /= 2;/*number is now 6*/
```

这些操作符除了让程序员更容易编写这种类型的语句之外，并没有给语言带来任何新的东西。你必须记住，C 是作为一种系统编程语言而设计的，在设计这种语言时，效率的考虑通常会胜过可读性和用户友好性的考虑。

对于第一个例子，我将编写一个程序来检查 ID 号是否有效。如果 id 的前三位数字加起来达到第四位数字，则 ID 号有效。为了编写这个程序，我将使用模数运算符从 ID 中提取每个数字。我还将强调与*输入、流程、输出*模板相关的每个步骤。

程序是这样的:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  /* Input step */
  int id_number, digit1, digit2, digit3, digit4, check_sum;
  printf("%s", "Enter the ID number: ");
  scanf("%d", &id_number);

  /* Process step */
  int temp_number = id_number;
  digit4 = temp_number % 10;
  temp_number /= 10;
  digit3 = temp_number % 10;
  temp_number /= 10;
  digit2 = temp_number % 10;
  temp_number /= 10;
  digit1 = temp_number; /* Output step with some processing */
  if ((digit1 + digit2 + digit3) == digit4) {
    printf("%s", "The ID number is valid.\n");
  }
  else {
    printf("%s", "The ID number is not valid.\n");
  }
  return 0;
}
```

这个程序的输出是(两次运行):

```
Enter the ID number: 1236
The ID number is valid.Enter the ID number: 1237
The ID number is not valid.
```

我使用了一个我还没有讨论过(我的下一篇文章)的构造(`if`语句),但是你应该能够理解这个逻辑。

这个例子还演示了程序模板中的步骤有时可以合并，因为我的 if 语句不仅进行一些处理，而且还输出一条关于输入的 ID 号的有效性的消息。

下一个程序提示用户输入摄氏温度，然后将温度转换为华氏温度。下面是公式:华氏= (9.0 / 5.0) *摄氏+ 32.0。

程序是这样的:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  /* Input step */
  float celsius, fahrenheit;
  printf("%s", "Enter the Celsius temperature: ");
  scanf("%f", &celsius); /* Process step */
  fahrenheit = (9.0 / 5.0) * celsius + 32.0; /* Output step */
    printf("%.2f Celius is equivalent to %.2f Fahrenheit.\n",
           celsius, fahrenheit);
  return 0;
}
```

这个程序的输出是:

```
Enter the Celsius temperature: 0
0.00 Celsius is equivalent to 32.00 FahrenheitEnter the Celsius temperature: 100
100.00 Celsius is equivalent to 212.00 Fahrenheit.
```

我对 C 中各种算术和赋值操作符的讨论到此结束。在下一篇文章中，我将介绍从选项中选择的*程序模板，以及如何使用 C 的`if`语句实现该模板。*

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。