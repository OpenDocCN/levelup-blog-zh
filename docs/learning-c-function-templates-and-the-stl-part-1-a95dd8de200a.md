# 学习 C++:函数模板和 STL 第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-c-function-templates-and-the-stl-part-1-a95dd8de200a>

![](img/5d7bce4a144486ade387409b02bea9df.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

C++的标准模板库不是一个面向对象的库，而是一个通用库。函数从各种容器类型调用迭代器范围，而不是从对象实例调用成员函数。这些容器可以保存各种类型的数据。为了能够做到这一点，C++广泛使用了模板。在接下来的系列文章中，我将研究如何在 C++中使用模板，包括函数模板和类模板，从本文中的函数模板开始。

我对本文的大部分研究来自 David Vandevoore、Nicolai M. Josuttis 和 Douglas Gregor 所著的《C++模板:完全指南(第二版)》( T10)一书的第一章。

# 为什么需要函数模板

这里有一个简单的问题来说明为什么需要函数模板。我正在编写一系列统计或数学程序，我需要频繁地平方数字。我很自然地从编写一个函数来做平方开始:

```
int square(int number) {
  return number * number;
}
```

这对整数很有效，但对浮点数就不那么有效了，如下例所示:

```
#include <iostream>
using namespace std;int square(int number) {
  return number * number;
}int main()
{
  int num = 12;
  cout << square(num) << endl; // displays 144
  double dnum = 2.22;
  cout << square(dnum) << endl; // displays 4
  return 0;
}
```

我可以重载`square`来包含`double` s 的定义:

```
double square(double number) {
  return number * number;
}
```

但是现在我用一个重复的函数定义来占用空间。

这个问题的解决方案是使用函数模板编写 square 函数。

# 引入的功能模板

函数模板定义了一系列函数，这些函数可以对多种数据类型进行操作。数据类型是参数化的，以便程序员在运行时指定类型，或者如果没有提供类型参数，编译器将根据函数参数确定类型。

让我们通过为上面定义的`square`函数创建一个函数模板来看看这是怎么回事:

```
template <typename T>
T square(T number) {
  return number * number;
}
```

函数模板总是以关键字 template 开头，后跟*模板*参数。这些参数总是以关键字`typename`开头，后跟一个类型占位符。传统上，占位符通常被命名为`T`，但实际上它可以是任何东西。

在函数定义中，任何需要类型名的地方都用模板参数代替。在上面的`square`函数定义中，`T`用于函数返回类型的去向以及参数列表中的类型声明。在运行时，编译器会用一个实际的数据类型替换`T`，这个数据类型可以是程序提供的，也可以是编译器推导出来的。

让我们首先看一个类型演绎的例子，它使用了我在上面使用的相同程序来平方一个整数和一个浮点数。下面是将`square` 函数定义为模板函数的完整程序:

```
#include <iostream>
using namespace std;template <typename T>
T square(T number) {
  return number * number;
}int main()
{
  int num = 12;
  cout << square(num) << endl;
  double dnum = 2.22;
  cout << square(dnum) << endl;
  return 0;
}
```

这个程序的输出是:

```
144
4.9284
```

在调用函数的两种情况下，编译器通过检查函数的参数来推断要使用的类型。在第一个 output 语句中，参数是一个整数，因此返回一个整数；在第二个 output 语句中，参数是一个`double`，因此函数返回一个`double`。

如果我想明确告诉编译器应该使用什么类型，我可以在调用函数时将其作为模板参数提供。下面是一个使用显式类型参数的示例:

```
int main()
{
  int num = 12;
  cout << square<int>(num) << endl;
  double dnum = 2.22;
  cout << square<double>(dnum) << endl;
  return 0;
}
```

输出与前面的示例相同。

让我们看另一个例子。下面的函数定义是针对`maximum`的，这个函数采用两个值作为参数，并返回两个值中最大的一个。下面是函数模板定义:

```
template <typename T>
T maximum(T val1, T val2) {
  if (val1 < val2) {
    return val2;
  }
  return val1;
}
```

让我们将这个函数用于几种不同的数据类型:

```
int main()
{
  double dnum = 3.11;
  double dnum2 = 3.1;
  cout << "largest of 3.11 and 3.1: " << maximum(dnum, dnum2)
       << endl;
  int num = 123;
  int num2 = 212;
  cout << "largest of 123 and 212: " << maximum(num, num2)
       << endl;
  string word = "hello";
  string word2 = "hellos";
  cout << "largest of hello and hellos is: "
       << maximum(word, word2) << endl;
  return 0;
}
```

这个程序的输出是:

```
largest of 3.11 and 3.1: 3.11
largest of 123 and 212: 212
largest of hello and hellos is: hellos
```

但是如果我想比较不同类型的数字。我可以通过用两个模板参数重写函数来实现。

# 带有两个模板参数的模板函数

在你的编程中，你可能想要比较一个`int`和一个`double`，看看哪个是最大的。您可以通过重写`maximum`函数来获得两个模板参数。下面是函数定义:

```
template <typename T1, typename T2>
T1 maximum(T1 val1, T2 val2) {
  if (val1 < val2) {
    return val2;
  }
  return val1;
}
```

你注意到这个定义有问题吗？我必须为返回数据类型选择一个模板参数。让我们通过比较 int 和 double 来看看这是如何工作的，并在运行时为模板参数指定`<int, double>`:

```
int main()
{
  int num = 3;
  double dnum = 3.01;
  cout << "largest of 3 and 3.01 is: "
       << maximum<int, double>(num, dnum) << endl;
  return 0;
}
```

这个程序的输出是:

```
largest of 3 and 3.01 is: 3
```

这显然是不对的。我可以改变参数和模板参数的顺序:

```
int main()
{
  int num = 3;
  double dnum = 3.01;
  cout << "largest of 3 and 3.01 is: "
       << maximum<double, int>(dnum, num) << endl;
  return 0;
}
```

在我看来，这得到了正确的输出，但是破坏了使用函数模板的精神。

另一种处理方法是向模板参数添加一个输出参数。下面是`maximum`的新定义:

```
template <typename RT, typename T1, typename T2>
RT maximum(T1 val1, T2 val2) {
  if (val1 < val2) {
    return val2;
  }
  return val1;
}
```

下面是一个利用这个新定义的程序:

```
int main()
{
  int num = 3;
  double dnum = 3.01;
  cout << "largest of 3 and 3.01 is: "
       << maximum<double, int, double>(num, dnum) << endl;
  return 0;
}
```

这个程序的输出是:

```
largest of 3 and 3.01 is: 3.01
```

函数模板的这种用法更好，但还有更好的方法，尽管它更复杂。

# 使用 auto 作为返回类型

在我讨论更复杂的定义之前，解决这个问题的一个简单方法是让编译器通过对函数返回类型使用 auto 来确定数据类型。使用这种技术，我似乎可以为`maximum`写一个新的定义:

```
template <typename T1, typename T2>
auto maximum(T1 val1, T2 val2) {
  if (val1 < val2) {
    return val2;
  }
  return val1;
}
```

然而，当我试图用我们的测试程序编译它时，我得到了下面的错误:

**错误:“自动”的推导不一致:“double”和“int”**

这可以通过将函数体改为使用条件运算符来解决，如下所示:

```
template <typename T1, typename T2>
auto maximum(T1 val1, T2 val2) {
  return val1 < val2 ? val2 : val1;
}
```

# 使用尾随返回类型

从 C++11 开始，这种语言允许函数声明一个*尾随返回类型*。这意味着我可以通过从函数的返回值派生类型来确定函数的返回类型。关键字`decltype`执行这个任务。

使用`decltype`，可以定义一个以 auto 为返回类型的函数，并使用`decltype`让编译器确定数据类型。下面是对`maximum`函数的操作方法:

```
template <typename T1, typename T2>
auto maximum(T1 val1, T2 val2) -> 
  decltype(val1 < val2 ? val2 : val1) {
  return val1 < val2 ? val2 : val1;
}
```

我将函数体改为使用条件操作符，这样我就可以使用带有`decltype`的表达式。

下面是一个测试这个新版本`maximum`的程序:

```
int main()
{
  int num = 3;
  double dnum = 3.01;
  cout << "largest of 3 and 3.01 is: "
       << maximum<int, double>(num, dnum) << endl;
  return 0;
}
```

这个程序的输出是:

```
largest of 3 and 3.01 is: 3.01
```

# 使返回类型成为通用类型

C++有一个库`common_type`，它可以从可能的类型列表中选择最通用的数据类型。您可以在我们的最大值定义中使用这个库，这样当比较两个数值类型时，编译器将选择两个类型中更一般的类型。这意味着当比较一个`double`和一个`int` 时，编译器将选择`double`作为更常见的类型，因为一个整数将适合一个`double`而不是相反。该库位于标题`<type_traits>`中。

下面是为了利用`common_type`库而重写的`maximum`函数，以及头文件和测试程序:

```
#include <iostream>
#include <type_traits>
using namespace std;template <typename T1, typename T2>
typename common_type<T1, T2>::type maximum(T1 val1, T2 val2) {
  return val1 < val2 ? val2 : val1;
}int main()
{
  int num = 3;
  double dnum = 3.01;
  cout << "largest of 3 and 3.01 is: "
       << maximum<int, double>(num, dnum) << endl;
  return 0;
}
```

# 更多关于函数模板的信息

当谈到函数模板时，仍然有很多东西需要讨论，包括默认的模板参数和重载函数模板。我将在下一篇文章中讨论这些话题。

感谢您的阅读，请给我发电子邮件提出意见和建议。