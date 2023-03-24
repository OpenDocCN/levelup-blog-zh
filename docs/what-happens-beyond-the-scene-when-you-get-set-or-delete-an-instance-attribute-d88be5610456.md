# 当您获取、设置或删除实例属性时，场景之外会发生什么:Python OOP 完整教程—第 17 部分

> 原文：<https://levelup.gitconnected.com/what-happens-beyond-the-scene-when-you-get-set-or-delete-an-instance-attribute-d88be5610456>

## 了解 Python OOP 中的特殊方法是什么属性，以及如何覆盖它们。

![](img/b7d06d4474caf738cd63c0b35bb4d66b.png)

照片由[詹姆斯](https://unsplash.com/@fragilejames)在 [Unsplash](https://unsplash.com/s/photos/collections) 上拍摄

开始之前，让我告诉你:

*   这篇文章是 Python 面向对象编程完整课程的一部分，你可以在这里找到它。
*   所有资源都可以在下面的“资源”部分找到。
*   这篇文章也可以作为 YouTube 视频[第一部分](https://youtu.be/NPZ3HVYbM78)、[第二部分](https://youtu.be/ALJmoDY8yos)、[第三部分](https://youtu.be/fN8saK5La28)获得。

[https://youtu.be/NPZ3HVYbM78](https://youtu.be/NPZ3HVYbM78)

## 介绍

假设您有一个`DummyClass`类，其中有两个实例属性`x`和`y`。之后，您从这个类中定义了一个对象，并决定如下所示打印对象属性`z`:

```
obj  = DummyClass()print(obj.z)
```

您将得到一个错误，因为`z`属性在这个类中不可用

为什么会出现这个错误？当你想设置或获取一个属性时，场景之外会发生什么呢？让我们一起来看看…

**目录**

1.  [什么是属性特殊方法？](#5fb8)
2.  [可用属性特殊方法](#3328)
3.  [如何覆盖属性特殊方法？](#969c)
4.  [用例:一个懒惰的数据库类](http://e550)

## 1.什么是属性特殊方法？

当您想要**获取、设置或删除**一个对象属性值时，就会调用这些方法。

因此，你可以理解，当你想要**获取、设置或删除**一个对象的属性值时，可以覆盖这些方法来添加预处理逻辑。

现在让我们一起来看看这一类别下的可用方法。

## 2.可用属性特殊方法

我们有三种方法属于这一类:

*   `__getattr__()`:这个方法定义了当函数`getattr()` 被你的类中的一个对象调用时，你的类中没有定义的属性**的行为。此方法必须返回所需属性的值。**
*   `__setattr__()`:这个方法定义了当函数`setattr()` 被你的类的一个对象调用时，或者当你使用`dot`操作符设置一个属性值时的行为。
*   `__delattr__()`:这个方法定义了用你的类的对象调用函数`delattr()`时的行为。

为了更好地理解这些方法，让我们看一些代码示例。

![](img/4fa9197b9824fb737033f2929f16167d.png)

由[玛丽亚·特内娃](https://unsplash.com/@miteneva)在 [Unsplash](https://unsplash.com/collections/6dyV1yMNr3w/collections) 上拍摄的照片

## 3.如何重写属性特殊方法

*   首先，我们来谈谈`__getattr__()`法

假设您有一个带有一个实例属性`first_name`的`Student`类，并且您有一个来自这个类的对象。

```
class Student: def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')
```

让我们试着从`student` 对象中获取`first_name`属性。

**第一种方式:**使用`**.**` 运算符。

```
class Student:def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')print(student.first_name)
```

输出:

```
John
```

**另一种方式:**使用`getattr()`方法，在这里你应该传递对象名和你想要得到其值的属性名作为字符串。

```
class Student:def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')print(getattr(student, 'first_name'))
```

输出:

```
John
```

通过这两种方式，您可以访问相同的属性。

现在，让我们看看如果我们试图访问一个在我们的类中不可用的属性会发生什么。

```
class Student:def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')print(student.last_name)
```

输出:

```
**AttributeError**                            Traceback (most recent call last)
**<ipython-input-5-a12c62f9fcff>** in <module>
**----> 1** print**(**student**.**last_name**)**

**AttributeError**: 'Student' object has no attribute 'last_name'
```

如您所见，您得到了`AttributeError`,因为该对象没有`last_name` 属性。

为了解决这个错误，让我们覆盖方法 `__getattr__()`。

参考下面代码中的最后一个方法定义，其中 `__getattr__()`接受两个位置参数`self`和您想要访问的属性的`name`。

1-使用`.` 操作器进入`first_name`

输出:

```
John
```

2-通过调用 `__getattr__()`访问`first_name`

输出:

```
John
```

3-访问我们的类中没有的`last_name`实例属性

输出:

```
Hi I am getattr
None
```

如您所见，`ArtibuteError`已经消失，而`__getattr__()`方法已经被调用。

> 因此，如果实例属性直接可用，将返回该值，否则如果不可用，将调用`__getattr__()` 方法以防其被覆盖。

最后，当`__getattr()__`方法被覆盖时，您指导解释器在所需属性不可用的情况下做什么。

*   其次，我们来谈谈`__setattr__()` 法

回到前面的`Student`类，尝试设置`first_name`属性的值。

```
class Student: def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')
```

**第一种方式:**使用`**.**` 运算符。**不推荐**方式…

```
class Student: def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')student.first_name = 'Doe'   #Not Recommended way
print(student.first_name)
```

输出:

```
Doe
```

如您所见，您获得了新的值。

> **注意:**不建议在类范围之外更改或更新任何实例属性值。换句话说，定义实例方法来做到这一点。

**另一种方法:**使用预定义的`setattr()`方法。其中应该传递对象名、字符串形式的属性名和新的属性值。

```
class Student: def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')setattr(student, 'first_name', 'Doe 2')
print(student.first_name)
```

输出:

```
Doe 2
```

在前面的例子中，我们使用了预定义的`__setattr__()`方法。现在让我们尝试覆盖它，但是首先我们添加一个新实例的属性，即`age`。

```
class Student: def __init__(self, first_name, age):
        self.first_name = first_name
        self .age = agestudent = Student('John', 26)
```

现在，使用`setattr()`函数更改`age`属性的值。

输出:

```
-25
```

如您所见，它工作了，您获得了新的值，但是这里有一个问题:`age`不应该是负的。

为了解决这个问题，您必须在设置新值之前添加一个验证过程，以不允许`age`为负。您可以通过覆盖`__setattr__()`方法来实现。参考下面代码中的最后一个方法定义…

输出:

```
**Exception**                                 Traceback (most recent call last)
**<ipython-input-18-d28bdd4db105>** in <module>
      1 student **=** Student**('John',** **25)**
      2 
**----> 3** setattr**(**student**,** **'age',** **-26)**
      4 print**(**student**.**age**)**

**<ipython-input-17-e69d25349695>** in __setattr__**(self, name, value)**
      7     **def** __setattr__**(**self**,** name**,** value**):**
      8         **if** name **==** **'age'** **and** value **<** **0:**
**----> 9             raise** Exception**('Age cannot be negative')**
     10         **#otherwise change the value**
     11         self**.**__dict__**[**name**]** **=** value

**Exception**: Age cannot be negative
```

如您所见，您得到了一个异常。

尝试一个正数。

```
setattr(student, 'age', 26)
print(student.age)
```

输出:

```
26
```

您可以毫无问题地获得新值。

*   最后，我们来谈谈`__delattr__()`法

回到前面的`Student`类，让我们从一个对象中删除`first_name`属性。

```
class Student: def __init__(self, first_name):
        self.first_name = first_namestudent = Student('John')
```

**第一种方式:**使用带有`del`关键字的`.`操作符

**注**:打印删除前后的`first_name`属性值。

输出:

```
John**AttributeError**                            Traceback (most recent call last)
**<ipython-input-23-8371fd6541e8>** in <module>
**----> 1** print**(**student**.**first_name**)**
      2 **del** student**.**first_name
      3 print**(**student**.**first_name**)**

**AttributeError**: 'Student' object has no attribute 'first_name'
```

正如您所看到的，首先您得到了`first_name`值，但是在您删除它之后，您得到了一个`AttributeError`。

**另一种方法:**使用`delattr()`方法，这里你应该传递对象名和要删除的属性名。

输出:

```
John**NameError**                                 Traceback (most recent call last)
**~\AppData\Local\Temp/ipykernel_11324/695046985.py** in <module>
**----> 1** student **=** Student**('John')**
      2 print**(**student**.**first_name**)**
      3 
      4 delattr**(**student**,** **'first_name')**
      5 print**(**student**.**first_name**)**

**NameError**: name 'Student' is not defined
```

你得到了同样的输出。

现在，假设当你想删除属性时，你只是想把它的值设置为`None`，而不是删除它。

为了实现这一点，让我们重写`__delattr()__`方法。然后使用`del`关键字删除`first_name`属性。

输出:

```
John
None
```

使用 `__delattr__()`方法删除`first_name`属性。

输出:

```
John
None
```

如您所见，`AttributeError` 已经消失，在两种情况下，您都使用`del`关键字或通过调用`delattr()`方法得到了`None`。

现在，让我们看一个真实的用例。

## 4.用例:惰性数据库类

这种情况下的一个实际用例是创建一个惰性数据库类。

“懒惰”在这里意味着当你第一次想访问数据库中的一个表中的一行时，你必须连接到这个数据库。然而，如果您下次想要访问同一行，您将获得一个缓存版本，并且不需要再次连接到数据库。

现在，让我们来定义这个类。关于它的几点说明:

*   这个类的构造函数接受我们想要连接到的表名`table_name.`
*   `__getattr__()`方法将被覆盖，它将接受我们想要从这个表中获取的`row_id` 。
*   在`__getattr__()`方法中，我们将使用`setattr()`方法来定义与被传递的`row_id`相关的值。
*   对于连接到数据库的过程，我们不打算连接到一个真实的数据库，而是打印一些语句来表示连接过程。

在这个类中，您可以看到，当您第一次想要访问数据库中的记录时，这个对象将连接到数据库以获取值。否则，将返回对象内部的可用值。

现在，假设我们有一个`students` 表，让我们从`LazyDB` 类定义一个对象。

为了在连接到数据库之前发现状态，我们可以打印`object_name.__dict__`

```
stds = LazyDB('students')
#befor connecting to the database
print('Before') 
print(stds.__dict__)
```

输出:

```
Before
{'table_name': 'students'}
```

如您所见，在连接到数据库之前，对象只有`table_name`属性。

现在让我们说，我们想连接到数据库，以获得有`**id = 1**`的行

```
print(stds.row_1)
print()
```

输出:

```
connecting to DB
I am getattr
6095d880-0c3a-4523-941e-e60bcca82e76
```

为什么我们会得到这样的结果？

当我们试图获取`row_1`的值时，已经调用了`__getattr__()`，因为这个值在我们的对象中不可用。

现在，如果我们试图重新访问同一个`row_1`值，就不需要再次重新连接数据库。为了确保`row_1` 成为我们的对象属性之一，让我们重新打印`object_name. __dict__`，看看结果。

```
print('After') 
print(stds.__dict__)
```

输出:

```
After
{'table_name': 'students', 'row_1': '6095d880-0c3a-4523-941e-e60bcca82e76'}
```

很明显，我们的对象有了一个新属性`row_1`。

现在，让我们总结一下我们在这篇文章中学到了什么。

![](img/ccb7fc677cd37fec2b5d19924bb08e77.png)

照片由[安 H](https://www.pexels.com/@ann-h-45017/) 在[像素上](https://www.pexels.com/)拍摄

*   **属性特殊方法:**是当你想要**获取、设置或删除**一个属性值时被调用的方法。
*   在这个类别下，我们有 `__getattr__()`、`__setattr__()`和`__delattr__()`。

***附言*** *:万分感谢您花时间阅读我的故事。在你离开之前，让我快速地提两点*

*   *首先，要想直接在你的收件箱里看到我的帖子，请在这里订阅*[](https://medium.com/@samersallam92/subscribe)**，你可以在这里关注我*[](https://medium.com/@samersallam92)**。***
*   ***第二，作家在媒介上制造了数以千计的****$****。为了无限制地访问媒体故事并开始赚钱，* [***现在就注册成为媒体会员***](https://medium.com/@samersallam92/membership)**其中* *每月只需花费 5 美元。通过此链接* *报名* [***，可以直接支持我，不需要你额外付费。***](https://medium.com/@samersallam92/membership)***

**要回到上一篇文章，您可以使用以下链接:**

**第 16 部分:[如何让你的类对象转换成其他数据类型](https://medium.com/gitconnected/how-to-make-your-class-objects-convertible-to-other-data-types-python-oop-complete-course-part-e0c71967c9ef)**

**要阅读下一篇文章，您可以使用以下链接:**

**第 18 部分:[如何创建定制的序列类](https://blog.devgenius.io/how-to-create-a-customized-sequence-class-python-oop-complete-course-part-18-8c47ac58b814)**

## **资源:**

*   **GitHub [**此处**](https://github.com/samersallam/The-Complete-Course-in-Object-Oriented-Programming-in-Python/tree/main/Attributes%20Special%20Methods) **。****

**你可能也会喜欢这个**

**![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

萨梅尔·萨拉姆** 

## **Python 中数据科学的完整课程**

**[View list](https://medium.com/@samersallam92/list/the-complete-course-in-data-science-in-python-aa3fac216e89?source=post_page-----d88be5610456--------------------------------)****9 stories****![A dummy image for better reading and navigation.](img/f40bcfacd37678dd6539d23b81787441.png)****![A dummy image for better reading and navigation.](img/346ad39e1625d7579740ee89b281b440.png)****![A dummy image for better reading and navigation.](img/1d874a781e55f43d4c8166e36aa27425.png)****![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

萨梅尔·萨拉姆** 

## **Python 初学者到专家的完整课程**

**[View list](https://medium.com/@samersallam92/list/python-complete-beginner-to-expert-course-32d3a941c05e?source=post_page-----d88be5610456--------------------------------)****21 stories****![A white desk with a laptop, its screen shows some lines of code, phone, cup of coffee, a screen and a white lamp on it.](img/a990b710cc6c33085894453e6e0b10b9.png)****![A dummy image for better reading and navigation.](img/6423d57aa5d56c05169d2738a1592214.png)****![A dummy image for better reading and navigation.](img/f8ba979ab77abc24ff23c97dc0499b45.png)**