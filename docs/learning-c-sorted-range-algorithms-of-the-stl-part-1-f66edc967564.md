# 学习 c++:STL 的排序范围算法第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-c-sorted-range-algorithms-of-the-stl-part-1-f66edc967564>

![](img/96ea41bf047979aebdb0a09265f1d134.png)

Jonah Pettrich 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在这篇文章中，我将讨论在元素已经排序的容器上使用的算法。这个类中的经典算法是`binary_search`，但是标准模板库(STL)中包含了其他几个算法。

# 二分搜索法算法

计算机科学中的经典算法之一是二分搜索法算法。使用排序来提高搜索效率的历史可以追溯到古代。二分搜索法背后的概念是首先确保集装箱被分类。将变量 L 设置为 0，将变量 R 设置为容器中的最后一个索引位置。找到容器的中点，并查看中点处的元素是否等于要搜索的值。如果是，返回中点并停止。如果元素小于要搜索的值，则将 L 调整到中点+ 1。如果元素大于要搜索的值，则将 R 设置为中点-1。继续这个过程，直到找到要搜索的值或者 L > R，在这种情况下，您已经到达容器的末尾。去[这里](https://en.wikipedia.org/wiki/Binary_search_algorithm#History)看更复杂的二分搜索法算法的讨论。

# 二进制搜索函数

STL 用`binary_search`函数实现了二分搜索法算法。该函数将起始范围、结束范围和要搜索的值作为参数。该函数返回一个布尔值，表明该值是否在范围内。重载版本也允许使用二元谓词函数作为排序标准，但是这个版本很少使用。

下面是`binary_search`函数的语法模板:

*bool binary_search(范围开始，范围结束，值)；*

下面是一个正在使用的`binary_search`函数的例子:

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
  int i = 1;
  for (const int n : vec) {
    cout << n << " ";
    if (i % 10 == 0) {
      cout << endl;
    }
    i++;
  }
}int main () {
  vector<int> numbers;
  buildVec(numbers, 100);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl << endl;
  int value;
  for (int i = 1; i <= 2; i++) {
    cout << "Enter value to search for: ";
    cin >> value;
    if (binary_search(numbers.begin(), numbers.end(), value)) {
      cout << "Found " << value << " in numbers." << endl;
    }
    else {
      cout << "Did not find " << value << " in numbers."
           << endl;
    }
  }
  return 0;
}
```

以下是该程序的输出:

```
1 2 3 4 5 6 6 7 7 9
12 12 13 17 17 19 19 22 23 24
24 24 25 27 27 28 28 30 30 30
30 32 34 35 36 36 37 38 38 39
39 40 41 41 42 42 42 42 43 43
43 45 45 46 47 48 48 49 49 51
54 54 55 57 58 59 60 62 63 63
65 65 65 67 68 68 69 70 70 71
72 74 77 79 79 82 83 83 85 89
91 91 92 92 93 94 95 96 96 100Enter value to search for: 49
Found 49 in numbers.
Enter value to search for: 97
Did not find 97 in numbers.
```

您可能想知道`binary_search`函数是否适用于未排序的容器。让我们试一试:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 100);
  //sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl << endl;
  int value;
  for (int i = 1; i <= 2; i++) {
    cout << "Enter value to search for: ";
    cin >> value;
    if (binary_search(numbers.begin(), numbers.end(), value)) {
      cout << "Found " << value << " in numbers." << endl;
    }
    else {
      cout << "Did not find " << value << " in numbers."
           << endl;
    }
  }
  return 0;
}
```

我注释掉了前面程序中的排序函数。让我们看看一些输出:

```
42 68 35 1 70 25 79 59 63 65
6 46 82 28 62 92 96 43 28 37
92 5 3 54 93 83 22 17 19 96
48 27 72 39 70 13 68 100 36 95
4 12 23 34 74 65 42 12 54 69
48 45 63 58 38 60 24 42 30 79
17 36 91 43 89 7 41 43 65 49
47 6 91 30 71 51 7 2 94 49
30 24 85 55 57 41 67 77 32 9
45 40 27 24 38 39 19 83 30 42Enter value to search for: 7
Did not find 7 in numbers.
Enter value to search for: 42
Did not find 42 in numbers.
```

显然，我搜索的值在容器中，但是函数没有找到它们。让这成为一个警告。不要试图对未排序的数据使用 binary_search。

# 搜索多个值

下一个函数搜索一个容器，看它是否在容器的任何地方包含一组值(不是一个精确的序列)。这个功能就是`includes`。以下是该函数的语法模板:

*bool 包含(range-start，range-end，search-start，search-end)；*

有一个版本允许二元谓词函数参数，但我在这里只讨论第一个版本。

