# 学习 C++:以 C++11 的方式生成随机数

> 原文：<https://levelup.gitconnected.com/learning-c-generating-random-numbers-the-c-11-way-f041bbe94f3e>

![](img/dc6ccf09b26ae49713c9a8fe092a6a7d.png)

泰勒·伊斯顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我过去关于学习 C++的文章中，我使用了旧的 C 风格的方法来生成随机数。在这篇文章中，我将介绍一种更流行的方法(从 C++11 开始),你应该使用 random 类在 C++中生成随机数。

# c 风格随机数生成

在我之前关于学习 C++标准模板库(STL)的文章中，我使用了 C 风格的生成随机数的老方法。下面是一个演示这种技术的程序:

```
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;int main () {
  vector<int> numbers;
  srand(time(0));
  for (int i = 1; i <= 10; i++) {
    numbers.push_back(rand() % 100 + 1);
  }
  for (const int n : numbers) {
    cout << n << " ";
  }
  return 0;
}
```

这个程序运行一次的输出是:

```
29 8 36 98 93 27 56 91 97 4
```

`rand`函数生成随机数，并在`cstdlib`中找到。`srand`函数为`rand`函数提供了一个种子，这样随机数的模式就不会重复。

不细说(但你可以在这里看到其中的一些),`rand`函数对于生成随机数来说并不完美。C++标准库现在有了一组更好的函数，用于在`random`类中生成随机数。本文的其余部分将简要探讨如何使用这个类。

# 随机类——引擎和分布

`random`类有一组随机数引擎，是随机值的来源，它还包含一组随机数分布，将引擎的值变成随机数。

C++标准库中有 16 个随机数引擎，所以我无法在本文中一一介绍。您可能使用最多的是`default_random_engine`，它是一个依赖于实现的引擎，用于产生随机值。另外两款发动机是`linear_congruential_engine`和`mersenne_twister_engine`。有关这些引擎的更多信息，请参考 C++标准库文档。

标准库中大约有 20 种将随机值转换成随机数的分布。您可能最常使用的两个，也是我将在本文中演示的，是`uniform_int_distribution`和`uniform_real_distribution`。一般的分布组有——伯努利分布、泊松分布、正态分布和抽样分布。与引擎一样，请参考标准库文档以获得关于这些发行版的更多信息。

# 使用随机类

现在让我们看看如何使用`random`类来生成我们的 C++应用程序所需的随机数。第一个例子使用`default_random_engine`和`uniform_int_distribution`来填充一个整数向量:

```
#include <iostream>
#include <vector>
#include <random>
using namespace std;int main () {
  vector<int> numbers;
  default_random_engine defEngine;
  uniform_int_distribution<int> intDistro(0,100);
  for (int i = 1; i <= 20; i++) {
    numbers.push_back(intDistro(defEngine));
  }
  for (const int n : numbers) {
    cout << n << " ";
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
0 13 76 46 53 22 4 68 68 94 38 52 83 3 5 53 67 0 38 6
```

您应该注意这里的一些事情。首先，当您声明该分布时，您还需要提供该分布应该使用的数字范围，如下行代码所示:

```
uniform_int_distribution<int> intDistro(0,100);
```

接下来，通过调用 distribution 对象并以 engine 对象作为参数来生成一个随机数，如下所示:

```
numbers.push_back(intDistro(defEngine));
```

我们可以通过清除向量并生成一组新的数字来了解随机数生成器的工作情况，如下例所示:

```
int main () {
  vector<int> numbers;
  default_random_engine defEngine;
  uniform_int_distribution<int> intDistro(0,100);
  for (int i = 1; i <= 20; i++) {
    numbers.push_back(intDistro(defEngine));
  }
  for (const int n : numbers) {
    cout << n << " ";
  }
  numbers.clear();
  for (int i = 1; i <= 20; i++) {
    numbers.push_back(intDistro(defEngine));
  }
  cout << endl << endl;
  for (const int n : numbers) {
    cout << n << " ";
  }
  return 0;
}
```

这个程序运行一次的输出是:

```
0 13 76 46 53 22 4 68 68 94 38 52 83 3 5 53 67 0 38 642 69 59 93 85 53 9 66 42 70 91 76 26 4 74 33 63 76 100 36
```

你会注意到第一组数字在程序第二次运行时和第一次运行时是一样的。这意味着我们需要提供一个种子，这样我们就不会重复数字的模式。

这个问题的解决方案是在声明引擎时为它提供一个种子。下面的程序提供了这样一个种子，使用来自`ctime`头文件的`time`函数返回系统时间。代码如下:

```
#include <vector>
#include <random>
#include <ctime>
using namespace std;int main () {
  vector<int> numbers;
  default_random_engine defEngine(time(0));
  uniform_int_distribution<int> intDistro(0,100);
  for (int i = 1; i <= 20; i++) {
    numbers.push_back(intDistro(defEngine));
  }
  for (const int n : numbers) {
    cout << n << " ";
  }
  return 0;
}
```

现在你每次运行这个程序都会得到一组不同的数字。

前面的例子生成了整数随机数。我们也可以通过将分布从`uniform_int_distribution`改为`uniform_real_distribution`来概括浮点随机数。下面是一个演示如何生成随机浮点数的程序:

```
int main () {
  vector<double> numbers;
  default_random_engine defEngine(time(0));
  uniform_real_distribution<double> dblDistro(1.0, 100.0);
  for (int i = 1; i <= 10; i++) {
    numbers.push_back(dblDistro(defEngine));
  }
  for (const double n : numbers) {
    cout << n << " ";
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
95.9524 32.255 99.9762 35.4064 67.0245 17.2297 92.0428 99.6441 21.8272 34.0419
```

# 这里的例子不适合专业人士

我在这里提供的例子不能用在生成数据的随机性很重要的程序中。这些例子只是为了生成随机数，因为将 100 个数字赋给一个向量来测试一个使用向量中数据的函数太费时间了。如果你需要更多关于 C++中随机数生成的专家建议，请咨询随机数和统计方面的专家。

此外，我写这篇文章是因为有些人问我是否应该在我的例子中使用旧的 C 风格的随机数生成，我觉得我应该演示一种更现代的随机数生成方式。对于你自己的工作，使用你最舒服的方法，但我将在我未来的文章中使用这些更现代的技术。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。