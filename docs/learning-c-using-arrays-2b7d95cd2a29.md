# 学习 C++:使用数组

> 原文：<https://levelup.gitconnected.com/learning-c-using-arrays-2b7d95cd2a29>

![](img/86915de8031a1592f16e23ea83f1211e.png)

由[兹比内克·布里瓦尔](https://unsplash.com/@zburival?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

数组是计算机科学中最常见的数据结构，因为它是每种编程语言的一部分，尽管在某些语言中它可能被称为其他东西，如列表。在这篇文章中，我将讨论如何在 C++中创建和使用数组。我将把我的讨论限制在一维数组，我将在以后的文章中讨论多维数组。

# 定义的数组

数组是一组连续的内存地址，用于存储特定数据类型的数据。数组必须用名称、数据类型和大小来声明，即数组中要存储的数据元素的数量。

在 C++中，数组大小是固定的，当程序需要比最初分配的更多的存储空间时，这会产生问题。

通过元素在数组中的索引位置来访问数组元素。数组元素从位置 0 开始排序，最后一个元素存储在比数组中元素数小 1 的索引位置。

# 声明数组

正如我上面提到的，数组是用数据类型、名称和大小来声明的。通常为数组的大小指定一个常数，因为任何使用数组的程序都需要多次引用数组的大小。

您可以声明一个数组，而不指定要存储在数组中的数据，或者您可以提供一个包含数组数据的初始化列表。

下面是第一种数组声明的语法模板:

*数据类型数组名【元素个数】；*

下面是第二种数组声明的语法模板:

*数据类型 array-name[(number-of-elements)]= {逗号分隔数据列表}；*

元素数量的说明在圆括号中，因为当提供初始化列表时你可以省略它，编译器将通过计算列表中元素的数量来确定数组的大小。

以下是数组声明的第一种形式的一些示例:

```
const int numElements = 10;
int grades[numElements];
string names[numElements];
bool flags[numElements];
char letters[numElements];
```

现在让我们看看一些使用初始化列表的声明:

```
const int numElements = 5;
int numbers[numElements] = {1,2,3,4,5};
const int numNames = 3;
string names[] = {"Cynthia", "Jonathan", "Raymone"};
cont int numFlags = 4;
bool flags[] = {false, true, true, false};
```

我写了几个声明，把元素的数量放在括号里，让编译器决定元素的数量。但是，即使您选择让编译器确定数组中的元素数量，您仍然应该用数组中的元素数量声明一个常数。

# 访问数组

通过使用`[]`操作符指定想要的元素的索引位置来访问数组的元素。例如，下面是一些将一些数据分配到数组中的语句:

```
numbers[0] = 101;
numbers[1] = 103;
numbers[2] = 105;
```

也使用`[]`操作符从数组中访问存储的数据。例如:

```
int subtotal = numbers[0] + numbers[1] + numbers[2];
```

这里是数组访问的另一个例子，这次使用一个`for`循环用一组数字初始化一个数组:

```
#include <iostream>
using namespace std;int main()
{
  const int numElements = 5;
  int numbers[numElements];
  for (int i = 0; i < numElements; i++) {
    numbers[i] = i + 1;
  }
  return 0;
}
```

下面是另一个使用`for`循环的例子，这次访问数组是计算平均值过程的一部分:

```
int main()
{
  const int numElements = 5;
  int grades[numElements] = {81, 78, 91, 77, 66};
  int total = 0;
  for (int i = 0; i < numElements; i++) {
    total += grades[i];
  }
  double average = static_cast<double>(total) / numElements;
  cout << "The average grade is: " << average << endl;
  return 0;
}
```

以下是该程序的输出:

```
The average grade is: 78.6
```

因为这是一篇介绍性文章，所以我使用了`int`来初始化 for 循环控制变量。如果这是一篇更高级的文章，我会用`size_t`来代替。

我还应该提到，当您想要访问每个元素时，从头到尾遍历一个数组的最佳方式是使用 range `for`循环。考虑到这一点，下面是重新编写的程序:

```
int main()
{
  const int numElements = 5;
  int grades[numElements] = {81, 78, 91, 77, 66};
  int total = 0;
  for (auto grade : grades) {
    total += grade;
  }
  double average = static_cast<double>(total) / numElements;
  cout << "The average grade is: " << average << endl;
  return 0;
}
```

# 作为函数参数的数组

我强调了一点，你应该总是声明一个常量来保存数组中元素的数量。您需要这样做的第一个原因是，当您编写一个 index `for`循环来遍历一个数组时，您将有一个常量可以让循环知道何时停止。

这很重要，因为 C++数组没有边界检查，这意味着您可能会意外地访问数组外部的内存而不会触发错误。正如某个名人曾经说过的，“C++给你的绳子刚好够你上吊。”当这种情况发生时，新的语言会抛出一个异常，但是 C++只是假设这就是你想要做的。

让常量保存数组元素数量的另一个原因是当您想要将数组传递给函数时。不能使用 range `for`循环从函数体内访问数组元素，所以必须使用 index `for`循环。这意味着您需要知道何时停止循环，常量参数提供了该值。

知道了这一点，我就可以向你展示如何将数组传递给函数。我将用一个计算平均值的函数重写上面的分数平均程序:

```
double gradeAvg(int arr[], int arraySz) {
  int total = 0;
  for (int i = 0; i < arraySz; i++) {
    total += arr[i];
  }
  double average = static_cast<double>(total) / arraySz;
  return average;
}int main()
{
  const int numElements = 5;
  int grades[numElements] = {81, 78, 91, 77, 66};
  int total = 0;
  double average = gradeAvg(grades, numElements);
  cout << "The average grade is: " << average << endl;
  return 0;
}
```

这个程序的输出是:

```
The average grade is: 78.6
```

数组也可以从函数中返回，但是这需要指针，所以我将不得不把这个讨论留到以后。

# 数组很棒，但向量可能更好

虽然我在这篇文章中解释了如何使用数组，但是大多数 C++程序员现在使用向量作为存储顺序数据的首选数据结构。这有几个原因，包括标准模板库中的 vector 类实现现在非常高效，可能最重要的是，vector 可以根据需要增长和收缩。在我的下一篇文章中，我将讨论如何在 C++中使用向量，你将看到它们相对于数组的优势。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线计算机编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。