# 学习 c++:STL 的数值算法

> 原文：<https://levelup.gitconnected.com/learning-c-numerical-algorithms-of-the-stl-eaa687d98fd3>

![](img/38ad3372409576c52edc89e3926c1de5.png)

约翰·莫塞斯·鲍恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在这篇文章中，我将讨论标准模板库(STL)中的数值算法。这些算法并没有取代`cmath`中的函数类型的数学库，而是提供了一套不同的算法来解决某些数值处理问题。

# 累加函数

一旦开始编程的学生学会了如何在数组中存储一些数据，他们就会被要求编写一个程序来对数组的元素求和。下面是这样一个程序的例子:

```
int main () {
  const int SZ = 5;
  int numbers[SZ] = {2, 27, 12, 14, 1};
  int sum = 0;
  for (int i = 0;i < SZ; i++) {
    sum += numbers[i];
  }
  cout << "The sum is: " << sum << endl;
  return 0;
}
```

现在您应该知道，以这种方式编写循环是容易出错的，因为程序有可能超出数组的界限。这个程序的一个改进是使用一个 range `for` 循环并将数据放入一个向量中，就像这样:

```
int main () {
  const int SZ = 5;
  vector<int> numbers = {2, 27, 12, 14, 1};
  int sum = 0;
  for (const int number : numbers) {
    sum += number;
  }
  cout << "The sum is: " << sum << endl;
  return 0;
}
```

这是一个改进，但是我们还可以做一个改进——完全去掉循环，使用`accumulate`函数。

`accumulate`函数将一个容器起始范围和一个容器结束范围以及一个起始值作为参数，并且在其第一种形式下将产生指定范围内元素的总和。在第二种形式中，它将一个函数作为第四个参数，并使用该函数计算一个值。我将在下面演示这是如何工作的。

下面是两种形式的`accumulate`的语法模板:

*T accumulate(范围开始，范围结束，初始值)；
T accumulate(范围开始，范围结束，初始值，二元函数)；*

返回类型 *T* 意味着返回类型将在运行时根据函数调用中给出的模板参数决定。

让我们从一个使用加法的例子开始:

```
#include <iostream>
#include <random>
#include <algorithm>
#include <ctime>
using namespace std;void buildVec(vector<int> &vec) {
  uniform_int_distribution<int> distro(1,20);
  default_random_engine engine(time(0));
  for (int i = 1; i <= 10; i++) {
    vec.push_back(distro(engine));
  }
}void printVec(vector<int> &vec) {
  for (const int n : vec) {
    cout << n << " ";
  }
}int main () {
  vector<int> numbers;
  buildVec(numbers);
  printVec(numbers);
  cout << endl << endl;
  int sum = 0;
  sum = accumulate(numbers.begin(), numbers.end(), 0);
  cout << "The sum is: " << sum << endl;
  return 0;
}
```

这个程序运行一次的输出是:

```
11 13 17 5 3 18 4 3 2 4The sum is: 80
```

现在让我们计算一个向量的乘积。为此，我们将使用`multiplies<int>`函数对象来改变函数的默认行为:

```
int main () {
  vector<int> numbers;
  buildVec(numbers);
  printVec(numbers);
  cout << endl << endl;
  int product = 1;
  product = accumulate(numbers.begin(), numbers.end(),
                       product, multiplies<int>());
  cout << "The product is: " << product << endl;
  return 0;
}
```

这个程序运行一次的输出是:

```
11 3 11 10 5 7 14 4 7 2The product is: 99607200
```

虽然您通常会将此函数用于数值计算，但是您也可以使用它来做类似字符串/字符连接这样的事情。这里有一个例子:

```
int main () {
  vector<char> letters = {'a','b','c','d','e'};
  string s = "";
  s = accumulate(letters.begin(), letters.end(), s);
  cout << "The first five letters are: " << s << endl;
  return 0;
}
```

这个程序的输出是:

```
The first five letters are: abcde
```

`accumulate`函数可以代替大多数执行数字计算的循环，比如容器上的加法或乘法，或者一些字符串/字符连接问题。然而，作为一名计算机编程教师，我仍然会教我的学生如何从索引`for`循环到范围`for` 循环，因为我真的相信学生在学习一些函数式编程“魔法”之前需要理解编程的基本原则

# 计算内积

计算一对向量的内积在物理、工程和其他领域都有用途。STL 有两个功能，类似于计算内积的累加— `inner_product`。这两种形式都将一个容器的起始和结束范围、第二个容器的起始范围和起始值作为参数，并返回两个容器的内积。第二种形式允许用户提供二元函数来执行计算。

两个版本的 inner_product 函数的语法模板是:

*T inner_product(范围 1-开始，范围 1-结束，范围 2-开始，开始值)；
T inner_product(range1-start，range1-end，range2-start，starting-value，binary-
函数)；*

下面是一个使用第一版`inner_product`计算两个向量内积的例子:

