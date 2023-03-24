# 学习 C++: 5 带注释的变量声明和初始化问题解决方案

> 原文：<https://levelup.gitconnected.com/learning-c-5-variable-declaration-and-initialization-problems-with-annotated-solutions-b98b99b9707c>

![](img/5696dd1c13180cd6555d14993dd9255c.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这个系列中，我将提出 C++不同领域的 5 个问题，然后提供解决方案以及我对解决方案的解释。在寻找解决方案之前，你应该先尝试这些问题，以最大化你的学习努力。

对于第一个问题集，这里有 5 个关于声明变量和用数据初始化变量的问题需要解决。

# 问题 1

为`integer`、`double`、`string`和`bool` 数据声明变量。这些应该只是声明语句。声明变量后，编写赋值语句将数据赋给每个变量。

# 问题 2

重写问题 1 的解决方案，使声明语句也包括对变量的数据赋值(初始化)。

# 问题 3

在同一行中声明三个整型变量并给它们赋值，这样只需声明一次数据类型。

# 问题 4

使用变量初始化的消息传递方式为`integer`、`double`、`string`和`bool`数据声明和初始化变量。

# 问题 5

使用花括号重写问题 5 的解决方案，并解释为什么这是用数据初始化变量的好方法。

# 问题 1 解决方案

这是创建变量并将数据赋给变量的最直接的方式。下面是这个问题的一组解决方案:

```
int intNumber;
double dblNumber;
string strngVar;
bool boolVar;
intNumber = 100;
dblNumber = 2.22;
strngVar = "Hello, world!";
boolVar = false;
```

如果您打算按照这种模式来声明变量，然后将数据赋给变量，那么您应该在一条语句中完成，如问题 2 的解决方案所示。

# 问题 2 解决方案

当您预先知道要存储在变量中的初始数据时，在单个语句中声明和初始化变量是将数据分配给变量的首选方式。这种方法节省空间和阅读时间。

下面是问题 2 的解决方案:

```
int intNumber = 100;
double dblNumber = 2.22;
string strngVar = "Hello, world!";
bool boolVar = false;
```

# 问题 3 解决方案

当声明相同数据类型的变量时，可以在同一行上声明和初始化它们，方法是在每个变量初始化之间加逗号。你可以这样做:

```
string first = "John", middle = "Michael", last = "Higgins";
int grade1 = 88, grade2 = 100, grade3 = 77;
bool flag1 = true, flag2 = false;
double rat1 = 1.22, rat2 = 2.11, rat3 = 0.117;
```

# 问题 4 解决方案

初始化变量的消息传递方式要求您将新变量的值放在括号中，就像它是传递给函数的数据一样。这不是一种常见的初始化技术，但是您偶尔会在代码中看到它。

下面是问题 4 的一个解决方案:

```
int salary(5000);
double cpdFactor(2.75);
string greeting("Hello!");
bool result(true);
```

# 问题 5 解决方案

使用花括号初始化变量从 C++11 开始就有了，除了在更复杂的程序中，仍然很少使用。然而，使用花括号有两个好处。第一个优点是，编译器会对您赋给变量的数据进行类型检查，以确保数据类型匹配。当将整数或双精度数赋给另一种类型的变量时，这有助于避免收缩和扩大转换。

第二个优点是，当你用空花括号初始化一个变量时，编译器会推导出该类型的默认值，并将其赋给该变量。

考虑到这些优势，下面是问题 5 的解决方案:

```
int salary{5000};
double cpdFactor{2.75};
string greeting{"Hello"};
bool result{true};
```

至于我之前提到的第二个优势，这里有两个例子:

```
int number{}; // compiler assigns 0 to variable
string name{}; // compiler assigns empty string to variable
```

当你开始使用模板时，随着你对 C++的经验越来越多，以这种方式使用花括号确实是一种优势。

# 更多的问题和解决方案即将出现

这只是我将要写的许多文章的第一篇，这些文章给你提供了练习 C++学习的机会。如果你喜欢这篇文章，请让我知道，并通过电子邮件给我评论和建议。