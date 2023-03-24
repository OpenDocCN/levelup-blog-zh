# 学习 c++:STL 的不变算法第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-c-nonmutating-algorithms-of-the-stl-part-1-7b306b4ac790>

![](img/eb4eb70139b63fb6625857aa5fa76ae8.png)

维多利亚诺·伊斯基耶多在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是关于标准模板库(STL)中的算法的系列文章的第一篇。这第一篇文章将研究非变动算法的一个子集——在不改变元素的情况下对容器元素执行任务的算法。第一组中的功能包括`for_each`、`count`、`count_if`、`min_element`、`max_element`和`minmax_element`。

# for_each 函数

`for_each`函数用于访问容器中的一系列元素，以便为每个元素执行一些任务。该函数的一个经典例子是打印容器中的每个元素。该函数的语法模板如下所示:

*for_each(range-start，range-end，func)；*

下面是一个使用`for_each`函数打印一个向量的每个元素的例子:

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdlib>
#include <ctime>
using namespace std;void buildVec(vector<int> &vec, int n) {
  srand(time(0));  
  for (int i = 1; i <= n; i++) {
    vec.push_back(rand() % 1000 + 1);
  }
}void printElement(int n) {
  cout << n << " ";
}int main()
{
  vector<int> numbers;
  buildVec(numbers, 20);
  for_each(numbers.begin(), numbers.end(), printElement);
  return 0;
}
```

这个程序运行一次的输出是:

```
928 722 270 403 832 495 100 728 993 75 442 376 482 169 368 563 978 610 432 374
```

为此目的使用`for_each`函数在某种程度上被 range `for`循环劫持了，尤其是当您想要访问容器中的每个元素时:

```
int main()
{
  vector<int> numbers;
  buildVec(numbers, 20);
  for (const int number : numbers) {
    cout << number << " ";
  }
  return 0;
}
```

range `for`循环在场景下使用迭代器，就像`for_each`函数使用迭代器一样。

下面是一个显示来自`map`元素容器的键和值的`for_each`示例:

```
void printNumber(pair<string, string> pr) {
  cout << "Name: " << pr.first << ", number: "
       << pr.second << endl;
}int main()
{
  map<string, string> phoneList = {{"Jones", "2300"},
                                   {"Smith", "2301"},
                                   {"Brown", "2302"},
                                   {"Green", "2303"}};
  for_each(phoneList.begin(), phoneList.end(), printNumber);
  return 0;
}
```

这个程序的输出是:

```
Name: Brown, number: 2302
Name: Green, number: 2303
Name: Jones, number: 2300
Name: Smith, number: 2301
```

因为`for_each`函数的许多用法都被使用范围`for`循环所取代，所以当我在以后的文章中讨论变异算法时，我将演示`for_each`的一些变异用法。

# count 和 count_if 函数

这两个函数计算在容器中找到的指定值的数量。以下是 count 函数的语法模板:

*计数(范围-开始，范围-结束，值)；*

该函数返回*值*在容器中出现的次数。下面是一个使用向量的示例:

```
int main()
{
  vector<int> numbers;
  buildVec(numbers, 100);
  for (const int number : numbers) {
    cout << number << " ";
  }
  const int VALUE = 100;
  cout << endl << endl;
  int hundreds = count(numbers.begin(), numbers.end(), VALUE);
  cout << "Number of 100 elements: " << hundreds << endl;
  return 0;
}
```

这个程序运行一次的输出是:

```
4 93 27 2 72 87 84 9 33 68 9 67 89 10 47 5 4 75 68 11 47 1 44 64 59 44 55 87 59 99 97 76 62 42 6 63 33 38 15 29 48 77 20 4 66 100 48 66 82 67 14 61 87 80 25 69 71 90 62 1 84 18 36 11 15 51 40 87 94 31 3 34 11 62 25 74 65 38 42 48 23 91 26 13 6 44 33 51 48 34 82 100 30 93 5 39 78 68 13 9Number of 100 elements: 2
```

函数对容器中符合给定标准的元素进行计数。这个标准可以是一个函数对象或一个 lambda。下面是`count_if`的语法模板:

*count_if(range-start，range-stop，func)；*

以下示例计算值大于 50 的元素的数量并显示结果:

```
bool aboveFifty(int element) {
  return element > 50;
}int main()
{
  vector<int> numbers;
  buildVec(numbers, 100);
  for (const int number : numbers) {
    cout << number << " ";
  }
  const int VALUE = 100;
  cout << endl << endl;
  int aboveFiftyN = count_if(numbers.begin(), numbers.end(),
                             aboveFifty);
  cout << "Number of elements above fifty: " << aboveFiftyN
       << endl;
  return 0;
}
```

这里是另一个使用 lambda 函数的`count_if`的例子:

```
int main()
{
  vector<int> numbers;
  buildVec(numbers, 100);
  int numOdds = count_if(numbers.begin(), numbers.end(),
                  [](int number) { return number % 2 != 0;});
  cout << "Number of odd elements: " << numOdds << endl;
  return 0;
}
```

# 查找容器中的最小值和最大值

接下来的两个函数用于查找容器中的最小和最大元素。第一个函数`min_element`查找容器的最小值。以下是该函数的语法模板:

*min_element(范围-开始，范围-结束)；*

该函数返回迭代器，而不是值，如下例所示:

```
int main()
{
  vector<int> numbers;
  buildVec(numbers, 100);
  for (const int number : numbers) {
    cout << number << " ";
  }
  cout << endl << endl;
  auto minValue = min_element(numbers.begin(), numbers.end());
  cout << "The minimum value is: " << *minValue << endl;
  return 0;
}
```

`max_element`函数查找容器中的最大值。下面是语法模板:

*max_element(range-start，range-end)；*

这个函数也返回一个迭代器，如下所示:

```
int main()
{
  vector<int> numbers;
  buildVec(numbers, 100);
  for (const int number : numbers) {
    cout << number << " ";
  }
  cout << endl << endl;
  auto maxValue = max_element(numbers.begin(), numbers.end());
  cout << "The minimum value is: " << *maxValue << endl;
  return 0;
}
```

该组中的第三个功能是`minmax_element`功能。该函数返回一个由`first`字段容器中的最小值和`second`字段容器中的最大值组成的`pair`。

`minmax_element`函数的语法模板是:

*minmax_element(range-start，range-end)；*

下面是一个使用`minmax_element`函数的例子:

```
int main()
{
  vector<int> numbers;
  buildVec(numbers, 100);
  for (const int number : numbers) {
    cout << number << " ";
  }
  cout << endl << endl;
  auto minMax = minmax_element(numbers.begin(), numbers.end());
  cout << "The minimum element is: " << *(minMax.first) << endl;
  cout << "The maximum element is: " << *(minMax.second)
       << endl;
  return 0;
}
```

这个程序运行一次的输出是:

```
870 960 895 146 899 868 269 174 439 403 886 287 131 652 526 39 830 798 88 770 303 435 814 297 689 108 69 589 678 758 816 503 143 259 755 392 811 467 91 756 94 709 653 341 811 289 893 797 542 32 168 140 259 190 683 971 459 982 181 319 424 302 610 58 758 197 562 151 909 323 449 357 980 992 936 126 457 783 37 8 382 252 86 59 622 948 404 964 192 652 517 369 538 741 170 278 891 742 746 928The minimum element is: 8
The maximum element is: 992
```

特别注意检索`pair`字段的方式。因为该函数返回一个迭代器，所以必须通过将对字段的调用放在括号中，然后应用解引用操作符来检索字段。不将字段调用放在括号中会导致编译器只对`pair`名称应用解引用操作符，这是一个语法错误。

# 作为成员函数的算法

大多数 STL 算法，无论是在本文中还是在我将在以后的文章中介绍的函数中，并不总是适用于每种容器类型。有时算法作为成员函数嵌入在容器中。

以`count`功能为例。如果您试图将这个函数应用于一个`multimap`，比如计算 multimap 中的键的数量，您将会得到一个语法错误。然而，在`multimap`类中有一个`count`成员函数允许这样做。这里有一个例子:

```
#include <iostream>
#include <map>
#include <algorithm>
using namespace std;int main()
{
  multimap<string, string> words;
  words.insert({"nail", "a finger or toe covering"});
  words.insert({"nail", "a sharp metallic object"});
  words.insert({"bark", "the noise made by a dog"});
  words.insert({"bark", "the covering of a tree"});
  int wordCount = words.count("bark");
  cout << "Number of bark keys: " << wordCount << endl;
  return 0;
}
```

这个故事的寓意是，如果你试图将一个算法应用到一个容器中，但它不起作用，那么在你试图编写自己的算法之前，你应该检查一下是否在容器的成员函数中找到了一个算法。更好的是，首先检查成员函数，因为它们通常比一般算法更有效。

# 本文中的函数摘要

我在本文中讨论的非变动算法要么用于访问容器的元素(`for_each`函数)，要么用于返回关于容器的一些信息(计数函数和最小值/最大值函数)。在我的下一篇文章中，我将介绍一些用于搜索容器或在容器中查找特定值的非可变算法。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。