# 学习 c++:STL 和数组类

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-the-array-class-f35a1dcb8663>

![](img/26ba789de9bdd251c7bce5676098683b.png)

照片由 [Nico Giras](https://unsplash.com/@nicogiras?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

C++的内置(原始)数组有一些缺陷，这使得它很难在许多程序中使用。原始数组的一个主要问题是没有返回数组大小的函数。这意味着当你需要数组的大小来控制 for 循环时，你必须使用存储数组大小的变量或常量。原始数组的另一个问题是，你不能把数组传递给一个函数，你还必须传递它的大小。不能在函数中使用带有原始数组的 range for 循环，因为函数不能访问设置迭代器所需的信息。

使用原始数组的一个解决方案是使用属于标准模板库(STL)及其容器集的`array`类。在这篇文章中，我将介绍如何在你的 C++程序中使用这个类。

# array 类的功能

`array`类是 STL 中的一个模板类。它被认为是 STL 容器类中的一个，所以它有许多与其他 STL 容器相同的特性，比如`vector`类。如果你需要一个固定的数字序列，那么应该选择`array`类而不是其他 STL 容器，因为像原始数组一样，`array`类提供随机访问，并且用于存储其元素的内存是在堆栈上分配的，因为不需要像`vector`类那样重新分配内存。

# 创建数组类对象

创建新的`array`对象有几种方法。一种方法是用数据类型和大小(元素数量)来声明对象，就像这样:

```
array<int, 10> numbers;
```

`numbers`数组被分配来存储 10 个整数。该对象还自动初始化为`int`数据类型的默认值，因此数组中存储了 10 个零。

您也可以使用初始化列表用数据初始化一个新的`array`对象:

```
array<int, 5> numbers {1,2,3,4,5};
```

如果您声明了一个大小和一个初始值设定项列表，但没有提供该大小的数据，则其余元素将被设置为数据类型的默认值:

`array<int, 10> numbers {1,2,3,4,5};`

可以通过赋值(复制构造函数)从另一个`array` 对象创建一个新的`array`对象:

```
array<int, 10> numbers {1,2,3,4,5};
array<int, 10> nums = numbers;
```

# 尺寸函数

在进入`array` 类操作之前，我需要介绍一个许多数组操作都需要的重要函数——函数`size`。正如我在本文开头提到的，原始数组的一个问题是它们没有一个`size`函数或其他返回数组大小的函数。`array`类确实有一个`size`函数，它应该在你访问`array`类对象的任何时候使用。在接下来的部分中，我将演示如何使用这个函数。

# 访问数组元素

访问数组元素最明显的方法是使用下标符号，如下例所示:

```
#include <iostream>
#include <array>
using namespace std;int main()
{
  array<int, 5> numbers {1,2,3,4,5};
  for (unsigned i = 0; i < numbers.size(); i++) {
    cout << numbers[i] << " ";
  }
  return 0;
}
```

您也可以使用`at` 功能代替`[]`运算符:

```
for (unsigned i = 0; i < numbers.size(); i++) {
  cout << numbers.at(i) << " ";
}
```

当然，你也可以使用一个范围`for`循环，完全避免`size`功能:

```
for (int n : numbers) {
  cout << n << " ";
}
```

数组对象可用的两个特殊访问函数是`front`和`back`。这些函数分别返回数组的第一个元素和最后一个元素:

```
array<int, 5> numbers {1,2,3,4,5};
cout << "The first element is: " << numbers.front() << endl;
cout << "The last element is: " << numbers.back() << endl;
```

# 数组对象使用迭代器

您可以使用迭代器来遍历数组对象。标准迭代器函数:`begin`、`end`、`rbegin`和`rend` 可用。下面是一个程序中这些函数的工作示例，该程序从头到尾显示一个数组，然后以相反的顺序显示:

```
int main()
{
  array<string, 4> beatles =
    {"John", "Paul", "George", "Ringo"};
  cout << "The Beatles in order of joining the group: "
       << endl << endl;
  for (auto iter = beatles.begin(); iter != beatles.end();
       iter++) {
    cout << *iter << endl;
  }
  cout << endl << "The Beatles in reverse order of joining: "
       << endl << endl;
  for (auto riter = beatles.rbegin(); riter != beatles.rend();
       riter++) {
    cout << *riter << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
The Beatles in order of joining the group:John
Paul
George
RingoThe Beatles in reverse order of joining:Ringo
George
Paul
John
```

迭代器的另一个用途是在 STL 算法中。下面是一个对数字进行排序的程序:

```
#include <iostream>#include <array>#include <algorithm>using namespace std;int main()
{
  array<int, 10> numbers {6,1,10,5,9,2,8,3,7,4};
  for (int n : numbers) {
    cout << n << " ";
  }
  cout << endl << endl;
  sort(numbers.begin(), numbers.end());
  for (int n : numbers) {
    cout << n << " "; // 1 2 3 4 5 6 7 8 9 10
  }
  return 0;
}
```

# 数组相对于原始 C++数组的优势

在本文的前面，我提到了使用`array`类优于原始数组的两个优点。这两个原因都围绕着这样一个概念，即`array`类具有`size`函数，可用于您希望在数组末尾停止的循环中。传递给函数的原始数组必须与它们的大小一起传递，因为不能在函数中的原始数组上使用 range `for`循环，因为函数不能访问正确的信息来使用 range `for`循环。

下面是一个不起作用的例子:

```
void computeAverage(int arr[]) {
  for (int n : arr) {
    cout << n << " ";
  }
}int main()
{
  const int SIZE = 5;
  int grades[SIZE] = {88, 71, 91, 85, 93};
  printArray(grades);
  return 0;
}
```

错误如下:“begin 未在此范围内声明。”编译器不能使用`begin`函数创建迭代器，因为数组是在`main`函数中创建的，在`computeAverage`函数的作用域之外。

不过，您可以在这个程序中成功地使用一个`array`对象:

```
#include <iostream>
#include <array>
using namespace std;void printArray(auto arr) {
  for (int n : arr) {
    cout << n << " ";
  }
}int main()
{
  array<int, 5> grades = {88, 71, 91, 85, 93};
  printArray(grades); // displays 88 71 91 85 93
  return 0;
}
```

请注意，我使用了`auto`关键字来键入函数参数，以便编译器可以确定如何在运行时处理该参数。

# 最终建议

STL `array`类提供了与原始数组相同的功能，并且提供了许多便利，这使得它在您需要一个可以随机访问的顺序容器时更加有用。当然，这并不否定对大多数应用程序在任何类型的数组上使用`vector`的更一般的建议，因为向量比数组(任何类型)具有更大的灵活性，除非您存储的是大型数据集，并且需要数组提供的随机访问效率。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。