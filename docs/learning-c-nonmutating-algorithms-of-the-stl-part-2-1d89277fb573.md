# 学习 c++:STL 的不变算法第 2 部分

> 原文：<https://levelup.gitconnected.com/learning-c-nonmutating-algorithms-of-the-stl-part-2-1d89277fb573>

![](img/7a7930f096452e69633c6098c73ec544.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将展示 C++标准模板库中的另一组非可变算法。这组函数是可以用来在容器中查找数据或搜索数据的函数。在开始之前，我应该提到这些函数将用于未排序的数据。我将在另一篇文章中详细讨论对排序数据使用哪些函数。

# 查找功能

`find`函数用于确定容器中是否存在指定的值。如果值在容器中，函数返回一个迭代器，如果值不在容器中，函数返回 end。

下面是`find`函数的语法模板:

*find(范围-开始，范围-结束，值)；*

下面是一个使用`find`函数的示例程序:

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdlib>
#include <ctime>
using namespace std;void buildVec(vector<int> &vec, int n) {
  srand(time(0));
  for (int i = 1; i <= n; i++) {
    vec.push_back(rand() % 100 + 1);
  }
}void printVec(vector<int> vec) {
  int i = 0;
  for (const int n : vec) {
    cout << n << " ";
    if (++i % 10 == 0) {
      cout << endl;
    }
  }
}int main () {
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  int value;
  for (int i = 1; i <= 2; i++) {
    cout << "Value to find: ";
    cin >> value;
    auto position = find(numbers.begin(), numbers.end(), value);
    if (position != numbers.end()) {
      cout << "Found " << value << "." << endl;
    }
    else {
      cout << value << " not in numbers." << endl;
    }
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
57 4 86 1 39 47 67 37 12 30
86 6 94 48 23 72 82 31 82 52
65 29 60 68 48 18 29 2 97 74
87 23 68 61 45 10 85 55 94 72
34 61 44 45 43 5 12 15 24 54Value to find: 3
3 not in numbers.
Value to find: 82
Found 82.
```

# find_if 函数

`find_if`函数用于根据谓词函数查找值，谓词函数可以是 lambda 函数或函数适配器。以下是该函数的语法模板:

*find_if(范围开始，范围结束，函数)；*

我的第一个例子将使用一个谓词函数来确定该函数找到的值。我创建了一个函数`greaterThan98`，它返回其参数与值 98 的比较结果。程序如下:

```
bool greaterThan98(int n) {
  return n > 98;
}int main () {
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl << endl;
  auto found = find_if(numbers.begin(), numbers.end(),
                       greaterThan98);
  if (found != numbers.end()){
    cout << "found value greater than 98" << endl;
  }
  else {
    cout << "did not find value greater than 98";
  }
  return 0;
}
```

以下是该程序两次运行的结果:

```
89 4 17 84 93 97 99 14 83 7
7 76 89 61 85 64 41 18 13 67
46 44 83 68 93 93 19 76 41 73
96 86 60 12 36 56 100 54 28 98
70 51 46 85 36 86 20 18 13 85found value greater than 9864 73 77 52 53 7 49 23 34 74
6 89 96 10 17 24 31 14 51 47
58 58 76 64 82 1 95 69 4 78
81 11 94 27 12 63 46 42 42 12
63 87 6 51 72 71 64 6 72 8did not find value greater than 98
```

您可以使用函数适配器和绑定器来代替。解释这方面的背景超出了本文的范围，所以请到[这里](https://www.cplusplus.com/reference/functional/bind/)进行解释。现在理解了(希望如此)绑定器是如何工作的，这里是前面的程序使用`greater<int>()`函数对象和一个绑定器将值 98 绑定到函数:

```
int main () {
  using namespace std::placeholders;
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  auto found = find_if(numbers.begin(), numbers.end(),
                       bind(greater<int>(),_1, 98));
  if (found != numbers.end()){
    cout << "found value greater than 98" << endl;
  }
  else {
    cout << "did not find value greater than 98";
  }
  return 0;
}
```

这个程序的输出类似于上面的输出，当然，只是数字不同。

另外，请注意，我在 main 中使用了一个`using` 语句。这样做简化了函数调用中的`bind`调用。否则，我会写:

```
auto found = find_if(numbers.begin(), numbers.end(),
               bind(greater<int>(),std::placeholders::_1, 98));
```

# 函数的作用是

find 家族中最后一个函数是`find_if_not`。此函数返回容器中不匹配指定标准的第一个元素。以下是该函数的语法模板:

*find_if_not(range-start，range-end，函数)；*

对于下面的例子，我传入一个 lambda 函数作为第三个参数。这个函数检查一个值是否大于或等于 10。`find_if_not`函数将返回不符合该标准的第一个值。程序如下:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  auto found = find_if_not(numbers.begin(), numbers.end(),
                          [](int element) { return element >= 10;});
  if (found != numbers.end()){
    cout << "found value less than 10: " << *found << endl;
  }
  else {
    cout << "did not find value less than 10";
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
17 60 44 24 56 10 68 95 76 20
55 21 34 87 46 32 62 9 5 3
12 79 34 44 51 96 6 29 62 95
40 86 95 8 89 67 72 10 8 34
8 59 23 27 16 68 98 74 62 71found value less than 10: 9
```

最后，这些`find`函数最适合顺序容器，比如数组和向量。关联容器和无序容器都有自己的 find 函数作为成员函数，效率比我在这里描述的函数要高得多。

# 使用搜索功能

`find`函数用于查找容器中的单个值。下一组函数是`search`函数，用于查找容器中的值序列。

这组中的第一个功能是`search`。这个函数有四个参数:开始搜索范围、结束搜索范围、要搜索的开始范围和要搜索的结束范围。下面是`search`函数的语法模板:

*搜索(搜索-范围-开始，搜索-范围-结束，序列-范围-开始，序列-范围-结束)；*

函数返回一个迭代器到找到匹配的第一个元素，如果没有找到匹配，函数返回一个迭代器结束。

下面是一个使用`search`函数在向量中寻找一系列值的例子。该程序在一组 50 个随机生成的数字中搜索序列 *98，99* 。代码如下:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 50);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl;
  vector<int> highVals = {98, 99};
  auto found = search(numbers.begin(), numbers.end(),
                      highVals.begin(), highVals.end());
  if (found != numbers.end()) {
    cout << "Found sequence starting with: " << *found << endl;
  }
  else {
    cout << "Didn't find sequence." << endl;
  }
  return 0;
}
```

下面是该程序的两次运行:

```
1 1 1 3 7 8 9 11 13 1517 18 19 20 22 26 27 33 33 3343 45 46 47 48 50 51 61 63 63
64 66 69 73 73 75 80 80 81 82|
83 84 86 86 88 90 91 96 99 99Didn't find sequence.1 1 2 2 3 4 5 10 14 17
19 28 31 33 34 39 41 44 47 50
50 51 53 55 58 59 60 62 63 67
68 68 70 72 75 77 80 80 80 82
84 85 94 94 94 95 98 99 99 99Found sequence starting with: 98
```

这个组的另一个功能是`search_n`。此函数搜索指定数量的相同值，例如连续三个 100 或连续两个 87。

下面是`search_n`函数的语法模板:

*search_n(范围-开始，范围-结束，顺序-计数，值)；*

`search_n`的返回值是一个迭代器，指向序列中第一个找到的值，如果序列没有找到，函数返回一个迭代器结束。

下面是一个演示`search_n`功能如何工作的程序:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 50);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl;
  int seqCount, searchVal;
  cout << "What value? ";
  cin >> searchVal;
  cout << "How many in a row? ";
  cin >> seqCount;
  auto found = search_n(numbers.begin(), numbers.end(),
                        seqCount, searchVal);
  if (found != numbers.end()) {
    cout << "Found at position: "
         << int(found - numbers.begin()) << endl;
  }
  else {
    cout << "Did not find that sequence count in the container."
         << endl;
  }
  return 0;
}
```

以下是该程序的几次运行:

```
1 3 5 6 10 11 12 12 12 13
13 15 22 30 32 33 35 35 38 40
42 45 49 50 53 53 54 59 59 60
61 61 69 69 71 73 73 77 79 80
80 82 84 85 87 91 91 92 94 99What value? 91
How many in a row? 2
Found at position: 452 3 5 5 5 6 7 8 8 13
14 16 16 18 25 28 32 32 33 36
39 44 44 44 48 48 50 56 65 65
66 72 73 74 76 77 77 79 82 86
89 93 95 97 97 97 98 99 100 100What value? 5
How many in a row? 3
Found at position: 2
```

您可以看到，我通过使用以下算法从找到第一个值的位置减去`begin`位置来确定序列中找到第一个元素的位置:

`int(found — numbers.begin())`

调用`search_n`函数的一个可选方法是提供一个谓词函数作为确定匹配的标准。此版本函数的语法模板是:

*search_n(range-start，range-end，sequence-count，value，谓词-函数)；*

下面是一个搜索大于指定值的指定值序列的示例:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 50);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl;
  int seqCount, searchVal;
  cout << "Greater than what value? ";
  cin >> searchVal;
  cout << "How many in a row? ";
  cin >> seqCount;
  auto found = search_n(numbers.begin(), numbers.end(),
                        seqCount, searchVal, greater<int>());
  if (found != numbers.end()) {
    cout << "Found at position: "
         << int(found - numbers.begin()) << endl;
  }
  else {
    cout << "Did not find that sequence count in the container."
         << endl;
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
1 1 3 6 15 16 20 24 24 26
27 34 35 39 41 44 44 46 48 48
48 49 50 51 51 55 57 57 57 57
63 65 67 67 67 68 69 72 72 73
74 75 76 78 85 87 87 90 91 98Greater than what value? 85
How many in a row? 2
Found at position: 45
```

# 还有更多的算法来寻找值

STL 中还有一套算法用于查找值和搜索序列。我将在下一篇文章中介绍这些函数，作为非可变算法系列的第 3 部分。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。