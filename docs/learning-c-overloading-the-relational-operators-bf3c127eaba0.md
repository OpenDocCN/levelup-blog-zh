# 学习 C++:重载关系运算符

> 原文：<https://levelup.gitconnected.com/learning-c-overloading-the-relational-operators-bf3c127eaba0>

![](img/bc9f8cb39a894affcb1dfd5841149596.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[科学高清](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)拍摄的照片

在本文中，我将讨论如何重载 C++关系运算符。我以前介绍过重载输入和输出操作符、赋值操作符以及递增和递减操作符。

# 示范课

出于讨论的目的，我将创建并使用一个`Item`类。下面是这个类的声明和定义，出于节省空间的目的，这个类很简单(没有 getters 和 setters，也没有重载的构造函数，还有一些我没有提到的特性):

```
class Item {
private:
  string itemName;
  int numStock;
public:
  Item(string name, int stock);
  void display();
};void Item::display() {
  cout << "Item name: " << itemName << ": " << numStock;
}Item::Item(string name, int number) {
  itemName = name;
  numStock = number;
}
```

# 重载==运算符

关系运算符很容易重载。您将它们声明为友元函数，然后在函数体中执行重载的比较。下面是重载`==`操作符的声明和定义:

```
friend bool operator ==(const Item &, const Item &);bool operator == (const Item &lhs, const Item &rhs) {
  return (lhs.numStock == rhs.numStock);
}
```

这里有一个测试程序可以使用:

```
int main()
{
  Item store1Widget("Widget", 3);
  Item store2Widget("Widget", 3);
  if (store1Widget == store2Widget) {
    cout << "Both stores have the same number of widgets."
         << endl;
  }
  else {
    cout << "The stores don't have the same number of widgets."
         << endl;
  }
  return 0;
};
```

以下是该程序的输出:

```
Both stores have the same number of widgets.
```

# 重载剩余的关系运算符

现在我们已经看到重载`==`操作符是多么容易，我们可以快速重载剩余的关系操作符(`!=`、`>`、`<`、`>=`和`<=`)。以下是声明和定义:

```
friend bool operator !=(const Item &, const Item &);friend bool operator >(const Item &, const Item &);friend bool operator <(const Item &, const Item &);friend bool operator >=(const Item &, const Item &);friend bool operator <=(const Item &, const Item &);bool operator <=(const Item &lhs, const Item &rhs) {
  return (lhs.numStock <= rhs.numStock);
}bool operator >=(const Item &lhs, const Item &rhs) {
  return (lhs.numStock >= rhs.numStock;
}bool operator <(const Item &lhs, const Item &rhs) {
  return (lhs.numStock < rhs.numStock);
}bool operator >(const Item &lhs, const Item &rhs) {
  return (lhs.numStock > rhs.numStock);
}bool operator !=(const Item &lhs, const Item &rhs) {
  return (lhs.numStock != rhs.numStock);
}
```

下面是一个测试程序，用于测试每个重载运算符:

```
int main()
{
  Item store1Widget("Widget", 4);
  Item store2Widget("Widget", 3);
  // excuse the awkward formatting
  (store1Widget != store2Widget) ?
    cout << "Not the same number of items." << endl
    : cout << "The same number of items." << endl; (store1Widget > store2Widget) ? cout << "Store 1 has more. "
    << endl : cout << "Store 1 doesn't have more. " << endl;
    (store1Widget >= store2Widget) ? cout << "Store 1 has more. "
    << endl : cout << "Store 1 doesn't have more. " << endl; (store2Widget < store1Widget) ? cout << "Store 2 has less."
    << endl : cout << "Store 2 doesn't have less." << endl;
    (store2Widget <= store1Widget) ? cout << "Store 2 has less."
    << endl : cout << "Store 2 doesn't have less." << endl; return 0;
}
```

下面是这个测试程序的输出:

```
Not the same number of items.
Store 1 has more.
Store 1 has more.
Store 2 has less.
Store 2 has less.
```

# 过载就过载

我还没有介绍完在 C++中可以重载的操作符。在我的下一篇文章中，我将介绍算术运算符的重载。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。