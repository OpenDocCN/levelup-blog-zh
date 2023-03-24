# 学习 c++:STL 的排序算法

> 原文：<https://levelup.gitconnected.com/learning-c-sorting-algorithms-of-the-stl-3c473a1e3143>

![](img/007751cdf6814c0189ba3e1eae79a0b4.png)

照片由[苏拉娅·欧文](https://unsplash.com/@traxing?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

标准模板库(STL)有一小组排序算法，可以用来排序容器元素。所使用的排序算法的类型是依赖于实现的，但是 STL 保证了 *n-log-n* (良好的)性能。在本文中，我将演示如何在 STL 中使用各种排序算法。

# 排序功能

STL 的基本排序功能是`sort`。该函数获取一系列容器元素并对它们进行排序。在该函数的第一个版本中，它使用`<`操作符对元素进行排序，在第二个版本中，您可以提供一个二元谓词函数来对元素进行排序。

以下是两个版本的 sort 的语法模板:

*void 排序(range-start，range-end)；
void 排序(range-start，range-end，binary-predicate-function)；*

下面是一个演示第一版`sort`如何工作的程序:

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdlib>
#include <ctime>
using namespace std;void buildVec(vector<int> &vec, int n) {
  for (int i = 1; i <= n; i++) {
    vec.push_back(rand() % 100 + 1);
  }
}void printVec(vector<int> &vec) {
  for (const int n : vec) {
    cout << n << " ";
  }
}int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  printVec(numbers);
  sort(numbers.begin(), numbers.end());
  cout << endl << endl;
  printVec(numbers);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
66 19 34 94 8 53 2 46 61 30 14 65 8 19 23 95 45 42 41 342 8 8 14 19 19 23 30 34 34 41 42 45 46 53 61 65 66 94 95
```

如您所见，vector 的元素按照升序排序，这是由`<`操作符的默认操作指定的。

如果我们想将这些数据按降序排序，我们可以向函数传递第三个参数来改变顺序。对于这个例子，我们将使用`greater<>()`函数对象。程序是这样的:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  printVec(numbers);
  sort(numbers.begin(), numbers.end(), greater<int>());
  cout << endl << endl;
  printVec(numbers);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
10 49 36 75 32 66 96 32 82 10 38 94 15 79 30 99 97 48 30 8899 97 96 94 88 82 79 75 66 49 48 38 36 32 32 30 30 15 10 10
```

使用`sort`功能有一些限制。主要的约束是，您不能在关联容器(如`map`)或基于列表的容器(如`list`或`forward_list`)上使用该函数，因为这些容器类型不提供对这些容器类型中的元素进行排序所需的迭代器类型。

# 稳定排序函数

我想讨论的下一种排序是稳定排序。稳定排序是指从原始容器到排序后的容器，相等元素的相对位置保持不变。实现稳定排序的函数是`stable_sort`函数。这个函数也可以接受一个二元谓词函数作为可选参数。以下是该函数的语法模板:

*void stable _ sort(range-begin，range-end)；
void stable _ sort(range-begin，range-end，binary-predicate-function)；*

下一个程序是一个例子，说明了`stable_sort`函数如何保持相等元素的位置。向量包含不同长度的字符串，并且有一个比较字符串长度的函数，强制小于排序。首先，我们调用`sort`函数:

```
// this example is taken from a similar example in the book
// *The C++ Standard Library: Second Edition* by Nicolai Josuttisbool length(const string &s1, const string &s2) {
  return s1.length() < s2.length();
}int main () {
  vector<string> words = {"1xxx", "2x", "3x", "4x", "5xx",
                          "6xxxx", "7xx", "8xxx", "9xx",
                          "10xxx", "11", "12", "13", "14xx",
                          "15", "16", "17"};
  vector<string> copied(words);
  sort(words.begin(), words.end(), length);
  cout << "Order with sort function: " << endl;
  printVec(words);
  return 0;
}
```

这个程序的输出是:

```
Order with sort function:2x 17 16 15 13 12 11 4x 3x 9xx 7xx 5xx 8xxx 14xx 1xxx 10xxx 6xxxx
```

下面是`stable_sort`节目:

```
bool length(const string &s1, const string &s2) {
  return s1.length() < s2.length();
}int main () {
  vector<string> words = {"1xxx", "2x", "3x", "4x", "5xx",
                          "6xxxx","7xx", "8xxx", "9xx", "10xxx",
                          "11", "12","13", "14xx",
                          "15","16","17"};
  vector<string> copied(words);
  stable_sort(words.begin(), words.end(), length);
  cout << "Order with stable_sort function: " << endl;
  printVec(words);
  return 0;
}
```

以下是该程序的输出:

```
Order with stable_sort function:
2x 3x 4x 11 12 13 15 16 17 5xx 7xx 9xx 1xxx 8xxx 14xx 6xxxx 10xxx
```

正如您所看到的，stable_sort 函数保留了数字开头的字符串的原始顺序，但 sort 函数没有保留。

# 部分排序算法

我上面讨论的算法主要用于对各种元素进行排序，比如一个完整的容器。下一个函数用于执行部分排序，其中只对一个范围的指定子范围进行排序。

执行部分排序的功能是`partial_sort`。它的参数是范围起点、停止部分排序的位置和整个范围的终点。`partial_sort`的第二个版本允许一个二元谓词函数作为最终参数。

以下是 partial_sort 的语法模板:

*void partial _ sort(range-start，partial-range-end，range-end)；
void partial _ sort(range-start，partial-range-end，range-end，
二元谓词函数)；*

下面的程序演示了第一个版本的`partial_sort`，排序在容器中的第十个元素后停止。程序是这样的:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  printVec(numbers);
  partial_sort(numbers.begin(), numbers.begin() + 10,
               numbers.end());
  cout << endl << endl;
  printVec(numbers);
  return 0;
}
```

以下是该程序的输出:

```
73 39 68 1 32 4 78 74 7 39 39 45 10 22 33 78 53 50 20 681 4 7 10 20 22 32 33 39 39 78 74 73 68 45 78 53 50 39 68
```

如果仔细观察，您会发现只有 vector 的前十个元素按照函数调用中的指定进行了排序。

下面是一个程序，它使用第二个版本的`partial_sort`将向量的前半部分按降序排序:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  printVec(numbers);
  partial_sort(numbers.begin(), numbers.begin() + 10,
               numbers.end(), greater<int>());
  cout << endl << endl;
  printVec(numbers);
  return 0;
}
```

