# 学习 c++:STL 的不变算法第 3 部分

> 原文：<https://levelup.gitconnected.com/learning-c-nonmutating-algorithms-of-the-stl-part-3-94c0c04cabeb>

![](img/bd8fcd9894ef20c2df83a08e4bda23bd.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在这最后一篇关于 C++标准模板库的不变算法的文章中，我将介绍一些很少使用但仍然很重要的在容器中查找值的函数，以及其他一些任务。

# 在序列的开头和结尾查找值

STL 中有两个函数用于查找序列开头或结尾的值。第一个参数`find_end`返回最后一个序列中第一个元素的位置，该序列与作为第二个参数提供的序列相匹配。以下是此版本函数的语法模板:

*find_end(range-start，range-end，seq-start，seq-end)；*

下面是这个版本的`find_end`在工作中的一个例子:

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <list>
using namespace std;int main () {
  vector<int> numbers = {8,1,2,3,4,5,8,1,3,4,5,8,1,2};
  list<int> seq = {8,1,2};
  auto found = find_end(numbers.begin(), numbers.end(),
                        seq.begin(), seq.end());
  int position = int(found - numbers.begin());
  cout << "Found last sequence match starting at: " << position
       << endl;
  return 0;
}
```

在这个程序中，`find_end`函数在数字向量中寻找序列 8，1，2 的最后一个匹配。以下是该程序的输出:

```
Found last sequence match starting at: 11
```

如果该函数找不到匹配项，它将返回容器的末尾，表明它已到达容器的末尾，但没有找到匹配项。

第二个版本的`find_end`可以有第五个参数，这是一个谓词函数，可以用作标准。下面是这个版本的`find_end`的语法模板:

*find_end(range-start，range-end，seq-start，seq-end，谓词函数)；*

以下示例演示了该函数的工作原理。该程序生成一个由 50 个随机生成的数字组成的向量。还有第二个向量有三个`even`值。`find_end`函数在数字向量中查找三个偶数值的最后一个序列，并报告该值。

程序如下:

```
bool isEven(int number, bool even) {
  if (even) {
    return number % 2 == 0;
  }
  else {
    return number % 2 == 1;
  }
}int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  vector<bool> threeEvens = {true, true, true};
  auto found = find_end(numbers.begin(), numbers.end(),
                        threeEvens.begin(), threeEvens.end(),
                        isEven);
  if (found != numbers.end()) {
    cout << "The last sequence starts at: "
         << int(found-numbers.begin()) << endl;
  }
  else {
    cout << "No sequence like that found." << endl;
  }
  return 0;
}
```

我必须创建一个更复杂的谓词函数，因为`find_end`函数期望谓词接受两个参数，而不是一个。

这个程序运行一次的输出是:

```
97 41 76 78 49 61 56 93 53 5
24 65 39 61 99 24 14 11 2 91
84 87 71 33 4 63 16 18 19 79
61 89 90 50 66 28 31 27 29 63
19 33 26 8 17 11 64 85 41 86The last sequence starts at: 33
```

该组中的最后一个功能是`find_first_of`功能。该函数返回一个范围中第一个元素的位置，该范围与作为第二组参数给出的序列中的任何元素相匹配。以下是该函数的语法模板:

*find_first_of(range-start，range-end，seq-start，seq-end)；*

下面是一个演示如何使用`find_first_of`的程序:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  list<int> sequence {50, 51, 52};
  auto found = find_first_of(numbers.begin(), numbers.end(),
                             sequence.begin(), sequence.end());
  if (found != numbers.end()) {
    cout << "Found sequence starting at: "
         << int(found-numbers.begin()) << endl;
  }
  else {
    cout << "Sequence not found." << endl;
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
25 38 45 2 50 67 74 68 14 81
59 26 98 88 4 42 84 50 79 9
72 55 67 72 14 24 99 85 99 38
9 77 84 7 57 79 90 92 52 5
60 79 66 29 49 47 84 44 46 29Found sequence starting at: 4
```

第二种形式的`find_first_of`函数将谓词函数作为最后一个参数，并返回容器中第一个元素的位置，当第二个序列中的任何元素匹配谓词的标准时，返回 true。

下面是这个版本的`find_first_of`的语法模板:

*find_first_of(range-start，range-end，seq-start，seq-end，谓词函数)；*

