# 用 Python 类隐藏和寻找

> 原文：<https://levelup.gitconnected.com/hide-seek-with-python-classes-6a459872b5cc>

![](img/1211d98759262f4ad2420761e1a25d88.png)

照片由 [Dagmar Klauzová](https://unsplash.com/@dagakla?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

当您创建 Python 类时，您正在创建一个带有其属性和相关方法的实体。

这被称为**封装**，面向对象编程的支柱。

让我们看一个例子:

让我们使用这个类定义两个对象并打印它们的属性。

像这样定义类会导致一个大问题。

默认情况下，它的所有属性和方法都是公开的。

如果一个聪明的学生知道如何用 Python 编码，他可以很容易地看到并**改变**他们的`name`和`grade`！

## 隐藏属性

为了使属性**成为私有的**(其他人无法访问)，可以定义前缀为双下划线(`__`)的属性名。

让我们试着查一下这些学生的成绩。

![](img/75cb47a2e72ec03ae9737e82d5dc7ac7.png)

托马斯·列斐伏尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## Getter 和 Setter 方法

要访问这些属性，可以为它们定义 getter 和 setter 方法。

# 一个重要的警告

不幸的是，私有属性仍然可以被聪明的学生访问和更改(T21)。

这是通过使用`_<class_name>__<property_name>`语法来完成的。

![](img/b577441408250573517cc815c0b7c6fa.png)

[维多利亚·希斯](https://unsplash.com/@vheath?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

感谢您阅读这篇文章！

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)