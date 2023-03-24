# 学习 C:输入输出和两个程序模板

> 原文：<https://levelup.gitconnected.com/learning-c-input-and-output-and-two-program-templates-e944f2a91ffd>

![](img/08c938d91fa43038791bd0e7feff409d.png)

[田永光](https://unsplash.com/@kevin_mr?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在我深入 C 语言之前，我需要向您展示如何将数据输入和输出您的程序。对数据使用赋值过一段时间后就过时了，您希望能够让用户输入他们自己的数据。你肯定需要能够看到你的数据在程序中发生了什么，所以学习如何在屏幕上显示数据是重要和必要的。

除了演示如何在 C 中执行输入和输出，我还将演示与这些主题相关的两个模板— *提示符，然后阅读*和*输入、处理、输出* (IPO)。IPO 模板尤其重要，因为实际上你写的每一个 C 程序都会用到这个模板。

当我谈到 C 语言中的输入和输出时，我将使用术语标准输入和标准输出。这些术语指的是计算机上的默认输入和输出设备。标准的输入设备是键盘。标准的输出设备是计算机的显示器或屏幕。我将只使用术语输入和输出，当我使用这些术语时，我指的是标准输入和标准输出。如果我想引用不同的输入和/或输出设备，我将使用该设备的专用术语。

# 显示程序中的数据

我将从 C 如何从程序中输出数据开始。我们需要这种能力，然后才能智能地将数据输入程序。我将避免输出数据的许多细节，但是我将为您提供一些好的资源来获得更多信息。

显示程序数据的主要功能是`printf`。该功能提供了一种将*格式的*数据写入输出的方法。我说格式化是因为您可以使用`printf`函数指定输出的很多外观。

我不会在这里花费太多的篇幅来讨论所有你可以在`printf`中使用的格式选项。这里有一个[链接](https://www.cplusplus.com/reference/cstdio/printf/)到`printf`功能及其格式规范的参考指南。现在我将演示如何使用`printf`来显示简单的整数、浮点数和字符串。

不过，首先，让我向您展示我将在这些示例中使用的格式说明符:

*   %d —十进制整数(整数)
*   % n.df 浮点数，其中 n 指定数字，d 指定要显示的小数位数
*   %s —字符串

下面是一个初始化几个变量并使用`printf`显示它们的值的程序:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  int number = 100;
  double pi = 3.14159;
  printf("%d", number);
  printf("%1.5f", pi);
  printf("%s", "Hello, world!");
  return 0;
}
```

这个程序的输出是:

```
1003.14159Hello, world!
```

呃。需要有一种方法让每个输出在自己的行上开始，这就是特殊的换行符`\n`。您可以将换行符嵌入到字符串中，这样当遇到它时，就会输出一个换行符。

下面是在格式规范中添加了换行符的新程序:

```
int number = 100;
double pi = 3.14159;
printf("%d\n", number);
printf("%1.5f\n", pi);
printf("%s\n", "Hello, world!");
```

现在的输出是:

```
100
3.14159
Hello, world!
```

当然，关于使用 printf 的输出，我还可以告诉您更多的内容，但是我在这里展示的内容已经足够应付相当长一段时间了。我更感兴趣的是教你 C 语言的编程结构以及如何使用它们。您可以稍后了解如何美化您的输出。

# 将数据输入您的程序

有两种方法可以将数据输入到程序中。一种方法是一次做一个字符。我现在要跳过这个方法。相反，我将演示如何使用一个名为`scanf`的函数进行格式化输入。

`scanf`函数，正如我将在本文中介绍的，将格式规范和变量地址作为它的参数。那么什么是可变地址呢？它是一个变量，前面有一个地址运算符(`&`)，也称为指针。我不打算详细说明什么是指针或者它们是如何工作的，但是现在你需要知道的是你必须使用一个变量的地址作为`scanf`的第二个参数，以便将数据存储在变量中。

下面是`scanf`的语法模板:

*scanf(格式规范，变量地址)；*

格式规范提供了作为输入接收的数据类型的规范，其方式与格式规范使用`printf`功能的方式相同。

这里有一个例子:

```
int age;
printf("How old are you? ");
scanf("%d", &age);
printf("You are %d years old!", age);
```

这个程序的输出是:

```
How old are you? 27
You are 27 years old!
```

这是另一个例子:

```
char letter;
printf("%s", "Type a letter: ");
scanf("%c", &letter);
printf("You typed a %c.", letter);
```

这个程序的输出是:

```
Type a letter: c
You typed a c.
```

和`printf`一样，`scanf`有很多选项，但是你对函数的了解足以让你在学习 c 语言的时候有效地使用它。

# 提示，然后读取模板

当您获取用户输入时，您的程序通常会遵循以下模式:

*提示用户输入一些数据。
将数据读入你的程序。*

这是*提示，然后读*模板。我已经通过向你展示如何使用`printf`和`scanf`向你展示了两个例子，但是让我们继续看更多的例子。

这是一个很短的程序，实际上是一个程序片段，它从用户那里得到两个整数:

```
int operand1, operand2;
printf("%s", "Enter the first number: ");
scanf("%d", &operand1);
printf("%s", "Enter the second number: ");
scanf("%d", &operand2);
```

在这个例子中，*提示，然后读取*模板被实现了两次。下面是这个程序的运行:

```
Enter the first number: 12
Enter the second number: 24
```

这是另一个例子:

```
int base_number;
float rate;
printf("%s", "Enter the base number: ");
scanf("%d", &base_number);
printf("%s", "Enter the rate of growth: ");
scanf("%0.2f", &rate);
```

下面是该程序的一次运行:

```
Enter the base number: 234
Enter the rate of growth: .02
```

现在让我们使用这个模板和另一个模板来完成一个完整的程序。

# 输入、流程、输出模板

大多数，如果不是全部，你写的程序会做同样的三件事:1)输入数据到程序中；2)数据处理；以及 3)输出处理的结果。以下是描述这一过程的计划模板:

*输入数据。
处理数据。
输出数据。*

模板似乎很简单，乍一看确实如此，但是当然这个过程的每一步都可能变得非常复杂，这取决于您试图解决的编程问题的类型。

为了演示如何使用模板，这里有一个我们可以使用模板的问题。这将是一个相当简单的问题，因为我们还没有学到很多关于使用 C 的知识。问题是这样的:

让用户输入两个数字，并将这些数字存储在变量中。创建第三个变量，并将另外两个变量的和存储在第三个变量中。显示两个原始数字及其总和。

模板的输入步骤将使用提示，然后读取模板两次，将前两个数字输入程序。模板的处理步骤将涉及使用加法运算符计算两个数的和，并将其存储在第三个变量中。输出步骤是在屏幕上显示这两个数字和它们的总和(通过第三个变量)。

以下是识别模板每个步骤的程序和注释:

```
#include <stdio.h>
#include <stdlib.h>int main()
{
  /* Input step */
  int number1, number2, sum;
  printf("%s", "Enter the first number: ");
  scanf("%d", &number1);
  printf("%s", "Enter the second number: ");
  scanf("%d", &number2);/* Process step */
  sum = number1 + number2;

  /* Output step */
  printf("The sum of %d and %d is %d.",
         number1, number2, sum);
  return 0;
}
```

下面是这个程序的一次运行:

```
Enter the first number: 23
Enter the second number: 7
The sum of 23 and 7 is 30.
```

这就结束了这篇关于将数据导入和导出 C 程序的文章。在我的下一篇文章中，我将讨论如何在 c 中执行算术。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。