下一个程序演示了如何使用`includes`函数来检查是否在容器中找到了一组值。代码如下:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 100);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl << endl;
  cout << "Searching for 72, 82, and 92 in numbers."
       << endl << endl;
  vector<int> searched = {72, 82, 92};
  if (includes(numbers.begin(), numbers.end(),
               searched.begin(), searched.end())) {
    cout << "Found all elements of searched in numbers."
         << endl;
  }
  else {
    cout << "Did not find all elements of searched in numbers."
         << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
1 2 3 4 5 6 6 7 7 9
12 12 13 17 17 19 19 22 23 24
24 24 25 27 27 28 28 30 30 30
30 32 34 35 36 36 37 38 38 39
39 40 41 41 42 42 42 42 43 43
43 45 45 46 47 48 48 49 49 51
54 54 55 57 58 59 60 62 63 63
65 65 65 67 68 68 69 70 70 71
72 74 77 79 79 82 83 83 85 89
91 91 92 92 93 94 95 96 96 100 Searching for 72, 82, and 92 in numbers.Found all elements of searched in numbers.
```

关于`includes`函数的最后一件事——用户需要确保两个范围都按照相同的标准排序，以使函数正确工作。

# 寻找第一个和最后一个位置

接下来的两个函数用于确定值在容器中的第一个位置和最后一个位置。这些功能是`lower_bound`和`upper_bound`。例如，如果容器中有一组相等的数字，而您想要将另一组相同值的数字插入到容器中，则可以使用这些函数来确定插入值的安全位置范围。

以下是这些函数的两个最常见版本的语法模板:

*forward _ iterator lower _ bound(范围开始，范围结束，值)；
forward _ iterator upper _ bound(范围开始，范围结束，值)；*

下面的程序演示了如何使用`lower_bound`和`upper_bound`来确定在何处插入一个值，以保持当前的顺序。程序使用函数找到指定值的开始和结束位置，然后使用由`lower_bound`返回的迭代器插入该值。程序是这样的:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 100);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl << endl;
  int value;
  cout << "What value do you want to insert? ";
  cin >> value;
  auto lower = lower_bound(numbers.begin(),
                           numbers.end(), value);
  auto upper = upper_bound(numbers.begin(),
                           numbers.end(), value);
  int firstPos = int(lower - numbers.begin());
  int lastPos = int(upper - numbers.begin());
  cout << "You can insert " << value << " between positions "
       << firstPos << " and " << lastPos << "." << endl << endl;
  numbers.insert(lower, value);
  printVec(numbers);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
1 2 3 4 5 6 6 7 7 9
12 12 13 17 17 19 19 22 23 24
24 24 25 27 27 28 28 30 30 30
30 32 34 35 36 36 37 38 38 39
39 40 41 41 42 42 42 42 43 43
43 45 45 46 47 48 48 49 49 51
54 54 55 57 58 59 60 62 63 63
65 65 65 67 68 68 69 70 70 71
72 74 77 79 79 82 83 83 85 89
91 91 92 92 93 94 95 96 96 100What value do you want to insert? 17You can insert 17 between positions 13 and 15.1 2 3 4 5 6 6 7 7 9
12 12 13 17 17 17 19 19 22 23
24 24 24 25 27 27 28 28 30 30
30 30 32 34 35 36 36 37 38 38
39 39 40 41 41 42 42 42 42 43
43 43 45 45 46 47 48 48 49 49
51 54 54 55 57 58 59 60 62 63
63 65 65 65 67 68 68 69 70 70
71 72 74 77 79 79 82 83 83 85
89 91 91 92 92 93 94 95 96 96
100
```

还有一个函数将返回被搜索值的第一个位置和最后一个位置。这个功能就是`equal_range`。此函数返回一个`pair`，其中`first`字段保存找到值的第一个位置，而`second` 字段保存找到值的最后一个位置。

以下是`equal_range`函数的语法模板:

*对<前向迭代器，前向迭代器> equal_range(range-start，range-end，
value)；*

下面的程序提示用户输入他们想要插入的值，并告诉用户第一个位置和最后一个位置，这将使排序顺序得以保留。程序如下:

```
int main () {
  vector<int> numbers;
  buildVec(numbers, 100);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl << endl;
  int value;
  cout << "What value do you want to insert? ";
  cin >> value;
  auto positions = equal_range(numbers.begin(), numbers.end(),
                               value);
  cout << "The first position you can insert at is: "
       << *positions.first << endl;
  cout << "The last position you can insert at is: "
       << *positions.second << endl;
  return 0;
}
```

这个程序的输出是:

```
1 2 3 4 5 6 6 7 7 9
12 12 13 17 17 19 19 22 23 24
24 24 25 27 27 28 28 30 30 30
30 32 34 35 36 36 37 38 38 39
39 40 41 41 42 42 42 42 43 43
43 45 45 46 47 48 48 49 49 51
54 54 55 57 58 59 60 62 63 63
65 65 65 67 68 68 69 70 70 71
72 74 77 79 79 82 83 83 85 89
91 91 92 92 93 94 95 96 96 100 What value do you want to insert? 17
The first position you can insert at is: 17
The last position you can insert at is: 19
```

# 在第 2 部分中

这就完成了第一组排序范围算法。在我的下一篇文章中，我将介绍一些用于合并两个容器的算法，找出两个有序集合的并集和交集，并找出两个有序集合的差异。

感谢您的阅读，请给我发电子邮件提出意见和建议。