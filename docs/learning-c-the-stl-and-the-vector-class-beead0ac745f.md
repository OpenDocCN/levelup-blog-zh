# 学习 c++:STL 和 vector 类

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-the-vector-class-beead0ac745f>

![](img/4ff7c27ba58a42e7fa80670230d482b5.png)

图片由 mathinsight.org 提供

标准模板库中最流行的容器是`vector`类。当需要对数据进行顺序排序时，vector 很快成为所有 C++程序的首选容器。大多数 C++编程专家(以及 C++教科书作者)都认识到，向量比原始数组具有更好的灵活性，并且对于大多数应用程序来说，向量足够高效，但对于大多数数据密集型场景来说则不然。

在本文中，我将讨论如何使用`vector`类进行顺序数据存储。

# 数组的问题

使用原语(我使用原语是指数组是内置的，不是库的一部分)数组有两个主要问题。首先，数组的大小是固定的，所以您必须预先知道在程序的整个生命周期中有多少元素将存储在数组中。另一个问题是，当您将数组传递给函数时，您必须将数组的大小也传递给函数，这使得函数定义和函数调用比它们需要的更复杂。解决这两个问题的一个好方法是使用`vector`类。

# 创建向量

创建 vector 之前的第一步是将`vector`类导入到程序中，如下所示:

```
#include <vector>
```

下一步是声明一个新的 vector 实例。`vector`类是一个模板化的类，所以你必须至少在尖括号内提供 vector 的数据类型。这里有几个例子:

```
vector<int> numbers;
vector<string> names;
vector<bool> flags;
```

您还可以声明一个新的 vector 实例，其初始容量为*。我需要花一点时间来解释容量的含义。当您声明 vector 实例时，初始容量为 0。当你把一个元素加到 vector 上，容量就变成了 1。当您添加第二个元素时，容量变为 2。当您添加第三个元素时，容量变为 4。当您添加第五个元素时(我跳过了第四个元素)，容量变为 8。以使得每当向量变满时容量翻倍。*

*这种容量分配需要一点点开销，所以您可能希望指定一个初始容量来避免早期分配，即使它们很小。以下是您的操作方法:*

```
*vector<int> numbers(32);
vector<string> names(16);*
```

*最后，您可以声明一个新的向量，并使用初始化列表用数据初始化它。这里有两个例子:*

```
*vector<int> grades = {88, 91, 77, 84, 100};
vector<string> beatles = {"John", "Paul", "George", "Ringo"};*
```

# *向向量添加数据*

*向向量添加数据的标准方法是`push_back`函数。这个函数在向量的后面放置一个新的数据元素。这里有一个例子:*

```
*vector<int> numbers;
for (int i = 1; i <= 20; i++) {
  numbers.push_back(i);
}*
```

*如果向量已经被分配，可以使用下标符号将数据添加到向量中:*

```
*vector<int> numbers(20);
for (int i = 0; i < 20; i++) {
  numbers[i] = i+1;
}*
```

*不过，通常情况下，您应该使用`push_back`函数来添加数据。*

# *访问矢量数据*

*有几种方法可以访问向量中的数据。一种方法是使用`at`函数。`at`函数将一个索引位置作为参数，并返回该位置的元素。下面是一个使用`at`功能的例子:*

```
*int main()
{
  vector<int> numbers(20);
  for (int i = 0; i < 20; i++) {
    numbers[i] = i+1;
  }
  for (unsigned i = 0; i < numbers.size(); i++) {
    cout << numbers.at(i) << " ";
  }
  return 0;
}*
```

*您也可以像处理数组一样使用下标操作符`[]`:*

```
*for (unsigned i = 0; i < numbers.size(); i++) {
  cout << numbers[i] << " ";
}*
```

*你可以使用迭代器来遍历一个向量。我之前已经写了关于迭代器的文章，所以我将在这里演示如何使用它:*

```
*for (auto iter = numbers.begin(); iter != numbers.end(); iter++) {
  cout << *iter << " ";
}*
```

*最后，如果你想访问 vector 的每个元素，你可以使用一个 range `for`循环:*

```
*for (int n : numbers) {
  cout << n <<  " ";
}*
```

# *尺寸函数*

*您可能会注意到，在上面的访问示例中，我添加了一个新函数— `size`。该函数返回向量中当前元素的数量，并为程序员提供了在不完全了解向量大小的情况下安全遍历向量的能力。*

*`size`函数的一个真正优势是当你在函数中访问一个向量时。对于数组，您必须将大小和数组一起传递给函数，但是对于向量，您不必传递向量中元素的数量。这里有一个例子:*

```
*double compAvg(vector<int> vec) {
  int total = 0;
  for (unsigned i = 0; i < vec.size(); i++) {
    total += vec[i];
  }
  return static_cast<double>(total) / vec.size();
}int main()
{
  vector<int> grades = {81, 77, 91, 100, 83};
  double avgGrade = compAvg(grades);
  cout << "The average grade is: " << avgGrade << endl;
  return 0;
}*
```