```
int main () {
  vector<int> one = {1,2,3};
  vector<int> two = {4,5,6};
  int inner = 0;
  inner = inner_product(one.begin(), one.end(),
                        two.begin(), inner);
  cout << "The inner product is: " << inner << endl;
  return 0;
}
```

这个程序的输出是:

```
The inner product is: 32
```

为了演示 inner_product 的第二种形式，我调用了两个内置函数对象，`minus<int>`和`divides<int>`。程序如下:

```
int main () {
  vector<int> one = {1,2,3};
  vector<int> two = {4,5,6};
  int inner = 1;
  inner = inner_product(one.begin(), one.end(), two.begin(),
                        inner, minus<int>(), divides<int>());
  cout << "The inner product is: " << inner << endl;
  return 0;
}
```

这个程序的输出是:

```
The inner product is: 1
```

# 计算部分和集

给定下面一组数据 *{1，2，3，4，5}* ，这个数据集的部分和集是 *{1，3，6，10，15}* 。STL 有一个生成部分和集的函数— `partial_sum`。这个函数的返回值是一个输出迭代器，所以您将主要使用这个函数来显示部分和集。如果您不想使用默认的加法计算，还有第二种形式的函数，它将一个二元函数作为第四个参数。

以下是 partial_sum 的两种形式的语法模板:

*output-iterator partial _ sum(range-start，range-end，output-iterator)；
output-iterator partial _ sum(range-start，range-end，output-iterator，binary-
函数)；*

下面的程序生成我在本节开始时给出的部分和集:

```
int main () {
  vector<int> numbers = {1,2,3,4,5};
  cout << "The data set is: " << endl;
  for (const int n : numbers) {
    cout << n << " ";
  }
  cout << endl << endl;
  cout << "The partial sum set is: " << endl;
  partial_sum(numbers.begin(), numbers.end(),
              ostream_iterator<int>(cout, " "));
  return 0;
}
```

这个程序的输出是:

```
The data set is:
1 2 3 4 5The partial sum set is:
1 3 6 10 15
```

如果我们想要生成部分乘积集，我们可以在函数调用中添加一个二元函数参数。这里有一个程序可以做到这一点:

```
int main () {
  vector<int> numbers = {1,2,3,4,5};
  cout << "The data set is: " << endl;
  for (const int n : numbers) {
    cout << n << " ";
  }
  cout << endl << endl;
  cout << "The partial product set is: " << endl;
  partial_sum(numbers.begin(), numbers.end(),
              ostream_iterator<int>(cout, " "),
              multiplies<int>());
  return 0;
}
```

这个程序的输出是:

```
The data set is:
1 2 3 4 5The partial product set is:
1 2 6 24 120
```

如果您没有注意到，当计算一个乘积时，数据集从 1 开始按顺序排列，计算集的最后一个元素是原始集的最后一个元素的计算阶乘。

# 计算相邻差异

相邻差可以这样定义:

如果数据集是 *a1，a2，a3，a4，a5，a6* ，那么相邻的差集*是 a1，a2-a1，a3–2，a4-a3，a6-a5* 。

函数`adjacent_difference`用于计算相邻差异。正如我们在 partial_sum 中看到的那样，该函数有两种形式，具有相同的参数和返回值。下面是两种形式的`adjacent_difference`的语法模板:

*output-iterator adjacent _ difference(range-start，range-end，output-iterator)；
output-iterator adjacent _ difference(range-start，range-end，output-iterator，
binary-function)；*

第一个程序演示了如何使用第一种形式的函数来查找一组数字的相邻差集:

```
int main () {
  vector<int> numbers = {1,2,3,4,5,6};
  cout << "The data set is: " << endl;
  for (const int n : numbers) {
    cout << n << " ";
  }
  cout << endl << endl;
  cout << "The adjacent difference set is: " << endl;
  adjacent_difference(numbers.begin(), numbers.end(),
                      ostream_iterator<int>(cout, " "));
  return 0;
}
```

这个程序的输出是:

```
The data set is:
1 2 3 4 5 6The adjacent difference set is:
1 1 1 1 1 1
```

下一个程序演示了如果我们将默认的差计算改为加法会发生什么:

```
int main () {
  vector<int> numbers = {1,2,3,4,5,6};
  cout << "The data set is: " << endl;
  for (const int n : numbers) {
    cout << n << " ";
  }
  cout << endl << endl;
  cout << "The adjacent difference set is: " << endl;
  adjacent_difference(numbers.begin(), numbers.end(),
                      ostream_iterator<int>(cout, " "),
                      plus<int>());
  return 0;
}
```

这个程序的输出是:

```
The data set is:
1 2 3 4 5 6The adjacent difference set is:
1 3 5 7 9 11
```

容器元素以同样的方式配对，但是执行的是加法而不是减法。

# 使用数字算法

诚然，我在本文中讨论的组中唯一可能经常使用的函数是 accumulate。不过，这个函数应该取代所有使用索引 for 循环或范围 for 循环的次数，因为它通常比您自己编写的循环运行得更快，出错的机会也更少。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。