# 代码味道 142 —构造函数中的查询

> 原文：<https://levelup.gitconnected.com/code-smell-142-queries-in-constructors-c61e359567b1>

## 在域对象中访问数据库是一种代码味道。在构造函数中这样做是一个双重的味道

![](img/bf5e25c4ff5c5713193a589e4c2ec9d6.png)

> *TL；DR:构造函数应该构造(并且可能初始化)对象。*

# 问题

*   [联轴器](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04)
*   副作用

# 解决方法

1.  将基本的业务逻辑从偶然的持久性中分离出来
2.  在持久性类中，在构造函数/析构函数之外的函数中运行查询

# 语境

在遗留代码中，数据库没有正确地与业务对象分离。

构造函数不应该有副作用。

根据单一责任原则，他们应该只构建*有效的*对象

# 示例代码

## 错误的

```
public class Person {
  int childrenCount;   public Person(int id) {
    childrenCount = database.sqlCall("SELECT COUNT(CHILDREN) FROM PERSON WHERE ID = " . id); 
  }
}
```

## 对吧

```
public class Person {
  int childrenCount;   // Create a class constructor for the Main class
  public Person(int id, int childrenCount) {
    childrenCount = childrenCount; 
    // We can assign the number in the constructor
    // Accidental Database is decoupled
    // We can test the object
  }
}
```

# 侦查

[X]半自动

我们的 linters 可以发现构造函数上的 SQL 模式并警告我们。

# 标签

*   连接

# 结论

在设计健壮的软件时，关注点的分离是关键，耦合是我们的主要敌人。

# 更多信息

*   [耦合:唯一的软件问题](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04)

# 信用

照片由[卡勒姆希尔](https://unsplash.com/@inkyhills)在 [Unsplash](https://unsplash.com/s/photos/no) 上拍摄

> 我的信念仍然是，如果你得到了正确的数据结构和它们的不变量，大部分代码会自己写出来。

彼得·德乌斯奇

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)