下面是一个使用这个版本的函数的例子。程序将返回 numbers 向量中大于 50、51 或 52(实际上是 52)的第一个值。代码如下:

```
bool greaterThan(int number, int number1) {
  return number > number1;
}int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  list<int> sequence {50, 51, 52};
  auto found = find_first_of(numbers.begin(), numbers.end(),
                             sequence.begin(), sequence.end(),
                             greaterThan);
  cout << "The first value greater than 50, 51, 52 is "
       << *found << endl;
  return 0;
}
```

以下是该程序的几次运行:

```
72 13 15 98 91 66 25 78 44 85
34 45 65 100 8 49 28 48 79 10
57 24 13 32 18 4 81 85 29 1
78 82 73 79 75 19 30 99 13 49
50 78 100 63 72 26 41 84 48 95The first value greater than 50, 51, 52 is 7245 22 12 69 76 7 45 72 22 17
95 79 78 20 44 64 57 45 3 66
86 14 67 89 44 64 22 60 68 66
88 17 21 36 97 61 12 7 24 29
63 75 50 88 15 27 67 66 46 92The first value greater than 50, 51, 52 is 69
```

# 邻接函数

`adjacent_find`函数用于查找两个具有相同值的相邻容器元素。该函数返回容器范围中与其后续元素具有相同值的第一个元素。

该函数有两种形式。第一种形式只接受一个范围。以下是该表单的语法模板:

*相邻 _ 查找(range-start，range-end)；*