*我可以在函数中使用一个 range `for`循环来避免超出数组范围的可能性，如下所示:*

```
*double compAvg(vector<int> vec) {
  int total = 0;
  for (int n : vec) {
    total += n;
  }
  return static_cast<double>(total) / vec.size();
}* 
```

*这个例子展示了数组和向量之间的另一个重要区别。不能在传递给函数的数组上使用 range `for`循环，因为编译器无法在内存中找到该数组的起始点。*

# *作为函数参数和返回值的向量*

*向量通过值传递给函数。这意味着如果你想修改一个函数中向量的内容，你必须通过引用传递这个向量。这里有一个例子:*

```
*void curveGrades(vector<int> &vec, int amount) {
  for (unsigned i = 0; i < vec.size(); i++) {
    vec[i] += amount;
  }
}void printVec(vector<int> vec) {
  for (int n : vec) {
    cout << n << " ";
  }
}int main()
{
  vector<int> grades = {81, 77, 91, 100, 83};
  printVec(grades); // displays 81 77 91 100 83
  cout << endl << endl;
  curveGrades(grades, 5);
  printVec(grades); // displays 86 82 96 105 88
  return 0;
}*
```

*你也可以从函数中返回一个向量。下面是一个将随机数赋给向量并将其返回给调用程序的函数:*

```
*#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;vector<int> initVec(int numElements) {
  srand(time(0));
  vector<int> vec;
  for (unsigned i = 1; i <= numElements; i++) {
    vec.push_back(rand() % 1000 + 1);
  }
  return vec;
}void printVec(vector<int> vec) {
  for (int n : vec) {
    cout << n << " ";
  }
}int main()
{
  vector<int> numbers = initVec(20);
  printVec(numbers);
  return 0;
}*
```

# *插入向量和从向量中移除*

*将元素插入向量需要使用迭代器。要使用的函数是 insert，函数有几个重载版本。这里我将演示最常见的一个——*insert(position，element)* 。该模板指示元素参数被插入到第一个参数中给定的迭代器位置之前的位置。下面的例子演示了如何将一个数字插入到一个排序的向量中(在这个例子中，你可以免费看到一个关于排序算法如何工作的例子):*

```
*#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;void printVec(vector<int> vec) {
  for (int n : vec) {
    cout << n << " ";
  }
}int main()
{
  vector<int> grades = {82, 71, 89, 77, 91, 75};
  sort(grades.begin(), grades.end());
  printVec(grades); // displays 71 75 77 82 89 91
  cout << endl << endl;
  int grade = 86;
  auto iter = grades.begin();
  while (*iter < grade) {
    iter++;
  }
  grades.insert(iter, grade);
  printVec(grades); // displays 71 75 77 82 86 89 91
  return 0;
}*
```

*`while`循环遍历矢量，直到它到达一个比我们想要插入的坡度更大的坡度。这是插入点，因此我们调用 insert 函数将新的等级放在适当的位置。*

*移除元素也涉及迭代器。`erase`函数删除作为参数给出的迭代器位置的元素。首先，这里是擦除函数的模板:*擦除(位置)*。现在让我们看一个例子:*

```
*void printVec(vector<string> vec) {
  for (string s : vec) {
    cout << s << " ";
  }
}int main()
{
  vector<string> beatles = {"John", "Paul", "George", "Pete",    
                            Ringo"};
  string fifthBeatle = "Pete";
  auto iter = beatles.begin();
  while (*iter != fifthBeatle) {
    iter++;
  }
  beatles.erase(iter);
  printVec(beatles); // displays John Paul George Ringo
  return 0;
}*
```

# *两个效用函数*

*vector 类中有几个实用函数。这里我要提到的两个是`empty`和`clear`。`empty`函数是一个布尔函数，如果该函数为空，则返回 true，否则返回 false。`clear` 功能将删除一个矢量的所有元素。以下是如何使用它们的示例:*

```
*int main()
{
  vector<int> numbers = {1,2,3,4,5};
  if (!numbers.empty()) {
    numbers.clear();
  }
  cout << "Number of elements: " << numbers.size() << endl;
  return 0;
}*
```

*在这个程序中，如果`numbers`向量不为空，它将被清除。*

# *向量是新的数组*

*如果你阅读过去二十年或更多年的任何编程教科书，讨论的第一个数据结构是数组。对于 C++(以及所有语言)来说，这应该会改变。vector 是一个更加灵活的容器，具有非常好的性能，并且更容易被初级程序员成功使用。如果您没有使用 vector(或其他语言中类似的容器类型)作为您的首选容器类型，现在是时候改变您的做法了。*

*感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。*

*![](img/cc28fc8ea8bb70b81958696a5a6d62cd.png)*

*由 [Tamanna Rumee](https://unsplash.com/@tamanna_rumee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片*