# 学习 c++:STL 的变异算法

> 原文：<https://levelup.gitconnected.com/learning-c-mutating-algorithms-of-the-stl-583aa720d860>

![](img/0fa25b5086cb94dc0da985d668724390.png)

Sergi Viladesau 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在最近的一组文章中，我介绍了如何使用几个标准模板库(STL)函数，这些函数在 STL 容器上执行任务，比如查找和搜索值。在下一组文章中，我将讨论一组 STL 函数的变异。这些函数改变容器元素的顺序，而不改变它们的值。

# 反转容器的元素

`reverse` 函数做了您认为它会做的事情:它颠倒了容器元素的顺序。该函数从容器中获取开始和结束范围，并反转该范围的顺序。下面是`reverse`的语法模板:

*反向(范围-开始，范围-结束)；*

下面是一个演示反转函数如何工作的程序:

```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;void printVec(vector<string> &vec) {
  for (const string s : vec) {
    cout << s << " ";
  }
}int main () {
  vector<string> names = {"Meredith", "Allison", "Mason",
                          "Christine"};
  printVec(names);
  cout << endl;
  reverse(names.begin(), names.end());
  printVec(names);
  return 0;
}
```

这个程序的输出是:

```
Meredith Allison Mason Christine
Christine Mason Allison Meredith
```

以下是如何在容器的子范围上使用该函数:

```
int main () {
  vector<string> names = {"Meredith", "Cody", "Ty", "Allison"};
  printVec(names);
  cout << endl;
  vector<string>::iterator iter = names.begin()+2;
  reverse(iter, names.end());
  printVec(names);
  return 0;
}
```

这个程序的输出是:

```
Meredith Cody Ty Allison
Meredith Cody Allison Ty
```

还有一个函数`reverse_copy`，当它反转容器的顺序时，将复制的元素发送到输出迭代器。这意味着您可以打印容器的反转元素，而无需永久更改顺序。

该函数的语法模板是:

*reverse_copy(range0-start，range0-end，output-iterator)；*

下面是一个演示`reverse_copy`如何工作的程序:

```
int main () {
  vector<string> names = {"Meredith", "Allison", "Mason"};
  printVec(names);
  cout << endl;
  reverse_copy(names.begin(), names.end(),
               ostream_iterator<string>(cout, " "));
  cout << endl;
  printVec(names);
  return 0;
}
```

这个程序的输出是:

```
Meredith Allison Mason
Mason Allison Meredith
Meredith Allison Mason
```

# 旋转容器元件

`rotate`功能用于旋转容器的元素。该函数接受一个范围起点、要旋转的元素数量(向左)和范围终点。下面是 rotate 函数的语法模板:

*旋转(range-start，new_range-start，range-end)；*

*new-range-start* 参数通过从迭代器中加减来设置，以指示容器的新的第一个元素。

下面的例子演示了`rotate`函数如何通过在容器元素的循环中将容器的每个元素左移一位来工作:

```
int main () {
  vector<string> names = {"Terri", "Meredith", "Allison",
                          "Mason"};
  cout << "Before rotation: " << endl;
  printVec(names);
  cout << endl << endl;
  cout << "During rotation: " << endl;
  for (int i = 1; i <= names.size()-1; i++) {
    rotate(names.begin(), names.begin()+1, names.end());
    printVec(names);
    cout << endl;
  }
  return 0;
}
```

以下是该程序的输出:

```
Before rotation:
Terri Meredith Allison MasonDuring rotation:
Meredith Allison Mason Terri
Allison Mason Terri Meredith
Mason Terri Meredith Allison
```

这个程序通过从容器的末尾减去 1，将容器的元素向另一个方向移动:

```
int main () {
  vector<string> names = {"Terri", "Meredith", "Allison",
                          "Mason"};
  cout << "Before rotation: " << endl;
  printVec(names);
  cout << endl << endl;
  cout << "During rotation: " << endl;
  for (int i = 1; i <= names.size()-1; i++) {
    rotate(names.begin(), names.end()-1, names.end());
    printVec(names);
    cout << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
Before rotation:
Terri Meredith Allison MasonDuring rotation:
Mason Terri Meredith Allison
Allison Mason Terri Meredith
Meredith Allison Mason Terri
```

还有一个`rotate_copy`函数，其工作方式类似于`reverse_copy`函数。您可以使用此函数显示容器旋转，而无需对容器进行实际更改。像`reverse_copy`函数一样，函数的最后一个参数是输出迭代器。

下面是`rotate_copy`的语法模板:

*rotate_copy(range-start，rotate-start-iterator，range-end，output-iterator)；*

下面是一个程序，它将容器向左旋转一个元素，显示旋转而不改变底层元素的顺序:

