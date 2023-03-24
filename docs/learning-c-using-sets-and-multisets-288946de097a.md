# 学习 C++:使用集合和多重集合

> 原文：<https://levelup.gitconnected.com/learning-c-using-sets-and-multisets-288946de097a>

![](img/0f32b7dd094e76e88d7b4d347517ca95.png)

由 [Karen Vardazaryan](https://unsplash.com/@bright?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

有时候，您需要一个容器来保存按照某种标准排序的唯一值。标准模板库的`set`类具有这些特性。如果您的需求允许容器保存重复的有序值，那么`multiset`类就是您想要使用的容器。在本文中，我将讨论如何使用这两个类，以及如何修改这些容器中数据的排序方式。我将首先介绍`set` 类，然后讨论当您使用`multiset`类时的区别。

# 创建集合

集合类通过以下头文件导入到您的程序中:

```
#include <set>
```

该类是一个模板类，因此当声明一个集合时，必须提供一个数据类型。集合可以是根据排序标准可比较的任何数据类型。这意味着您可以拥有一个用户定义类型的集合，如果为该类型定义了某种类型的排序函数。

集合声明必须包括集合元素的数据类型:

```
set<string> names;
set<int> grades;
```

您还可以在声明中声明一个排序标准作为第二个模板参数。我将在本文后面演示这是如何工作的。

你可以通过提供一个初始化列表来声明一个带有初始数据的集合。列表不必排序，因为它插入到集合中会自动排序。例如:

```
int main()
{
  set<int> numbers = {3,1,2};
  for (const int n : numbers) {
    cout << n << " "; // displays 1 2 3
  }
  return 0;
}
```

# 向集合中添加数据

向集合中添加数据的主要方法是`insert`函数。此函数将元素放入集合中适当的位置:

```
set<int> numbers;
numbers.insert(3);
numbers.insert(1);
numbers.insert(2);
```

该函数返回元素插入的位置，对于集合，还返回插入是否成功。这个返回值是一个`pair`结构，其中`first`字段是插入位置，而`second`字段是一个布尔值，显示插入的成功或失败。

下面是一个如何处理`insert`函数返回值的例子:

```
int main()
{
  set<int> numbers;
  pair<set<int>::iterator, bool> success;
  success = numbers.insert(3);
  if (success.second) {
    cout << "3 was inserted at position "
         << *success.first << "." << endl;
  }
  success = numbers.insert(1);
  if (success.second) {
    cout << "1 was inserted at position "
         << *success.first << "." << endl;
  }
  success = numbers.insert(2);
  if (success.second) {
    cout << "2 was inserted at position "
         << *success.first << "." << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
3 was inserted at position 3.
1 was inserted at position 1.
2 was inserted at position 2.
```

还有其他版本的`insert` 功能。您可以为函数提供一个建议的插入点，以加快插入速度。这里有一个例子:

```
int main()
{
  set<int> numbers = {8, 1, 2, 4, 5, 7, 3};
  int value = 6;
  auto iter = numbers.begin();
  while (*iter < value) {
    ++iter;
  }
  numbers.insert(iter, value);
  for (const int n : numbers) {
    cout << n << " "; // 1 2 3 4 5 6 7 8
  }
  return 0;
}
```

程序在集合中循环，直到到达小于要插入的值的最后一个值。插入发生在该点。

您也可以将元素列表插入到集合中，如下面的程序所示:

```
int main()
{
  set<int> numbers = {2,4,6};
  numbers.insert({1,3,5});
  for (const int n : numbers){
    cout << n << " "; // 1 2 3 4 5 6
  }
  return 0;
}
```

# 从集合中移除元素

使用`erase`功能从集合中删除元素。此函数的第一个版本删除一个特定值，并返回删除的元素数:

```
int main()
{
  set<int> numbers = {1,3,2,5,4};
  int removed = 3;
  int count = numbers.erase(removed);
  cout << count << " element was removed." << endl << endl;
  for (const int n : numbers) {
    cout << n << " "; // displays 1 2 4 5
  }
  return 0;
}
```

第二个版本的`erase`通过指向集合中某个位置的迭代器移除元素。它的返回值是移除元素后的位置。在下面的例子中，我忽略了返回值:

```
int main()
{
  set<int> numbers = {1,3,2,5,4};
  int removed = 3;
  auto iter = numbers.begin();
  while (*iter != removed) {
    iter++;
  }
  numbers.erase(iter);
  for (const int n : numbers) {
    cout << n << " "; // 1 2 4 5
  }
  return 0;
}
```

# 搜索集合

在 `set` 类中有几个成员函数用于搜索。搜索的主要功能是`find`。该函数将一个值作为参数，并在集合中搜索该值。如果找到该值，该函数将返回一个指向该值的迭代器。如果没有找到值，函数返回`end()`，或者集合中最后一个元素之后的位置。

下面是一个演示 find 函数如何工作的程序:

```
#include <iostream>
#include <set>
#include <cstdlib>
#include <ctime>
using namespace std;void buildSet(set<int> &st) {
  srand(time(0));
  for (int i = 1; i <= 25; i++) {
    st.insert(rand() % 100 + 1);
  }
}void printSet(const set<int> &st) {
  for (const int n : st) {
    cout << n << " ";
  }
}int main()
{
  set<int> numbers;
  buildSet(numbers);
  printSet(numbers);
  cout << endl << endl;
  int value;
  cout << "Enter a value to find: ";
  cin >> value;
  auto found = numbers.find(value);
  if (found != numbers.end()) {
    cout << "Found " << value << "." << endl;
  }
  else {
    cout << value << " was not found in the set. " << endl;
  }
  return 0;
}
```

下面是该程序两次运行的输出:

```
14 21 24 27 35 38 42 44 52 57 58 60 68 71 80 84 86 89 92 96
Enter a value to find: 57
Found 57.7 10 11 15 22 23 34 36 40 53 56 60 62 63 65 72 76 79 84 93 94 96 97
Enter a value to find: 77
77 was not found in the set.
```

# 更改排序顺序

正如我前面提到的，集合的主要排序标准是`less<>`。您可以通过在声明新集合时指定不同的标准来更改这一点。如果您不熟悉 C++中不同的排序标准，这里的[是您的参考指南。](https://www.cplusplus.com/reference/functional/less/)

为了演示如何更改排序标准，我将创建一个使用`greater<int>`标准排序的新集合。程序如下:

```
#include <iostream>
#include <set>
#include <cstdlib>
#include <ctime>
using namespace std;void buildSet(set<int, greater<int>> &st) {
  srand(time(0));
  for (int i = 1; i <= 25; i++) {
    st.insert(rand() % 100 + 1);
  }
}void printSet(const set<int, greater<int>> st) {
  for (const int n : st) {
    cout << n << " ";
  }
}int main()
{
  set<int, greater<int>> numbers;
  buildSet(numbers);
  printSet(numbers);
  return 0;
}
```

这个程序的输出是:

```
90 87 85 77 65 62 50 40 39 38 31 29 26 25 24 21 20 19 15 14 8 6 1
```

注意，我必须在所有函数参数中指定第二个参数。模板函数将解决这个问题，我将在以后的文章中解决这个问题。

# 使用 multiset 类

如果你需要一个集合的排序特性，但是你想在容器中允许重复，你应该使用`multiset`类。我们用于集合的函数也适用于多重集合。

您仍然可以在预处理器指令中引用`set` 类来使用多重集。下面的程序创建一个新的多重集并显示容器中的值:

```
#include <iostream>
#include <set>
#include <cstdlib>
#include <ctime>
using namespace std;void buildSet(multiset<int> &st) {
  srand(time(0));
  for (int i = 1; i <= 25; i++) {
    st.insert(rand() % 100 + 1);
  }
}void printSet(const multiset<int> st) {
  for (const int n : st) {
    cout << n << " ";
  }
}int main()
{
  multiset<int> numbers;
  buildSet(numbers);
  printSet(numbers);
  return 0;
}
```

下面是这个程序运行一次的输出:

```
9 11 15 18 18 28 45 46 54 63 64 67 68 68 71 77 79 79 84 84 87 88 91 93 95
```

请注意，有重复的 18、68 和 84。

我前面用集合演示的所有函数都适用于多重集合。一个可以有效地与 multisets 一起使用的函数是`count`函数，它计算集合中指定值的个数。我没有用`set`实例演示这一点，因为众所周知一个集合中只存储一个值。

下面是一个计算多重集中的值的数量的程序:

```
int main()
{
  multiset<int> numbers;
  buildSet(numbers);
  printSet(numbers);
  int value;
  cout << endl << endl;
  cout << "Enter a value to count: ";
  cin >> value;
  auto found = numbers.find(value);
  if (found != numbers.end()) {
    int numVals = numbers.count(value);
    cout << "Found " << numVals << " " << value
         << "s in the set." << endl;
  }
  return 0;
}
```

下面是该程序的一次运行:

```
5 20 21 21 22 22 25 29 30 33 33 50 57 66 70 70 73 77 81 82 82 89 89 93 94Enter a value to count: 70
Found 2 70s in the set.
```

# 何时使用集合和多重集合

使用集合或多重集合的主要原因是当您需要按排序顺序保存数据时。集合和多重集合的底层数据结构是平衡二叉树，这意味着保持数据有序非常有效。然而，使用二叉树也会产生一个问题，因为你不能改变元素的值，因为这样做会使排序无效。因此，进行更改需要从容器中移除一个元素，然后插入一个新元素。这比在另一个容器中这样做效率更低，所以如果您需要对应用程序中的数据进行大量更改，您将需要选择不同的容器。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。