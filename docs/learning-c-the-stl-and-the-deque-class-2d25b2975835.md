# 学习 c++:STL 和 deque 类

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-the-deque-class-2d25b2975835>

![](img/a951149206785edfce8cd6c680069f36.png)

照片由 [Aditya Chinchure](https://unsplash.com/@adityachinchure?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

deque(发音像“一副”卡片)是一个双面队列，您可以在容器的前面或后面添加和删除数据。Deques 不是一个非常常用的容器，但是在一些特殊的应用中有它的用途。当应用程序需要在容器两端添加和删除数据时，deque 可能是首选容器。

本文将演示如何使用 deques，并为您提供一些关于它们在应用程序中的使用的文章。

# 创建德克

与标准模板库(STL)中的其他类一样，`deque`类也是一个模板类。您必须先导入它，然后才能在程序中使用它:

```
#include <deque>
```

用数据类型和名称声明了一个`deque`实例:

```
deque<string> words;
deque<int> numbers;
```

可以用一个列表初始化 Deques:

```
deque<string> names = {"Cynthia", "Jonathan", "Raymond"};
```

# 向队列添加数据

正如我提到的，数据可以添加在队列的前面或后面。其功能是`push_front`和`push_back`。下面是这些功能发挥作用的一个例子:

```
int main()
{
  deque<string> names;
  names.push_back("Cynthia");
  names.push_front("Jonathan");
  names.push_back("Danny");
  names.push_front("Raymond");
  for (const string s : names) {
    cout << s << " "; // Raymond Jonathan Cynthia Danny
  }
  return 0;
}
```

我包含了一个 range `for`循环来演示访问队列元素的顺序。

# 访问队列的元素

我已经演示了一种访问 deque 元素的方法——range`for`循环。如果想通过队列中的索引位置访问元素，可以使用`at`函数:

```
for (unsigned i = 0; i < names.size(); i++) {
  cout << names.at(i) << " ";
}
```

您也可以使用迭代器遍历一个队列:

```
for (auto iter = names.begin(); iter != names.end(); iter++) {
  cout << *iter << " ";
}
```

对于更细粒度的访问，函数`front`和`back`分别用于访问队列前端和后端的元素。下面是它们用法的一个例子:

```
int main()
{
  deque<string> names;
  names.push_back("Cynthia");
  names.push_front("Jonathan");
  names.push_back("Danny");
  names.push_front("Raymond");
  cout << "Front of deque: " << names.front() << endl;
  // Raymond
  cout << "Back of deque: " << names.back() << endl;
  // Danny
  return 0;
}
```

# 删除队列元素

从队列中删除元素最常见的方法是调用`pop_front`函数从队列的前面删除元素，调用`pop_back`函数从队列的后面删除元素。这些是最常见的删除函数，因为使用 deque 的主要原因是为了快速有效地从前面或后面删除元素。如果您不需要这些效率，就不应该使用 deque。

下面是一个在程序中使用`pop_front`和`pop_back`的例子:

```
int main()
{
  deque<string> names;
  names.push_back("Cynthia");
  names.push_front("Jonathan");
  names.push_back("Danny");
  names.push_front("Raymond");
  cout << "Removing the front of the deque: " << endl;
  names.pop_front();
  cout << "Removing the back of the deque: " << endl;
  cout << "The front element is now: "
       << names.front() << endl;  // front is Jonathan
  cout << "The back element is now: " << names.back() << endl;
  // back is Danny
  return 0;
}
```

与其他容器一样，可以通过迭代器移除特定的元素。这里有一个例子:

```
int main()
{
  deque<string> names;
  names.push_front("Mayo");
  names.push_back("Cynthia");
  names.push_front("Jonathan");
  names.push_back("Danny");
  names.push_front("Raymond");
  for (const string s : names) {
    cout << s << " ";
  }
  cout << endl << endl;
  string removeElement = "Mayo";
  auto iter = names.begin();
  while (*iter != "Mayo") {
    iter++;
  }
  names.erase(iter);
  for (const string s : names) {
    cout << s << " ";
  }
  return 0;
}
```

这个程序的输出是:

```
Raymond Jonathan Mayo Cynthia DannyRaymond Jonathan Cynthia Danny
```

# 检查一个空的队列并清除一个队列

deque 类有两个您可以使用的实用函数，`empty`和`clear`。`empty`函数检查队列中是否有数据，返回`true`或`false`，`clear` 函数将删除队列中的所有数据。下面是一个使用这两个函数的程序:

```
int main()
{
  deque<string> names;
  names.push_front("Mayo");
  names.push_back("Cynthia");
  names.push_front("Jonathan");
  names.push_back("Danny");
  names.push_front("Raymond");
  cout << "Size of deque: " << names.size() << endl;
  if (!names.empty()) {
    names.clear();
  }
  cout << "Size of deque: " << names.size() << endl;
  return 0;
}
```

# 使用 Deque 的示例应用程序

deque 的一个用途是确定一个单词是否是回文。如果一个单词是通过把它的字母从后面压入 deque 而形成的，而一个新单词是通过把它的字母也从后面弹出而形成的，那么这个单词就是一个回文。

下面的程序演示了这一点:

```
bool checkPal(string word) {
  deque<char> letters;
  string rword = "";
  for (unsigned i = 0; i < word.size(); i++) {
    letters.push_back(word[i]);
  }
  while (!letters.empty()) {
    rword += letters.back();
    letters.pop_back();
  }
  if (word == rword) {
    return true;
  }
  return false;
}int main()
{
  string aWord;
  cout << "Enter a word: ";
  cin >> aWord;
  if (checkPal(aWord)) {
    cout << aWord << " is a palindrome." << endl;
  }
  else {
    cout << aWord << " is not a palindrome." << endl;
  }
  return 0;
}
```

以下是该程序的两次运行:

```
Enter a word: radar
radar is a palindrome.Enter a word: hello
hello is not a palindrome.
```

# Deques 的一些可能应用

像队列一样，deque 的一个真正用途是在模拟中。一个可能的模拟是铁路调车场，其中有多条轨道供轨道车停放，但只有调车场末端的轨道允许轨道车进出。您可以使用队列来模拟这种情况，因为队列只允许从前面或后面输入和输出，模拟铁路站场的侧轨。

双队列的另一个用途是跟踪网页浏览历史。被访问的新网站被放在队列的前面，一段时间后，旧的被访问网站被从队列的后面移除。

deque 不是一个经常使用的容器，但是它确实有它的用途，应该考虑用于可以从容器的前端或后端添加和删除数据的应用程序。

感谢您的阅读，请发送电子邮件提出意见和建议。