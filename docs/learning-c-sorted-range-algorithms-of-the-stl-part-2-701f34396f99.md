# 学习 c++:STL 的排序范围算法第 2 部分

> 原文：<https://levelup.gitconnected.com/learning-c-sorted-range-algorithms-of-the-stl-part-2-701f34396f99>

![](img/e56b37d541c0320717ec9d529ef9361a.png)

约书亚·科尔曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将继续讨论标准模板库(STL)中的排序范围算法。这些算法执行诸如合并容器和执行集合运算(如并、交和差)之类的任务。

# 合并容器元素

`merge`函数获取两个容器范围的内容，并将它们合并到一个输出迭代器中。以下是该函数的语法模板:

*输出-迭代器合并(范围 1-开始，范围 1-结束，范围 2-开始，范围 2-结束，
输出-迭代器-开始)；*

下面的程序合并了两个排序的、随机生成的数字向量，并通过输出迭代器将它们打印到屏幕上:

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdlib>
#include <ctime>
#include <iterator>
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
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 10);
  sort(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl << endl;
  vector<int> more_numbers;
  buildVec(more_numbers, 10);
  sort(more_numbers.begin(), more_numbers.end());
  printVec(more_numbers);
  cout << endl << endl;
  cout << "Merged elements: " << endl;
  merge(numbers.begin(), numbers.end(),
        more_numbers.begin(), more_numbers.end(),
        ostream_iterator<int>(cout, " "));
  return 0;
}
```

这个程序运行一次的输出是:

```
14 14 19 28 62 68 75 95 98 10024 25 36 46 52 68 70 71 81 97Merged elements:14 14 19 24 25 28 36 46 52 62 68 68 70 71 75 81 95 97 98 100
```

必须对每个容器的元素进行排序，才能对合并后的集合进行排序。

还有一个版本的`merge`允许您指定不同的谓词函数用于排序，但我不会在这里讨论它。

# 寻找两个集合的并集

`set_union`函数是 STL 中的另一个排序范围函数。这个函数确实如它所说的那样—找到两个排序范围(集合)的并集，并将其写入输出迭代器。下面是`set_union`函数的语法模板:

*output-iterator(范围 1-start，范围 1-end，范围 2-start，范围 2-end，output-|
iterator)；*

以下是显示两个集合的并集的示例:

```
int main () {
  srand(time(0));
  vector<int> set1;
  buildVec(set1, 10);
  sort(set1.begin(), set1.end());
  cout << "Set 1: " << endl;
  printVec(set1);
  cout << endl << endl;
  vector<int> set2;
  buildVec(set2, 10);
  sort(set2.begin(), set2.end());
  cout << "Set 2: " << endl;
  printVec(set2);
  cout << endl << endl;
  cout << "Set 1 and 2 Union: " << endl;
  set_union(set1.begin(), set1.end(),
            set2.begin(), set2.end(),
            ostream_iterator<int>(cout, " "));
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Set 1:
8 19 26 41 49 67 71 85 91 99 Set 2:
2 8 18 24 35 46 66 70 76 98 Set 1 and 2 Union:2 8 18 19 24 26 35 41 46 49 66 67 70 71 76 85 91 98 99
```

请注意，元素 8 在联合中只出现一次，这正是集合联合应该如何形成的。

重要的是要记住，必须对这两个范围进行排序，该函数才能正常工作。

还有一个版本的函数接受谓词函数作为最后一个参数，但我不会在这里介绍它。

# 寻找两个集合的交集

`set_intersection`函数找到两个排序后的容器的交集。以防你忘记了你的集合论，两个集合的交集是在两个集合中都可以找到的元素的集合。

下面是这个函数的 synxtax 模板:

*输出-迭代器 set_intersection(range1-start，range1-end，range2-start，range2-
end，输出-迭代器)；*

下面是一个演示`set_intersection`功能如何工作的程序:

```
int main () {
  srand(time(0));
  vector<int> set1;
  buildVec(set1, 10);
  sort(set1.begin(), set1.end());
  cout << "Set 1: " << endl;
  printVec(set1);
  cout << endl << endl;
  vector<int> set2;
  buildVec(set2, 10);
  sort(set2.begin(), set2.end());
  cout << "Set 2: " << endl;
  printVec(set2);
  cout << endl << endl;
  cout << "Set 1 and 2 Intersection: " << endl;
  set_intersection(set1.begin(), set1.end(),
                   set2.begin(), set2.end(),
                   ostream_iterator<int>(cout, " "));
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Set 1:
20 25 27 27 58 63 65 79 94 95 Set 2:
12 29 35 54 58 58 69 77 82 94 Set 1 and 2 Intersection:
58 94
```

当然，有可能两个集合的交集是空集，这意味着没有元素显示为函数的输出。

这个函数还有一个重载版本，它将一个谓词函数作为最后一个参数，但我不在这里讨论它。

# 找出两个集合的差异

`set_difference`函数找出两个排序后的容器之间的差异。两个集合的区别在于第一个集合中的元素不在第二个集合中。

下面是`set_difference`的语法模板是:

*output-iterator(范围 1-start，范围 1-end，范围 2-start，范围 2-end，output-
iterator)；*

下面的程序展示了如何使用`set_difference`找出两个排序区域之间的差异:

```
int main () {
  srand(time(0));
  vector<int> set1;
  buildVec(set1, 10);
  sort(set1.begin(), set1.end());
  cout << "Set 1: " << endl;
  printVec(set1);
  cout << endl << endl;
  vector<int> set2;
  buildVec(set2, 10);
  sort(set2.begin(), set2.end());
  cout << "Set 2: " << endl;
  printVec(set2);
  cout << endl << endl;
  cout << "Set 1 and 2 Difference: " << endl;
  set_difference(set1.begin(), set1.end(),
                 set2.begin(), set2.end(),
                 ostream_iterator<int>(cout, " "));
  return 0;
}
```

下面是该程序运行一次的输出:

```
Set 1:
9 12 13 23 26 56 59 63 87 90 Set 2:
5 12 29 35 42 51 77 83 93 100 Set 1 and 2 Difference:
9 13 23 26 56 59 63 87 90
```

您可以看到第一个容器中的 12 没有显示在差异输出中，因为它也存在于第二个容器中。

这个函数还有一个重载版本，它将一个谓词函数作为最后一个参数，但我不在这里讨论它。

# 求两个集合的对称差

两个集合的对称差是在任一集合中找到但在两个集合中都找不到的所有元素的集合。STL 中执行该任务的函数是`set_symmetric_difference`。

以下是该函数的语法模板:

*输出迭代器 set _ symmetric _ difference(range 1-start，range1-end，range2-
start，range2-end，输出迭代器)；*

下面是一个程序，它找出两个容器的对称差异:

```
int main () {
  srand(time(0));
  vector<int> set1;
  buildVec(set1, 10);
  sort(set1.begin(), set1.end());
  cout << "Set 1: " << endl;
  printVec(set1);
  cout << endl << endl;
  vector<int> set2;
  buildVec(set2, 10);
  sort(set2.begin(), set2.end());
  cout << "Set 2: " << endl;
  printVec(set2);
  cout << endl << endl;
  cout << "Set 1 and 2 Difference: " << endl;
  set_symmetric_difference(set1.begin(), set1.end(),
                           set2.begin(), set2.end(),
                           ostream_iterator<int>(cout, " "));
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Set 1:
3 14 40 41 62 64 67 84 94 96 Set 2:
18 23 26 26 41 48 54 63 66 84 Set 1 and 2 Difference:
3 14 18 23 26 26 40 48 54 62 63 64 66 67 94 96
```

您可以看到 41 出现在两个容器中，但在构成两个容器的对称差的集合中找不到。

和其他函数一样，`set_symmetric_difference`也有一个重载版本，它允许一个谓词函数，但是我不会在这里讨论这个版本。

# 接下来:数字算法

这就是 STL 的排序范围算法。在我的下一篇文章中，我将介绍 STL 中的数值算法。

感谢您的阅读，请给我发电子邮件提出意见和建议。