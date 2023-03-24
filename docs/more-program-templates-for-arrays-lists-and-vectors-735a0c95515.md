# 更多数组、列表和向量的程序模板

> 原文：<https://levelup.gitconnected.com/more-program-templates-for-arrays-lists-and-vectors-735a0c95515>

## 处理顺序数据的模式

![](img/6f0fbaf454fb9ccd93f354766e7f0156.png)

凯文·Ku 在 [Unsplash](https://unsplash.com/s/photos/program-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在上一篇文章中，我介绍了三个用于处理顺序数据集合(数组、列表或向量)的程序模板。在本文中，我将讨论这类数据集合的另外三个程序模板——如何将数据插入到顺序集合中，如何复制顺序集合，以及如何插入和复制顺序集合。

# 为什么程序模板对程序员学习很重要

程序模板代表程序员用来快速评估如何解决问题的大量编程知识。专家学者认为组块是获得某一领域专业知识的必要条件。组块是通过研究一个领域中的常见使用模式并练习使用这些组块来解决越来越难的问题而实现的。一旦专家吸收了许多语块，他们就能比没有掌握那么多语块的人表现出更高水平的专业技能。

# 插入收藏

我们要看的第一个程序模板是*插入到一个集合中*。当您需要将一段数据添加到集合中，并且它不能位于集合的开头或结尾时，可以使用该模板。

当插入一个值序列时，首先要找到要插入新值的位置。然后将插入点右侧的所有值移动一个位置，为新值腾出空间。最后，在移位完成后，将新值插入移位打开的空间。

模板的伪代码如下所示:

*(新值将被插入到集合中的位置 p)
将变量(I)设置为集合的最后一个元素的索引加 1(k)
当 I 大于 p+1 时重复:
coll[I]= coll[I-1]
coll[p]=新值
将集合元素的编号加 1*

这里有一个使用这个模板的问题:你有一组按数字顺序排列的成绩，你需要在这组成绩的中间添加一个成绩来保持顺序。

这里有一个 JavaScript 解决方案:

```
let grades = new Array(71, 82, 85, 88, 89, 91);let new_grade = 86// find place for grade
let pos;
for (pos = 0; pos < grades.length; pos++) {
  if (grades[pos] > new_grade) {
    break;
  }
}
// shift the elements to the right
for (let index = grades.length; index > pos; index--) {
  grades[index] = grades[index-1];
}
// insert the new grade
grades[pos] = new_grade;
```

下面是使用数组的 C++解决方案:

```
int main()
{
  const int NUM_GRADES = 10;
  int grades[NUM_GRADES] = {71, 82, 85, 88, 89, 91,0,0,0,0};
  int new_grade = 86;
  int pos;
  for (pos = 0; pos < NUM_GRADES; pos++) {
    if (grades[pos] > new_grade) {
      break;
    }
  }
  for (int index = NUM_GRADES-1; index > pos; index--) {
    grades[index] = grades[index-1];
  }
  grades[pos] = new_grade;
  for (int grade : grades){
    cout << grade << " ";
  }
  return 0;
}
```

将数据插入到顺序集合中可能会非常昂贵。出于这个原因，大多数现代编程语言库都包括一些其他类型的集合，比如列表，这使得插入和删除的代价更低。如果你不熟悉链表的概念，你应该通过互联网搜索来探索它们是如何工作的。

# 深层复制收藏

许多初学编程的人认为他们可以通过简单的赋值来复制一个集合，比如一个数组或一个列表。这似乎是可行的，但是你剩下的就是所谓的*浅拷贝*。

当您将一个现有集合分配给一个新集合时，您正在进行引用，因此新集合仅引用旧集合。结果是，如果旧的集合被改变，则该改变反映在新的集合中。

下面演示了在 JavaScript 中进行浅层复制时会发生什么:

```
let grades = new Array(77, 88, 99);
let grades1 = grades;
grades[1] = 89;
print(grades);
print(grades1);
```

这个程序的输出是:

```
77, 89, 99
77, 89, 99
```

您会注意到 grades1 数组反映了 grades 数组中的变化。这就是浅拷贝的情况。

解决方案是用一个*深度拷贝*将一个集合拷贝到一个新集合。深层副本是旧集合到新集合的一个元素一个元素的赋值。以下是深层拷贝的计划模板:

*声明没有元素的新集合
for i =索引旧集合的开始到索引旧集合的结束:
将旧集合的 I 元素赋给新集合的 k 元素*

下面是如何在 JavaScript 中使用该程序模板来创建深层副本:

```
let grades = new Array(77, 88, 99);
let grades1 = new Array();
for (let i = 0; i < grades.length; i++) {
  grades1[i] = grades[i];
}
grades[1] = 89;
print(grades);
print(grades1);
```

这个程序的输出是:

```
77, 89, 99
77, 88, 99
```

这表明更改 grades 数组并不会更改 grades1 数组，因为 grades1 数组由于深度复制而没有引用 grades 数组。

Python 中也存在同样的行为。除非您想要浅拷贝的行为，否则您应该总是制作列表的深拷贝。

C++不允许你将一个数组赋给另一个数组，因为数组名是指向数组第一个元素的指针。您将需要使用一个函数来执行数组赋值，这将导致您总是制作数组的深层副本。

# 复制收藏时插入

通常，您会想要复制一个集合，同时向该集合中插入新元素。该程序模板将旧集合中的元素复制到新集合中，直到插入点，然后将新元素添加到新数组的末尾。

如果您在 C++这样的语言中工作，数组的大小是固定的，并且如果您想要添加新元素，您将需要创建一个新的、更大的数组(假设您不想使用动态内存和指针)，则此模板非常有用。

下面是模板伪代码:

*当有元素要插入时，执行:
从源集合复制到目标集合，直到插入点
添加要插入到目标集合末尾的元素*

这里有一个问题，我们可以使用这个模板来解决。您有一个来自一个班级的十个成绩的数组，并且您决定要将另一个班级的十个成绩相加，这样您就可以计算两个班级的平均值。下面是一个 C++解决方案:

```
int main()
{
  const int NUM_GRADES = 10;
  int class_one[] = {91, 77, 74, 82, 98, 83, 77, 99, 80, 68};
  int class_two[] = {84, 74, 88, 93, 100, 88, 62, 81, 93, 75};
  int both_size = NUM_GRADES * 2;
  int both_classes[both_size];
  for (int i = 0; i < NUM_GRADES; i++) {
    both_classes[i] = class_one[i];
  }
  for (int i = NUM_GRADES, start = 0; i < both_size;
                                     i++, start++) {
    both_classes[i] = class_two[start];
  }
  int total = 0;
  for (int grade : both_classes) {
    total += grade;
  }
  double average = static_cast<double>(total) / both_size;
  cout << "The average of both classes is: " << average
       << endl;
  return 0;
}
```

在 C++中，更好的解决方案是使用向量而不是数组，尤其是当速度不是项目中最重要的因素时。向量可以很容易地增长和收缩，它们不会像过去那样影响性能。大多数编程专家现在强烈推荐使用向量而不是数组，除非访问速度至关重要。

用像 Python 这样的动态语言编写这个程序也容易得多，特别是因为 Python 允许列表连接。下面是上面用 Python 编写的程序:

```
class_one = [91, 77, 74, 82, 98, 83, 77, 99, 80, 68]
class_two = [84, 74, 88, 93, 100, 88, 62, 81, 93, 75]
both_classes = class_one + class_two
total = 0
for i in range(0, len(both_classes)):
  total = total + both_classes[i]
average = total / len(both_classes)
print("The average of the two classes is:", average)
```

我的 C++学生讨厌看到这样的例子。

# 学习模板是发展专业技能的关键

我向我的学生灌输的一件事是，像本文中讨论的学习模板对于培养计算机程序员的专业技能至关重要。当一个程序员面临一个问题，并能迅速确定解决问题的合适模板时，他们就节省了时间，可以用来在项目中做更有创造性的工作，就像国际象棋大师使用他们的组块来帮助他们更快地走更好的棋。

如果你想成为一名专家程序员，花时间学习和掌握程序模板。