以下是该程序的输出:

```
33 61 50 27 24 71 1 88 12 13 30 75 47 44 34 97 100 93 77 91100 97 93 91 88 77 75 71 61 50 1 12 13 24 27 30 33 34 44 47
```

还有一个函数执行部分排序，并将排序后的元素复制到另一个容器中。这个功能叫做`partial_sort_copy`。它还有一个重载版本，接受二元谓词函数作为最后一个参数。以下是该函数的语法模板:

*void partial _ sort _ copy(range-start，range-end，destination-range-begin，
destination-range-end)；*

*void partial _ sort _ copy(range-start，range-end，destination-range-begin，
destination-range-end，binary-predicate-function)；*

以下程序演示了如何使用第一版`partial_sort_copy`:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  vector<int> partSort(20);
  buildVec(numbers, 20);
  printVec(numbers);
  partial_sort_copy(numbers.begin(), numbers.begin() + 10,
                    partSort.begin(), partSort.end());
  cout << endl << endl;
  cout << "Showing copied elements: " << endl;
  printVec(partSort);
  return 0;
}
```

这个程序的输出是:

```
21 46 29 92 10 52 24 29 32 10 29 70 18 33 74 18 46 9 62 94Showing copied elements:
10 10 21 24 29 29 32 46 52 92
```

只有排序后的元素被复制到新的 vector 中，如输出所示。

# 排序到第 n 个元素

有时，您只需要看到容器中某个范围内的最高值或最低值。STL 有一个函数提供这个视图:`nth_element`。该函数的语法模板是:

*void n _ element(range-start，iterator-n-position，range-end)；
void n _ element(range-start，iterator-n-position，range-end，binary-
谓词-函数)；*

在第一个程序中，我将显示向量中的四个最低值:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  printVec(numbers);
  cout << endl << endl;
  nth_element(numbers.begin(), numbers.begin()+3,
              numbers.end());
  cout << "The four lowest values are: " << endl;
  copy(numbers.begin(), numbers.begin()+4,
       ostream_iterator<int>(cout, " "));
  cout << endl;
  return 0;
}
```

这个程序的输出是:

```
71 43 55 81 27 56 21 42 96 98 3 29 35 23 44 82 30 27 87 21The four lowest values are:
21 3 21 23
```

# 堆算法

我将在本文中讨论的最后一组算法是堆算法。堆是一种数据结构，它有几个属性，这些属性对于存储排序后的数据很有用。介绍堆的细节超出了本文的范围，但是您可以在这里了解更多关于堆的内容。

堆算法包含四个函数:

`make_heap`:将一定范围的元素转换成堆。
`push_heap`:将一个元素添加到一个堆中。
`pop_heap`:从堆中移除下一个元素。
`sort_heap`:将一个堆转换成一个排序的集合。

我将只提供一个程序来演示这些函数的主要版本是如何工作的，而不是涵盖这些函数的语法模板。程序是这样的:

```
int main () {
  srand(time(0));
  vector<int> numbers = {4,1,2,18,9,6,11};
  cout << "The vector before make_heap: " << endl;
  printVec(numbers);
  make_heap(numbers.begin(), numbers.end());
  cout << endl << endl << "After make_heap: " << endl;
  printVec(numbers);
  numbers.push_back(15);
  push_heap(numbers.begin(), numbers.end());
  cout << endl << endl << "After push_heap: " << endl;
  printVec(numbers);
  pop_heap(numbers.begin(), numbers.end());
  cout << endl << endl << "After pop_heap: " << endl;
  printVec(numbers);
  sort_heap(numbers.begin(), numbers.end());
  cout << endl << endl << "After sort_heap: " << endl;
  printVec(numbers);
  return 0;
}
```

这个程序的输出是:

```
The vector before make_heap:
4 1 2 18 9 6 11After make_heap:
18 9 11 1 4 6 2After push_heap:
18 15 11 9 4 6 2 1After pop_heap:
15 9 11 1 4 6 2 18After sort_heap:
1 2 4 6 9 11 18 15
```

您会注意到，在调用 `sort_heap`函数后，数据没有完全排序。我不会将这些函数用于您的排序目的，但我将它们涵盖在内以使其完整。

# 接下来—排序范围算法

在我的下一篇文章中，我将介绍一组算法，您可以对已经排序的数据使用这些算法。这些算法中最著名的是二分搜索法，但也有其他算法。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。