```
int main () {
  vector<string> names = {"Terri", "Meredith", "Allison",
                          "Mason"};
  cout << "Before rotation: " << endl;
  printVec(names);
  cout << endl;
  cout << endl << "Rotating one element to the right: " << endl;
  rotate_copy(names.begin(), names.begin()+1,
              names.end(), ostream_iterator<string>(cout, " "));
  cout << endl;
  cout << endl << "After calling rotate_copy: " << endl;
  printVec(names);
  return 0;
}
```

以下是该程序的输出:

```
Before rotation:
Terri Meredith Allison MasonRotating one element to the right:
Meredith Allison Mason TerriAfter calling rotate_copy:
Terri Meredith Allison Mason
```

# 置换容器

我将介绍的下一个函数处理容器排列。排列是在不改变元素值的情况下，对容器中的元素进行重新排序。比如集合 *{1，2}* 还有一种排列: *{2，1}* 。

这些功能中的第一个是`next_permutation`。这个函数是一个布尔函数，返回一个容器是否有另一种排列可供使用。但是，您可以使用它来简单地置换容器中的元素。

下面是`next_permutation`函数的语法模板:

*next _ permutation(range-begin，range-end)；*

下面是一个程序，演示了如何使用`next_permutation`函数来生成数字向量的两种排列:

```
int main () {
  vector<int> numbers = {1,2,3,4};
  next_permutation(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl;
  next_permutation(numbers.begin(), numbers.end());
  printVec(numbers);
  return 0;
}
```

以下是该程序的输出:

```
1 2 4 3
1 3 2 4
```

以下程序演示了如何查看容器的所有排列:

```
int main () {
  vector<int> numbers = {1,2,3};
  while (next_permutation(numbers.begin(), numbers.end())) {
    printVec(numbers);
    cout << endl;
  }
  return 0;
}
```

以下是该程序的输出:

```
1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
```

我保持数据集简短，因为数据集中排列的数量增长非常快。

这组的另一个功能是`prev_permutation`。这个函数将容器恢复到最后一次排列之前的排列。该函数具有与`next_permutation`函数相同的语法模板，并且该函数也返回一个布尔值。

下面是一个演示`prev_permutation`如何工作的程序:

```
int main () {
  vector<int> numbers = {1,2,3,4};
  cout << "Original data set: " << endl;
  printVec(numbers);
  cout << endl;
  next_permutation(numbers.begin(), numbers.end());
  cout << "First permutation: " << endl;
  printVec(numbers);
  cout << endl;
  next_permutation(numbers.begin(), numbers.end());
  cout << "Second permutation: " << endl;
  printVec(numbers);
  cout << endl;
  cout << "Previous permutation: " << endl;
  prev_permutation(numbers.begin(), numbers.end());
  printVec(numbers);
  return 0;
}
```

这个程序的输出是:

```
Original data set:
1 2 3 4
First permutation:
1 2 4 3
Second permutation:
1 3 2 4
Previous permutation:
1 2 4 3
```

# 洗牌容器元素

我将讨论的下一组函数用于随机打乱容器的元素。

