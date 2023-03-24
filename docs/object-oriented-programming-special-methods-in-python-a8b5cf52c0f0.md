# 面向对象编程:Python 中的特殊方法

> 原文：<https://levelup.gitconnected.com/object-oriented-programming-special-methods-in-python-a8b5cf52c0f0>

## 了解 OOP 中使用的特殊方法的指南。

![](img/6340c147b3324fb5cd657a6edd8b7c11.png)

[弗拉基米尔·库季诺夫](https://unsplash.com/@madbyte?utm_source=medium&utm_medium=referral)在[号航天飞机](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄的照片

特殊的方法允许我们使用 Python 中的一些内置操作来创建我们自己的自定义对象。

# 需要面向对象的特殊方法

假设我们有一个列表，我们想检查列表的长度并打印该列表，我们将执行以下操作:

```
>>> list1 **=** [11,22,33,44]>>> len(list1)
4>>> print(list1)
[11, 22, 33, 44]
```

现在假设我们想检查我们创建的对象的长度。

```
**>>> class** Test():
        **pass**>>> my_test **=** Test()>>> len(my_test)
**---------------------------------------------------------------------------**
**TypeError**                                 Traceback (most recent call last)
**<ipython-input-5-63acc1b4ea06>** in <module>
**----> 1** len**(**my_test**)****TypeError**: object of type 'Test' has no len()
```

我们得到一个“TypeError”，表示这样一个物体没有透镜()。

```
>>> print(my_test)
<__main__.Test object at 0x00000296237884C8>
```

请注意，当我们打印列表时，它会返回列表。但是当我们打印类“my_test”的实例时，我们得到了对象在内存中的位置。

因此，我们需要特殊的方法来执行一些内置的功能，如长度和打印。

# 特殊方法

它们也被称为魔术方法或 dunder 方法，因为它们使用双下划线。

“init”是第一个自动调用的特殊方法。

接下来是“str”方法，它返回字符串表示形式并帮助打印。这是因为当我们调用 print()时，它会要求我们试图打印的对象的字符串表示。所以我们需要一种方法来获得字符串表示。

接下来是“len”方法来获得物体的长度。

```
**>>> class** Novel():
        **def** __init__(self, title, author, pages):
            self.title **=** title
            self.author **=** author
            self.pages **=** pages **def** __str__(self):
            **return** ("The novel {} is written by {}".format(self.title, self.author)) **def** __len__(self):
            **return** (self.pages)>>> my_novel **=** Novel("Habits", "Chandra", 200)>>> print(my_novel)
The novel Habits is written by Chandra>>> len(my_novel)
200
```

现在如果你想删除你创建的对象。

```
**>>> del** my_novel>>> my_novel
**---------------------------------------------------------------------------**
**NameError**                                 Traceback (most recent call last)
**<ipython-input-17-4119ba51f8ec>** in <module>
**----> 1** my_novel**NameError**: name 'my_novel' is not defined
```

错误是因为对象“我的小说”已被删除。

有时，您希望在删除对象时发生某些事情。所以我们也有一个方法“del”。

```
**>>> class** Novel():
        **def** __init__(self, title, author, pages):
            self.title **=** title
            self.author **=** author
            self.pages **=** pages **def** __str__(self):
            **return** ("The novel {} is written by {}".format(self.title, self.author)) **def** __len__(self):
            **return** (self.pages) **def** __del__(self):
            print("A novel object has been deleted")>>> my_novel **=** Novel("Habits", "Chandra", 200)**>>> del** my_novel
A novel object has been deleted
```

在此查阅笔记本[。](https://github.com/jayashree8/Python_guide/blob/master/Python%20object%20oriented%20programming/Object-oriented%20programming%E2%80%8A-%E2%80%8Aspecial%20methods%20in%C2%A0Python.ipynb)

## 学习 Python 需要参考的入门书籍:

[](https://amzn.to/3yDY4To) [## Python 速成课程，第 2 版:基于项目的编程实践入门

### 世界上最畅销的 Python 书的第二版。一个快节奏的，没有废话的 Python 编程指南……](https://amzn.to/3yDY4To) [](https://amzn.to/3vtvQZv) [## 艰难地学习蟒蛇:一个非常简单的介绍，让你了解……

### 您将学习 Python！泽德·肖完善了世界上最好的学习 Python 的系统。跟着它走，你会…](https://amzn.to/3vtvQZv) [](https://amzn.to/3urluYI) [## 想想 Python，2e:如何像计算机科学家一样思考

### 想想 Python，2e:如何像计算机科学家一样思考:亚马逊。in: Downey，Allen B: Books](https://amzn.to/3urluYI) 

## 学习 Python 需要参考的高级书籍:

[](https://amzn.to/3fMzVBn) [## 编程 Python:强大的面向对象编程

### 如果您已经掌握了 Python 的基本原理，那么您就可以开始使用它来完成真正的工作了。编程 Python 将…](https://amzn.to/3fMzVBn) [](https://amzn.to/34oFFMl) [## 高级 Python 编程:使用…构建高性能、并发和多线程应用程序

### 关键特性使用 Dask 和 PySpark Master 技能在集群上设置和运行分布式算法，以准确地…](https://amzn.to/34oFFMl) 

> *联系我:* [*LinkedIn*](https://www.linkedin.com/in/jayashree-domala8/)
> 
> *查看我的其他作品:* [*GitHub*](https://github.com/jayashree8)