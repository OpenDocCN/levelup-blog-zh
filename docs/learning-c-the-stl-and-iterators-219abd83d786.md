# 学习 c++:STL 和迭代器

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-iterators-219abd83d786>

![](img/2766732a9efbbba43030f26cd916ca6f.png)

照片由 [CJ Dayrit](https://unsplash.com/@cjred?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

理解如何使用 C++的标准模板库(STL)容器的关键之一是理解迭代器是如何工作的。在本文中，我将展示迭代器如何在高层工作(range `for`循环),然后如何更直接地使用它们来管理 STL 容器的移动。

# 迭代器和循环的范围

大多数编程专家现在建议，当您考虑程序的数据存储时，您应该选择使用灵活的容器，如 vector，而不是更不灵活的容器，如内置数组(如果您真的需要数组，STL 的`array`类是更好的选择)。

同样，这些专家现在建议，当您访问容器的所有元素时，应该在索引循环上使用 for-each-type 循环，以便最大限度地减少代码中出现数组边界错误的可能性。在 C++中，这种类型的循环是 range for loop，自 C++11 发布以来就可以使用。

范围`for` 循环的语法模板如下所示:

*for(数据类型元素:容器){
body；
}*

让我们看看这在实践中是如何工作的。以下程序创建一个简单的数字向量，并使用 range `for`循环显示向量中的每个数字:

```
#include <iostream>
#include <vector>
using namespace std;int main()
{
  vector<int> numbers{1,2,3,4,5};
  for (int number : numbers) {
    cout << number << " "; // displays 1 2 3 4 5
  }
  return 0;
}
```

range for 循环的工作方式是创建一个指向向量的第一个元素的迭代器，然后依次访问向量的每个元素，直到迭代器到达向量的最后一个元素，然后循环终止。

有了这个背景，我们就可以了解如何使用迭代器直接访问容器的元素了。

# 直接使用迭代器

范围`for`循环是迭代器的间接使用，因为迭代器是在幕后使用的。让我们看看如何使用迭代器直接遍历容器的内容。

你可以把迭代器想象成一个指针，可以间接访问容器元素。迭代器使用以下语法声明:

*容器类型<数据类型>::迭代器迭代器名；*

下面的代码片段演示了如何将迭代器声明为整数向量:

```
vector<int>::iterator iter;
```

一旦迭代器被声明，它必须被初始化。STL 容器有这样的功能。如果我们想创建一个标准的迭代器从容器的开头移动到结尾，这个函数就叫做`begin`。下面是它的使用方法:

```
vector<int> numbers{1,2,3,4,5};
vector<int>::iterator iter;
iter = numbers.begin();
```

为迭代器指定数据类型有点冗长，所以大多数程序员使用 auto 关键字在一条语句中声明和初始化迭代器。下面是如何做到这一点，使用上面例子中的向量:

```
vector<int> numbers{1,2,3,4,5};
auto iter = numbers.begin();
```

迭代器`iter`现在指向向量的第一个元素。我们可以通过使用解引用操作符(`*`)解引用迭代器来访问迭代器所指向的值，如下所示:

```
cout << "first element: " << *iter; // displays first element: 1
```

要移动到下一个元素，可以使用 increment 运算符将迭代器移动到下一个元素:

```
iter++;
cout << "second element: " << *iter;//displays second element: 2
```

现在您可以看到如何使用迭代器遍历容器。但是你怎么知道什么时候停止呢？STL 容器还有另一个功能，`end`，表示容器的结束。如果您是容器的结尾，该函数将返回 true 值，否则将返回 false 值。

下面是如何在一个循环中使用`end`函数来遍历一个向量的所有元素:

```
#include <iostream>
#include <vector>
using namespace std;int main()
{
  vector<int> numbers{1,2,3,4,5};
  auto iter = numbers.begin();
  while (iter != numbers.end()) {
    cout << *iter << " "; // displays 1 2 3 4 5
    iter++;
  }
  return 0;
}
```

# 在容器上向后移动

上面的例子演示了如何使用前向迭代器向前移动。还可以使用反向迭代器从容器的最后一个元素向后移动到容器的第一个元素。

创建反向迭代器的函数是`rbegin`。决定你是否在一个容器的开头的函数是`rend`。下面是一个使用它们在向量上向后移动的示例:

```
#include <iostream>
#include <vector>
using namespace std;int main()
{
  vector<int> numbers{1,2,3,4,5};
  auto iter = numbers.rbegin();
  while (iter != numbers.rend()) {
    cout << *iter << " "; // displays 5 4 3 2 1
    iter++;
  }
  return 0;
}
```

# 使用带有迭代器的 for 循环

我上面给出的例子使用了一个`while`循环来遍历向量，但是没有理由不能使用一个`for` 循环，for 循环甚至可能是迭代器的首选方法。下面是上面用`for`循环改写的两个例子:

```
#include <iostream>
#include <vector>
using namespace std;// forward iterator
int main()
{
  vector<int> numbers{1,2,3,4,5};
  for (auto iter = numbers.begin(); iter != numbers.end();
       iter++) {
    cout << *iter << " "; // displays 1 2 3 4 5
  }
  return 0;
}#include <iostream>
#include <vector>
using namespace std;// reverse iterator
int main()
{
  vector<int> numbers{1,2,3,4,5};
  for (auto iter = numbers.rbegin(); iter != numbers.rend();
       iter++) {
    cout << *iter << " "; // displays 5 4 3 2 1
  }
  return 0;
}
```

# 杂项迭代器技术

在上面的例子中，我总是将迭代器递增 1，但是没有理由你不能递增你想要的任何值。以下示例通过将迭代器递增 2 来显示随机生成的数字向量的每隔一个元素:

```
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>using namespace std;int main()
{
  vector<int> numbers;
  srand(time(0));
  for (int i = 1; i <= 20; i++) {
    numbers.push_back(rand() % 100 + 1);
  }
  for (auto iter = numbers.begin(); iter != numbers.end();
       iter += 2) {
    cout << *iter << " ";
  }
  return 0;
}
```

另一种写法是使用`advance`函数。这个函数接受当前迭代器和一个数字，并将迭代器推进到容器中的许多位置。下面的代码片段演示了`advance`函数是如何工作的:

```
vector<int> numbers {1,2,3,4,5};
auto iter = numbers.begin();
advance(iter, 2);
cout << *iter; // displays 3
```

next 函数将迭代器返回到容器中当前迭代器位置之后的下一个位置。这里有一个例子:

```
vector<int> numbers {1,2,3,4,5};
auto iter = numbers.begin();
auto next_iter = next(iter);
cout << *next_iter; // displays 2 
```

最后一个例子与其说是迭代器技术，不如说是迭代器的一种用法。STL 算法`sort`使用迭代器来决定将容器的哪一部分排序。例如，如果你想对整个容器进行排序，你可以使用`begin`和`end` 函数来指定整个容器。这里有一个关于`sort`功能如何工作的例子:

```
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <algorithm>using namespace std;int main()
{
  vector<int> numbers;
  srand(time(0));
  for (int i = 1; i < 20; i++) {
    numbers.push_back(rand() % 100 + 1);
  }
  for (int num : numbers) {
    cout << num << " ";
  }
  sort(numbers.begin(), numbers.end());
  cout << endl;
  for (int num : numbers) {
    cout << num << " ";
  }
  return 0;
}
```

这个程序运行一次的输出是:

```
62 35 56 62 7 74 8 46 4 53 1 26 7 94 27 64 31 63 100
1 4 7 7 8 26 27 31 35 46 53 56 62 62 63 64 74 94 100
```

# 迭代器的重要性

排序函数并不是 STL 中唯一需要使用迭代器的函数。迭代器是所有 STL 容器的核心，如果你想充分利用 STL 容器，你需要熟悉迭代器的工作原理和使用方法。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。