在我开始讨论这些函数之前，它们都使用了更现代的生成随机数的 C++方法。我将在另一篇文章中介绍这些技术，但是如果你现在想了解更多的话，[这篇](https://www.fluentcpp.com/2019/05/24/how-to-fill-a-cpp-collection-with-random-values/)教程介绍了基础知识。

该组中的第一个函数是`shuffle`函数。这个函数接受一系列元素，以及一个随机数生成器引擎，并使用这个引擎打乱这些元素。以下是 shuffle 函数的语法模板:

*洗牌(范围开始，范围结束，随机引擎)；*

下面是一个使用`shuffle`函数改变容器顺序的例子:

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <random>int main () {
  vector<int> numbers = {1,2,3,4,5,6,7,8,9,10};
  printVec(numbers);
  cout << endl;
  default_random_engine rnumEngine;
  shuffle(numbers.begin(), numbers.end(), rnumEngine);
  printVec(numbers);
  return 0;
}
```

这个程序的输出是:

```
1 2 3 4 5 6 7 8 9 10
8 7 5 6 2 4 10 3 1 9
```

这组中的第二个功能是`random_shuffle`。这个函数不需要随机数生成器，而是使用一个实现定义的函数，比如`rand`。以下是该函数的语法模板:

*random_shuffle(range-start，range-end)；*

下面的程序使用`random_shuffle`来打乱 vector 中的元素:

```
int main () {
  vector<int> numbers = {1,2,3,4,5,6,7,8,9,10};
  printVec(numbers);
  cout << endl;
  random_shuffle(numbers.begin(), numbers.end());
  printVec(numbers);
  return 0;
}
```

这个程序的输出是:

```
1 2 3 4 5 6 7 8 9 10
9 2 10 3 1 6 8 4 5 7
```

我们可以通过对元素重新排序并重新洗牌来测试函数的随机性，如下所示:

```
int main () {
  vector<int> numbers = {1,2,3,4,5,6,7,8,9,10};
  printVec(numbers);
  cout << endl;
  random_shuffle(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl;
  sort(numbers.begin(), numbers.end());
  random_shuffle(numbers.begin(), numbers.end());
  printVec(numbers);
  cout << endl;
  sort(numbers.begin(), numbers.end());
  random_shuffle(numbers.begin(), numbers.end());
  printVec(numbers);
  return 0;
}
```

以下是该程序的输出:

```
1 2 3 4 5 6 7 8 9 10
9 2 10 3 1 6 8 4 5 7
7 5 10 8 4 1 2 9 6 3
3 10 6 4 8 1 7 9 5 2
```

这个输出表明`random_shuffle`函数在随机洗牌方面做得很好。

还有第二个版本的`random_shuffle`,但是为了节省空间，我不会在本文中涉及它。

# 将元素移到前面的函数

我将介绍的最后一组函数用于将容器元素移动到容器的前面。第一个是`partition`，根据谓词函数将一组元素移动到容器的前面。

下面是分区的语法模板:

*划分(范围-开始，范围-结束，谓词-函数)；*

以下程序使用 lambda 函数作为谓词，将大于 50 的数字移动到向量的前面:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  cout << "Before partitioning numbers greater than 50: "
       << endl;
  printVec(numbers);
  cout << endl << endl;
  partition(numbers.begin(), numbers.end(),
            [] (int num) { return num > 50;});
  cout << "After partitioning: " << endl;
  printVec(numbers);
  return 0;
}
```

下面是这个程序的输出:

```
Before partitioning numbers greater than 50:
50 86 3 57 2 73 14 54 49 5 76 26 36 45 10 31 46 71 94 27After partitioning:
94 86 71 57 76 73 54 14 49 5 2 26 36 45 10 31 46 3 50 27
```

如果您想保留容器中元素的相对顺序，甚至在分区之后，您可以使用`stable_partition`函数。以下是该函数的语法模板:

*stable _ partition(range-start，range-end，谓词-函数)；*

在下面的程序中，使用了`stable_partition`函数，如果您将输出与上面的分区示例的输出进行比较，您会看到`stable_partition`函数做了它应该做的事情。代码如下:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  cout << "Before partitioning numbers greater than 50: "
       << endl;
  printVec(numbers);
  cout << endl << endl;
  stable_partition(numbers.begin(), numbers.end(),
                   [] (int num) { return num > 50;});
  cout << "After partitioning: " << endl;
  printVec(numbers);
  return 0;
}
```

下面是程序的输出:

```
Before partitioning numbers greater than 50:
15 90 19 46 17 35 65 34 75 83 94 17 81 27 27 84 56 50 84 57After partitioning:
90 65 75 83 94 81 84 56 84 57 15 19 46 17 35 34 17 27 27 50
```

与使用分区函数的早期程序相比，数字的顺序保持得更好。

该组的最后一个功能是`partition_copy`。这个函数将一个容器分成两个范围。该函数的语法模板是:

*partition_copy(range-start，range-end，output-iterator1-range-start，output-
iterator2-start，谓词-函数)；*

由于该函数将元素放入输出迭代器类型的范围中，所以使用起来有点复杂。为了使这更容易，我使用了一个名为`back_inserter`的函数。[这里的](https://www.cplusplus.com/reference/iterator/back_inserter/)描述了`back_inserter`的功能和工作原理。

下面的程序获取一个随机生成的数字向量，并将其分成两个向量，一个包含小于 50 的值，另一个包含大于等于 50 的值。代码如下:

```
int main () {
  srand(time(0));
  vector<int> numbers;
  buildVec(numbers, 20);
  cout << "Original vector: " << endl;
  printVec(numbers);
  cout << endl << endl;
  vector<int> lessThan50;
  vector<int> greaterThan50;
  partition_copy(numbers.begin(), numbers.end(),
                 back_inserter(greaterThan50),
                 back_inserter(lessThan50),
                 [](int num) { return num >= 50; });
  cout << "Less than 50: " << endl;
  printVec(lessThan50);
  cout << endl << endl;
  cout << "Greater than or equal to 50: " << endl;
  printVec(greaterThan50);
  return 0;
}
```

这个程序的输出是:

```
Original vector:
50 12 47 31 74 61 35 57 73 81 38 49 79 24 87 60 30 37 19 41Less than 50:
12 47 31 35 38 49 24 30 37 19 41Greater than or equal to 50:
50 74 61 57 73 81 79 87 60
```

# 另一种变异算法:排序

这就是 STL 中的一组变异算法。在我的下一篇文章中，我将讨论一组用于将容器元素排序的函数。

感谢您的阅读，请给我发电子邮件提出意见和建议。