下面是一个使用`adjacent_range`功能的程序:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 50);
  printVec(numbers);
  cout << endl;
  auto found = adjacent_find(numbers.begin(), numbers.end());
  if (found != numbers.end()) {
    cout << "Found two adjacent, equal values: "
         << *found << endl;
    cout << "Found them at position: "
         << int(found-numbers.begin()) << endl;
  }
  else {
    cout << "No adjacent, equal values were found." << endl;
  }
  return 0;
}
```

下面是该程序几次运行的输出:

```
29 72 7 13 78 92 63 30 73 97
19 41 98 98 18 87 24 59 68 1
2 3 35 60 17 35 50 99 59 79
32 39 62 3 56 39 40 29 2 80
5 21 84 74 97 69 26 40 52 51Found two adjacent, equal values: 98
Found them at position: 1270 2 34 59 53 33 58 29 75 74
59 35 30 94 9 29 18 11 85 22
49 23 13 76 53 10 14 26 72 100
1 15 72 13 85 25 1 97 92 27
12 31 4 34 73 31 24 78 47 23No adjacent, equal values were found.
```

# 比较范围

我要检查的下一组函数用于比较两个范围的容器元素。这些功能中的第一个是`equal`。该函数比较两个区域，看它们是否包含相同顺序的相同元素。

这个函数有两种形式。第一种形式比较两个范围是否相等。下面是这种等号形式的语法模板:

*相等(范围 1-开始，范围 1-结束，范围 2-开始)；*

请注意，该函数只取第二个范围的开始。

下面是一个使用 equal 来比较两个容器的程序:

```
int main () {
  vector<int> numbers = {2,1,4,3};
  list<int> nums = {1,2,3,4};
  if (equal(numbers.begin(), numbers.end(),
            nums.begin())) {
    cout << "numbers and nums are equal." << endl;
  }
  else {
    cout << "numbers and nums are not equal." << endl;
  }
  return 0;
}
```

这个程序将显示两个容器不相等。然而，如果 numbers 向量已经用`{1,2,3,4}`初始化，那么这两个容器将是相等的。

第二种形式的函数将谓词函数作为第四个参数，并使用谓词来比较两个范围是否相等。下面是该等号形式的语法模板:

*equal(range1-start，range1-end，range2-start，谓词-函数)；*

下面是一个演示这种形式的`equal`的程序:

```
bool greaterThanFive(int element1, int element2) {
  return (element1 > 5 && element2 > 5);
}int main () {
  srand(time(0));
  vector<int> numbers, numbers1;
  buildVec(numbers, 10);
  buildVec(numbers1, 10);
  printVec(numbers);
  cout << endl;
  printVec(numbers1);
  cout << endl;
  if (equal(numbers.begin(), numbers.end(),
            numbers1.begin(), greaterThanFive)) {
    cout << "Both containers have all elements > 5." << endl;
  }
  else {
    cout << "Both containers do not have all elements > 5."
         << endl;
  }
  return 0;
}
```

下面是该程序两次运行的输出:

```
30 49 11 73 25 32 30 11 63 2029 83 13 12 36 47 8 14 80 76Both containers have all elements > 5.29 41 82 75 22 18 81 55 62 47 70 96 30 70 94 5 73 50 89Both containers do not have all elements > 5.
```

这组函数中比较范围的下一个函数是`is_permutation`。该函数检查两个范围的容器元素，并确定第一个范围的元素是否是第二个范围的排列。

该函数的第一个版本采用容器的开始和停止范围进行比较，并采用容器元素的第二个开始范围进行比较。下面是这个版本的`is_permutation`的语法模板:

*is_permutation(range1-start，range1-end，range 2-start)；*

下面是一个演示如何使用这个版本的`is_permutation`的程序:

```
int main () {
  vector<int> nums = {1,2,5,4};
  list<int> numbers = {4,1,3,2};
  if (is_permutation(numbers.begin(), numbers.end(),
                     nums.begin())) {
    cout << "numbers is a permutation of nums." << endl;
  }
  else {
    cout << "numbers is not a permutation of nums." << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
numbers is a permutation of nums.
```

第二个版本的`is_permutation`采用了一个额外的参数——一个用于比较两个容器范围的谓词函数。以下是此版本函数的语法模板:

*is _ permutation(range 1-start，range1-end，range2-start，谓词-函数)；*

在下面的示例中，两个范围用谓词函数进行比较，该函数检查容器的每个元素是否小于或等于 4。代码如下:

```
bool lessEqualFour(int element1, int element2) {
  return (element1 <= 4 && element2 <= 4);
}int main () {
  vector<int> nums = {0,2,3,4};
  list<int> numbers = {4,1,3,2};
  if (is_permutation(numbers.begin(), numbers.end(),
                     nums.begin(), lessEqualFour)) {
    cout << "numbers is a permutation of nums." << endl;
  }
  else {
    cout << "numbers is not a permutation of nums." << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
numbers is a permutation of nums.
```

# 找出两个范围的差异

我要讨论的下一个函数会告诉你什么时候一个容器的元素不再匹配另一个容器的元素。这个功能叫做`mismatch`。该函数返回一个`pair`，其中`first`字段包含第一个不匹配的元素，而`second`字段包含第二个不匹配的元素。

这个函数有两个版本:一个只接受范围作为参数，另一个接受谓词函数进行比较。以下是这两个版本的语法模板:

*不匹配(范围 1-开始，范围 1-结束，范围 2-开始)；
不匹配(范围 1-开始，范围 1-结束，范围 2-开始，谓词-函数)；*

下面的程序演示了第一个版本的`mismatch`是如何工作的:

```
int main () {
  vector<int> nums = {1,2,3,4,5,6};
  list<int> numbers = {1,2,4,5,6};
  auto unmatched = mismatch(nums.begin(), nums.end(),
                            numbers.begin());
  if (unmatched.first != nums.end()) {
    cout << "The first mismatch is: " << *unmatched.first
         << endl;
    cout << "The second mismatch is: " << *unmatched.second
         << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
The first mismatch is: 3
The second mismatch is: 4
```

下一个程序演示了如何使用第二版的`mismatch`。这一次比较是检查偶数，如果在第一个范围内发现奇数，将进行报告。程序是这样的:

```
bool bothEven(int element1, int element2) {
  return (element1 % 2 == 0 && element2 % 2 == 0);
}int main () {
  vector<int> nums = {2,4,6,8,10,11,12};
  list<int> numbers = {2,4,6,8,10,12,14};
  auto unmatched = mismatch(nums.begin(), nums.end(),
                            numbers.begin(), bothEven);
  if (unmatched.first != nums.end()) {
    cout << "The first mismatch is: " << *unmatched.first
         << endl;
    cout << "The second mismatch is: " << *unmatched.second
         << endl;
  }
  return 0;
}
```

# 下一组算法

这就完成了我对 STL 的非可变算法的回顾。我没有涵盖每一个函数，但我检查了其中的大部分。接下来是一组可以改变容器顺序的算法。

感谢您的阅读，请给我发电子邮件提出意见和建议。