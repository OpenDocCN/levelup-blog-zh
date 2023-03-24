# 学习 c++:STL 和队列类

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-the-queue-class-6d8ba7a73d9c>

![](img/5e9b97bb1e654de51ddc590ee49cadb2.png)

查尔斯·德鲁维奥在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`queue`是一个具有先进先出(FIFO)行为的容器。排队的经典例子是杂货店排队。数据放在队列的后面，数据放在队列的前面，一次只能有一个数据元素进入或离开队列。在本文中，我将演示如何使用标准模板库的(STL) `queue`类，并讨论一些使用队列的应用程序。

# 队列概述

当我们美国人使用排队这个词时，英国人和其他欧洲人使用排队这个词。每个人都熟悉队列是如何工作的。当你进入一条线时，你从后面进入这条线，除非你是唯一一个排队的人，然后你在这条线的后面和前面。

当人们处理完事务后，他们从前面离开队伍，下一个人移到队伍的前面。当用一条线进行交易时，你通常只与这条线的前端或后端进行交易。不在队伍前面或后面的人不能上场，除非有人试图“切断”队伍，但这是违反规则的，我在这里不会考虑这种可能性。

`queue`容器以同样的方式工作。数据从后面进入队列，从前面离开队列。您只能检查队列的两个位置—前面和后面。这些接口约束使得使用队列的应用程序更有效，老实说，更容易使用，因为通常只有一种方法来执行操作。

# 声明队列

要使用`queue`类，您必须首先引用正确的头文件:

```
#include <queue>
```

`queue`容器是一个模板类，所以在声明新队列时必须提供一个数据类型。以下是一些例子:

```
queue<string> names;
queue<double> floats;
queue<int> ids;
```

不能用初始值设定项列表初始化队列，也不能将容量指定为构造函数参数。

# 向队列中添加数据并检查前端和后端

使用`push`功能将数据添加到队列中。这里有一个例子:

```
#include <iostream>
#include <queue>
#include <vector>
using namespace std;int main()
{
  vector<string> people = {"Meredith", "Allison", "Mason"};
  queue<string> names;
  for (const string person : people) {
    names.push(person);
  }
  return 0;
}
```

`front`和`back`功能分别用于检查队列的前面和后面。我们可以将下面的代码片段添加到上面的程序中，看看谁在队列的前面，谁在后面:

```
cout << "Front of queue: " << names.front() << endl; // Meredith
cout << "Back of queue: " << names.back() << endl; // Mason
```

# 从队列中删除数据并显示队列的大小

从队列中删除数据的唯一方法是使用`pop`函数。此函数删除该行前面的元素，将所有其他元素上移一个位置。

您可以使用`size`函数来查看一个元素已经从队列中弹出。下面是一个演示了`pop`函数和`size`函数的程序:

```
int main()
{
  vector<string> people = {"Meredith", "Allison", "Mason"};
  queue<string> line;
  for (const string person : people) {
    line.push(person);
  }
  do {
    cout << "Now serving: " << line.front() << endl;
    line.pop();
    cout << "Number waiting in line: " << line.size() << endl;
  } while (!line.empty());
  if (line.empty()) {
    cout << "Line empty." << endl;
  }
  return 0;
}
```

这个程序的输出是:

```
Now serving: Meredith
Number waiting in line: 2
Now serving: Allison
Number waiting in line: 1
Now serving: Mason
Number waiting in line: 0
Line empty.
```

这就结束了我对队列类及其函数的回顾。现在让我们看看队列的一些应用程序。

# 排队申请

队列最常见的应用是在模拟中。演示复杂的模拟超出了本文的范围，但是我可以演示一个简单的模拟——为一个方块舞配对。我从威廉·福特和威廉·托普的教科书《C++ 的 T4 数据结构》中引用了这个例子。

这种模拟是通过将广场舞的男性和女性参与者放置在两个单独的队列中来进行的。到了开始跳舞的时候，从每个队列的最前面抽出一男一女组成舞伴。如果两个队列的人数不相等，群众演员必须等到下一场舞会。为了简单起见，我将从两个向量中获取数据(男性和女性)，而不是像教科书示例那样从一个文件中获取。

程序是这样的:

```
#include <iostream>
#include <queue>
#include <vector>
using namespace std;int main()
{
  vector<string> men =
    {"George", "Bill", "Bob", "Dave", "Harold", "Dan", "John"};
  vector<string> women =
    {"Jane", "Sandra", "Shirley", "Louise", "Roberta"};
  queue<string> menDancers, womenDancers;
  for (string man : men) {
    menDancers.push(man);
  }
  for (string woman : women) {
    womenDancers.push(woman);
  }
  cout << "Current dance partners are: " << endl << endl;
  do {
    man = menDancers.front();
    woman = womenDancers.front();
    menDancers.pop();
    womenDancers.pop();
    cout << woman << " and " << man << endl;
  } while ((!menDancers.empty() && !womenDancers.empty()));
  if (!menDancers.empty()){
    cout << endl << endl
         << "Men dancers waiting for next dance: "
         << endl << endl;
    men.clear();
    while (!menDancers.empty()) {
      men.push_back(menDancers.front());
      cout << menDancers.front() << endl;
      menDancers.pop();
    }
  }
  if (!womenDancers.empty()){
    cout << endl << endl
         << "Women dancers waiting for next dance: "
         << endl << endl;
    women.clear();
    while (!womenDancers.empty()) {
      men.push_back(menDancers.front());
      cout << womenDancers.front();
      womenDancers.pop();
    }
  }
  return 0;
}
```

以下是该程序的输出:

```
Current dance partners are:Jane and George
Sandra and Bill
Shirley and Bob
Louise and Dave
Roberta and HaroldMen dancers waiting for next dance:Dan
John
```

福特和托普演示的另一个例子是基数排序。[这里](https://en.wikipedia.org/wiki/Radix_sort)描述了基数排序及其工作原理。下面是我用队列进行基数排序的程序:

```
#include <iostream>
#include <queue>
#include <cstdlib>
#include <ctime>
#include <cmath>
using namespace std;void radixSort(vector<int> &vec, int n) {
  queue<int> bins[10];
  int maxDigits=3;
  int currentDigit=0;
  while (currentDigit < maxDigits) {
    for(int i=0; i<n; i++){
      int divisor=pow(10,currentDigit);
      int num = vec[i];
      int digitValue = static_cast<int>((num/divisor)%10);
      bins[digitValue].push(num);
    }
    int i=0;
    for(int k=0;k<10;k++){
      while (!bins[k].empty()){
        int temp=bins[k].front();
        vec[i]=temp;
        bins[k].pop();
        i++;
      }
    }
    currentDigit++;
  }
}int main()
{
  const int SZ = 50;
  vector<int> numbers;
  for (int i = 0; i < SZ; i++) {
    numbers.push_back(rand() % 100 + 1);
  }
  radixSort(numbers, SZ);
  for (int i = 0; i < SZ; i++) {
    cout << numbers[i] << " ";
  }
  return 0;
}
```

下面是这个程序运行一次的输出:

```
42 68 35 1 70 25 79 59 63 65 6 46 82 28 62 92 96 43 28 37

1 6 25 28 28 35 37 42 43 46 59 62 63 65 68 70 79 82 92 96
```

# 其他队列应用程序

在计算机科学和运筹学的许多领域中，每当需要将数据保存在存储器中以备后用时，就会使用队列。在这些应用中，队列作为一个缓冲区工作，就像内存在计算机内存中被缓冲一样。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。