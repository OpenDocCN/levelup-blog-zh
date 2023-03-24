# 学习 c++:STL、地图和多重地图

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-maps-and-multimaps-993f5b931287>

![](img/86f7abca0c5dcf2a91b8465d9a67371b.png)

[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有许多应用程序要求数据以键值关系与其他数据相关联。字典、电话列表和清单只是三个例子。标准模板库(STL)中存储关联数据的主要容器是`map`类。该类允许存储唯一键及其相关值。与 map 类相关联的是允许重复键的`multimap`类。在本文中，我将向您展示如何使用地图和多地图来处理关联数据。

# 地图和多地图类的基础

使用平衡二叉树实现了`map`和`multimap`类。每个键/值对是树中的一个节点，节点根据键值放入树中。这意味着映射(和多映射)被很好地排序，并且为了发现与它相关的值而搜索一个键是非常有效的。然而，这种结构也意味着，在不提供键的情况下搜索一个值是非常低效的，甚至没有真正在类中实现。

对地图或多地图的另一个低效操作是更改键。你不能直接这样做；您必须删除键/值对，然后用修改后的键插入一个新对。这是因为就地修改键会使内部二叉树不平衡，并且作为修改操作的一部分，该树必须重新平衡自身。这种操作效率很低，实际上是不允许的。

# 创建地图

为使用映射而输入的预处理器指令是:

```
#include <map>
```

一个`map` 是一个模板类，它需要键和值的模板参数，按照这个顺序。以下是一些例子:

```
map<string, string> phoneList;
map<string, int> birthYears;
map<int, int> grades; // where first int is id number
```

如果要修改映射的排序顺序，可以提供第三个模板参数:

```
map<int, int, greater<int>> grades;
```

您可以提供一个初始化列表，用一些键/值对来初始化映射:

```
map<string, int> grades = {{"Doe", 81}, {"Brown", 92},
                           {"Smith", 88}};
```

# 快速回顾配对结构

您可能已经注意到在上面的初始化列表中，键/值对是以 *{key，value}* 的形式输入的。该表单用于定义一个`pair`，它是在`utility`库中找到的一个结构。`pair`结构有两个字段`first`和`second`。以下是如何在地图之外创建一个`pair`:

```
#include <iostream>
#include <utility>
using namespace std;int main()
{
  pair<string, int> student;
  student.first = "Brown";
  student.second = 88;
  cout << "Name: " << student.first << ", Grade: "
       << student.second << endl;
  return 0;
}
```

这个程序的输出是:

```
Name: Brown, Grade: 88
```

在一些情况下，当您使用地图时会直接使用这些`pair`函数，因此了解配对是如何工作的是个好主意。

# 向地图添加数据

`insert`函数用于向地图添加新元素:

```
#include <iostream>
#include <map>
using namespace std;int main()
{
  map<string, int> grades = {{"Doe", 81}, {"Brown", 92},
                             {"Smith", 88}};
  grades.insert({"Jones", 79});
  return 0;
}
```

我不会再提供一个完整的程序，除非其中一个预处理器指令发生变化。

`insert`函数返回一个`pair`作为返回值，包含一个指向`first`字段中插入点的迭代器和一个表示`second`字段中插入成功或失败的布尔值。这里有一个例子:

```
#include <iostream>
#include <map>
#include <utility>
using namespace std;int main()
{
  map<string, int> grades = {{"Doe", 81}, {"Brown", 92},
                             {"Smith", 88}};
  pair<map<string, int>::iterator, bool> success =
    grades.insert({"Jones", 79});
  if (success.second) {
    cout << "inserted at: " << success.second << endl;
  }
  return 0;
}
```

# 访问地图数据

如果您试图访问与特定键相关的值，您可以使用`[]`操作符并将键放在括号内，或者使用`at`函数将键作为参数。下面是一个使用两种方法的示例:

```
int main()
{
  map<string, int> grades = {{"Doe", 81}, {"Brown", 92},
                             {"Smith", 88}};
  grades.insert({"Jones", 79});
  string name = "Brown";
  cout << name << "'s grade: " << grades[name] << endl;
  name = "Doe";
  cout << name << "'s grade: " << grades.at(name) << endl;
  return 0;
}
```

这个程序的输出是:

```
Brown's grade: 92
Doe's grade: 81
```

如果你想访问地图的所有元素，你可以使用一个 range `for`循环，就像这样:

```
int main()
{
  map<string, int> grades = {{"Doe", 81}, {"Brown", 92},
                             {"Smith", 88}};
  grades.insert({"Jones", 79});
  for(auto iter = grades.begin(); iter != grades.end();
      iter++) {
    cout << iter->first << ": " << iter->second << endl;
  }
  return 0;
}
```

每次迭代返回一个指向 pair 结构的迭代器。要获取键，必须引用`first`字段，要获取值，必须引用`second`字段。要访问这些字段，必须使用箭头操作符`->`而不是点操作符。

# 从地图中移除元素

使用`erase`功能移除地图元素。该函数将一个键作为参数，并删除具有匹配键的元素。该函数返回移除的元素数量。

这里有一个例子:

```
int main()
{
  map<string, int> grades = {{"Doe", 81}, {"Brown", 92},
                             {"Smith", 88}};
  string key = "Doe";
  int numRemoved = grades.erase(key);
  cout << "Removed " << numRemoved << " elements." << endl;
  return 0;
}
```

# 使用多地图

一个`multimap`只是一个允许重复键的映射。有时这是使用的正确容器，比如当你建立一个字典或者一个电话列表，列表上的人可以有相同的名字。下面的程序片段将演示如何使用 multimap 创建一个字典，其中同一个单词可以有多种含义:

```
multimap<string, string> dictionary;
dictionary.insert({"bark", "outer covering of a tree"});
dictionary.insert({"bark", "sound a dog makes"});
dictionary.insert({"nail", " as in toenail, fingernail"});
dictionary.insert({"nail", "sharp sliver of metal"});
dictionary.insert({"mine", "possesive adjective"});
dictionary.insert({"mine", "place where minerals are located"});
```

我们可以使用 range `for` 循环遍历整个字典:

```
for (const auto elem : dictionary) {
  cout << elem.first << ": " << elem.second << endl;
}
```

现在让我们遍历一个单词的定义。为此，我需要引入两个新函数， `lower_bound`和`upper_bound`。这些函数将一个键作为参数。`lower_bound` 函数返回插入新元素的第一个位置，或者找到匹配键的第一个元素的位置。`upper_bound`函数返回插入新元素的最后位置，或者找到匹配键的最后一个元素的位置。

我们可以使用这些函数返回一个单词的所有定义，如下例所示:

```
//find all definitions of one word
string key("mine");
cout << "All definitions of the word mine: " << endl << endl;
for (auto iter = dictionary.lower_bound(key);
     iter != dictionary.upper_bound(key); iter++) {
  cout << iter->first << ": " << iter->second << endl;
}
```

这个程序片段的输出是:

```
All definitions of the word mine:mine: possesive adjective
mine: place where minerals are located
```

# 使用地图和多地图

`map` 和`multimap`容器是专门的容器，应该只在处理关联数据时使用。当应用程序中的键需要唯一时使用`map`类，当应用程序中的键可以重复时使用`multimap`类。这些容器也是经过排序的，所以如果您希望您的键以非升序的顺序出现，您可以更改排序标准。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。