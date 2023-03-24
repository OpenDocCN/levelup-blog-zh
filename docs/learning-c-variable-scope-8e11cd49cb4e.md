# 学习 C++:可变范围

> 原文：<https://levelup.gitconnected.com/learning-c-variable-scope-8e11cd49cb4e>

![](img/b753ef0192b3c212907fadd995900b3f.png)

照片由[丹尼·阿维拉](https://unsplash.com/@dannyavilamedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

变量作用域指的是变量在程序中“可见”的能力。如果在程序的特定位置，程序员可以访问变量的值，那么这个变量就是可见的。C++程序中有不同的作用域级别，作用域级别决定了一个变量在程序中的可见范围。在这篇文章中，我将讨论变量作用域的不同层次，以及它们为什么重要。

# 范围级别

C++程序中有三个层次的作用域:全局、局部和块。Global 是最大的作用域，意味着用 global 作用域声明的任何变量都可以在程序中的任何地方看到。局部作用域通常指函数中定义的变量，在整个函数中都可以看到，但在其他函数中看不到。块范围意味着一个变量被定义在一个代码块中，比如一个`for`循环或者一个`if`语句。

抛开这些定义，让我们看看这些不同的作用域级别是如何影响 C++程序的。

# 全球范围

具有全局范围的变量可以在 C++程序的任何其他部分看到，从函数定义到代码块。在主函数之外定义的变量是全局变量。

下面是一个演示全局范围如何工作的示例:

```
#include <iostream>
using namespace std;// global spaceint number = 1;void showValue() {
  cout << "Number accessed from a function: " << number << endl;
}int main ()
{
  showValue();
  cout << "Number accessed from the main function: "
       << number << endl;
  for (int i = 1; i <= 1; i++) {
    cout << "Number accessed from a block: " << number << endl;
  }
  return 0;
}
```

以下是该程序的输出:

```
Number accessed from a function: 1
Number accessed from the main function: 1
Number accessed from a block: 1
```

全局变量的主要问题是，因为它们可以从程序中的任何地方访问，所以它们可以在程序中的任何地方更改，这导致了难以发现的逻辑错误。当程序使用全局变量时，它也使得阅读和理解程序变得更加困难，因为读者不得不经常回到全局空间去查看变量是从哪里产生的。

# 局部变量

局部变量是在函数定义中定义的变量。函数中定义的变量仅对函数定义中的代码可见。该变量被称为函数的“局部”变量。

下面是一个演示局部作用域如何工作的示例:

```
// global spaceint number = 1;void defineNumber() {
  int number = 2;
  cout << "Number accessed from defining function: "
       << number << endl;
}int main ()
{
  cout << "Accessing global number: " << number
       << endl; // this is the global variable
  defineNumber(); // this is the local version
  number = 2; // changing global variable
  cout << "Accessing global number: " << number
       << endl; // this is the global variable
  return 0;
}
```

以下是该程序的输出:

```
Accessing global number: 1
Number accessed from defining function: 2
Accessing global number: 2
```

函数`defineNumber`定义了一个名为 number 的变量，但是是在局部范围而不是全局范围。`main`中的代码访问第一个输出语句的全局编号，调用`defineNumber`函数来显示该编号变量的值，然后给全局编号赋一个新值。

这个例子演示了使用全局变量时可能引起的混乱。

# 块范围

第三个作用域级别是块作用域。代码块中定义的变量只能在该代码块内部看到。块是任何用花括号括起来的代码。当然，全局空间和函数定义都有块，但是对于块级别的范围，我们通常讨论更紧密的代码空间，比如 for 循环。

以下简短程序演示了全局、本地和数据块范围之间的差异:

```
// global spaceint number = 1;int main ()
{
  cout << "Global number: " << number << endl;
  int number = 2;
  cout << "Local number: " << number << endl;
  {
    int number = 3;
    cout << "Block number: " << number << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
Global number: 1
Local number: 2
Block number: 3
```

`main`中的代码演示了你可以创建一个仅仅是代码的块，它不必附加到一个结构上，比如一个循环。

# 对于循环和块范围

块级作用域最重要的用途之一是约束循环控制变量的值。很多时候你会看到一个`for` 循环是这样写的:

```
int main ()
{
  const int numElements = 5;
  int grades[numElements] = {81, 77, 91, 88, 78};
  int i = 0;
  for (; i < numElements; i++) {
    cout << grades[i] << " ";
  }
  return 0;
}
```

不管出于什么原因，作者没有选择在 For 循环中利用块级范围。这意味着变量`i` 在循环外是可见的和可访问的，并且在没有重新赋值的情况下不能再次使用。

更好的方法是通过在循环内部初始化循环控制变量，使其始终处于块级别:

```
int main ()
{
  const int numElements = 5;
  int grades[numElements] = {81, 77, 91, 88, 78};
  for (int i = 0; i < numElements; i++) {
    cout << grades[i] << " ";
  }
  return 0;
}
```

通过将循环控制变量`i`定义为块范围的变量，我也可以使它的长度为一个字母。这被认为不是好的编程实践，但是在短块中是可以接受的，比如这个块，其中变量的初始化语句接近于它在程序中的用法。

# 函数参数的范围

函数参数具有局部范围。这意味着它们在函数定义中是可见的，而在其他地方是不可见的。这非常有意义，因为函数定义必须能够访问其参数值，以便在函数中使用它们。

# 如何使用可变范围

作为一名程序员，你的目标是在不荒谬的情况下，将变量控制在你能控制的最严格的范围内。许多变量必然会被定义为局部变量，但你应该尽可能在块级别定义变量。当然，for 循环控制变量应该在块中定义，其他地方不需要的其他循环变量也应该定义为块变量。

然而，在块级定义`if`语句中的变量时要小心。您经常会收到来自编译器的错误消息，指出在`if`中定义的一个或多个变量不可达。这是因为如果被测试的条件不为真，并且变量声明被放在了`if`语句的那一部分，那么变量就不能被使用。

变量作用域有时会让初学编程的学生感到困惑，但这是一个非常重要的话题，也是一个你在编程教育过程中应该确保理解的话题。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。