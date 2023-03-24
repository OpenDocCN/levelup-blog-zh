# Python 面向对象编程快速指南

> 原文：<https://levelup.gitconnected.com/python-object-oriented-programming-c74cecfc00f6>

## Python 中的面向对象编程|第 1 部分

![](img/ca84790946e8f6bc2f07f76512624839.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 什么是 OOP(面向对象编程)？

如果你试着在网上或者任何教科书上找，都没有关于 OOP 的具体定义。一般来说，面向对象编程被概念化为 4 个支柱概念的组合。这些概念是:

1.  抽象(隐藏某些东西)
2.  封装(封装或连接许多东西)
3.  遗产(继承某物)
4.  多态性(用于多种目的)

随着博客的进展，我会详细解释这些概念。但是在 OOP 中要知道的主要事情是**类&对象**。那么，这些类和对象是什么？

# Python 中的类和对象

简单来说，类只是一个实际事物(对象)的骨架或结构。例如，建筑物的草图/轮廓是一个类，而实际的建筑物是一个对象。

创建类的语法:

```
class ClassName:
    <statement-1>
    .
    .
    .
    <statement-N>
```

现在，如果我们将这个类对象概念应用于所有人，那么我们可以将 **Person** 作为一个具有许多属性和功能的类。

```
class Person:
  age = 30
  height = 180
  weight = 65 def say_hello(self):
	print('Hello Person')
```

这里，我们创建了一个 Person 类，它具有**年龄、身高**和**体重**属性以及 **say_hello()** 方法或功能。我们已经创建了这个人类，现在呢？

要使用这个 person 类，我们需要从这个 Person 类创建一个对象。要创建对象，我们使用以下语法:

```
object_name = ClassName()
```

要创建 Person 类的对象:

```
person = Person()
print(person)""" OUTPUT:<__main__.Person object at 0x000001C47ED317B0>"""
```

当我们尝试打印`person`时，可以看到它是内存中位于 **0x000001C47ED317B0** 的 Person 类的一个对象。要使用类的属性和方法，我们可以使用以下语法:

```
class Person:
  age = 30
  height = 180
  weight = 65 def say_hello(self):
	print('Hello Person')person = Person()
print("AGE:", person.age)
print("HEIGHT:", person.height)
print("WEIGHt:", person.weight)person.say_hello()""" OUTPUT: AGE: 30
HEIGHT: 180
WEIGHt: 65
Hello Person"""
```

这个 Person 类看起来非常静态，并且具有固定的功能，那么你可能会问，为什么我称它为 schema/outline？

我特意将 Person 类保持简单，以便向您展示类和对象的基本用法。所以如果我向你展示类和对象的正确而简单的用法，那么:

```
class Person: def __init__(self, name, age, height, weight):
	self.name  = name 
	self.age = age
	self.height = height
	self.weight = weight def show_data(self):
	print('Details: ')
	print('AGE:', self.age)
	print('HEIGHT:', self.height, "cm")
	print('WEIGHt:', self.weight, "kg")person1 = Person('John Doe', 33, 180, 75)
person1.show_data()print("-"*15)person2 = Person('Jane Doe', 31, 160, 65)
person2.show_data()""" OUTPUT:
Details: 
NAME: John Doe
AGE: 33
HEIGHT: 180 cm
WEIGHt: 75 kg
---------------
Details: 
NAME: Jane Doe
AGE: 31
HEIGHT: 160 cm
WEIGHt: 65 kg
"""
```

正如您在上面的代码示例中看到的，我能够为姓名、年龄、身高和体重传递不同的值，并从 Person 类创建了两个对象。我可以根据需要传递相同或不同的值，并创建 N 个对象，因为它们将存储在不同的位置。

```
person1 = Person('John Doe', 33, 180, 75)
print(person1)
# OUTPUT: <__main__.Person object at 0x000001E6B1C93FA0>person2 = Person('Jane Doe', 31, 160, 65)
print(person2)
# OUTPUT: <__main__.Person object at 0x000001E6B1C93EB0>person3 = Person('Jane Doe', 31, 160, 65)
print(person3)
# OUTPUT: <__main__.Person object at 0x000001E6B1C93E50>
```

在现实生活中，当我们必须创建多个用户(注册功能)时，我们不会为每个用户编写代码。我们使用类并基于接收到的细节创建对象，就像我们处理`person1`和`person2`一样。

现在，您可能已经注意到了上面代码中的`self`关键字和`__init__()`方法。这些是什么？

# Python 中的 init()方法是什么？

如果您来自其他语言，如 Java、C#或 JavaScript，您可能对构造函数的概念很熟悉。 **init()** 方法是 Python 中一个类的构造函数。对于非技术人员来说，可以把构造函数看作是告诉编译器/解释器启动一个操作或者为我们计划创建的对象分配内存的一种方式。但是在 Python 中使用构造函数并不是必须的。如果我们不提供`__init__()`方法，解释器会自己创建。

通常，我们使用 **init()** 方法来初始化一些属性并用于继承目的。正如我们前面看到的，这是我们初始化变量的方式:

```
def __init__(self, name, age, height, weight):
  self.name  = name 
  self.age = age
  self.height = height
  self.weight = weight
```

***注:*** 我们将在后面讨论如何使用`__init__()`方法进行继承。

现在，你可能会问，但是萨赫勒，到底什么是`self`？

# Python 中的‘self’关键字是什么？

**self** 关键字代表一个类的实例。(只是为了简单理解，把 instance 看成是解释器生成的对象。对象和实例并不相同，但为了简单的理解，这里将它们视为相同。)

使用这个 self 关键字，我们可以访问整个类的所有属性和方法。例如，在下面的例子中，我们使用了`self`关键字来访问**姓名**、**年龄**、**身高**和**体重**属性。

```
class Person: def __init__(self, name, age, height, weight):
	self.name  = name 
	self.age = age
	self.height = height
	self.weight = weight def show_data(self):
	print('Details: ')
	print('NAME:', self.name)
	print('AGE:', self.age)
	print('HEIGHT:', self.height, "cm")
	print('WEIGHt:', self.weight, "kg")
```

我们也可以使用`self`关键字访问方法。

```
class Person: def __init__(self, name, age, height, weight):
	self.name  = name 
	self.age = age
	self.height = height
	self.weight = weight def show_data(self):
	print('Details: ')
	print('NAME:', self.name)
	print('AGE:', self.age)
	print('HEIGHT:', self.height, "cm")
	print('WEIGHt:', self.weight, "kg") def self_demo(self):
	self.show_data()person1 = Person('John Doe', 33, 180, 75)
person1.self_demo()""" OUTPUT:
Details: 
NAME: John Doe
AGE: 33
HEIGHT: 180 cm
WEIGHt: 75 kg
"""
```

没有必要使用`self`关键字。您可以使用任何名称，但是条件是它应该是方法的第一个参数。(对于 ****init** **)也是如此。)

```
class Person: def __init__(random_replacement, name):
	random_replacement.name = name
	pass def show_data(random_replacement_1):
	print('Just a Random Thing!')
	print('Name:', random_replacement_1.name)person1 = Person('John Doe')
person1.show_data()""" OUTPUT:
Just a Random Thing!
Name: John Doe
"""
```

您可能已经猜到了，在整个类中使用相同的名称也不是强制性的。唯一的规则是`it should be the first argument!`但是，通常更喜欢`self`关键字，因为它几乎已经成为一个标准。如果我们想要传递任何参数，我们必须在关键字`self`之后传递它。正如我们在`__init__()`方法中所做的那样。

您也可以随时使用`self`关键字添加任何属性。

```
class Person: def __init__(self, name):
	self.name = name def add_age(self, age):
	self.age = age def show_data(self):
	print('Name:', self.name)
	print('Age:', self.age)person1 = Person('John Doe')
person1.add_age(31)
person1.show_data()""" OUTPUT:
Name: John Doe
Age: 31
"""
```

这里，我们已经将**年龄**添加到了类属性中。我不建议你这样做，只是为了演示的目的，这是可能的。将关键字`self`视为字典，将所有属性和方法视为该字典的`key`。

在上面的代码示例中，如果我们先调用`show_data()`方法。它将抛出一个错误，因为当时我们没有任何名为 age 的属性。

> *这种将属性和方法结合在一个保护伞下的过程叫做* ***封装*** *。*

那么，我们如何扩展类的用法和功能呢？我们可以使用`Inheritance`和`Polymorphism`来实现。

# Python 中的继承

顾名思义，继承就是继承某些东西的能力。在这种情况下，我们将继承属性和方法。例如，前面我们创建了一个名为 Person 的类，它太普通了。但是使用这个通用类，我们可以创建另一个特定于我们需求的类。

我们想要创建一个继承一切的**雇员**类，或者从**人员**类继承。那么，我们该怎么做呢？

**Python 中的继承语法:**

```
# Base Class/Parent Class
class BaseClass:
    <statement-1>
    .
    <statement-N># Derived Class/Child Class
class DerivedClass(BaseClass):
    <statement-1>
    .
    <statement-N>
```

为了从 **Person** 类创建 **Employee** 类，我们可以编写以下代码:

```
class Person:
  def __init__(self, name, age, height, weight):
	self.name  = name
	self.age = age
	self.height = height
	self.weight = weight def show_basic_data(self):
	print('Details: ')
	print('NAME:', self.name)
	print('AGE:', self.age)
	print('HEIGHT:', self.height, "cm")
	print('WEIGHt:', self.weight, "kg")class Employee(Person):
  def __init__(self, employeeId, department, salary):
	super().__init__(name, age, height, weight) 
	self.employeeId = employeeId
	self.department = department
	self.salary = salary def show_data(self):
	self.show_basic_data()
	print('Employee Id:', self.employeeId)
	print('Department:', self.department)
	print('salary: $', self.salary)employee = Employee('John Doe', 33, 180, 75, 1 ,'IT', 100000)
employee.show_data()""" OUTPUT:
Details: 
NAME: John Doe
AGE: 33
HEIGHT: 180 cm
WEIGHt: 75 kg
Employee Id: 1
Department: IT
salary: $ 100000
"""
```

你可能已经注意到员工班的`super()`。你可能会问这个`super()`是什么？**超级**关键字指的是父类。我们使用`super()`方法来访问父类的属性和方法。这就是我们在 Employee 类的`__init__()`方法中所做的。我们向父类传递了一些数据。

一旦我们在子类的`__init__()`方法中调用了 **super()** ，我们就可以在我们的子类中使用父类的所有属性和方法。这就是我们在 Employee 类的 **show_data()** 方法中所做的。我们也可以使用`super().show_basic_data()`。

# 结论

终于！我们已经到了这一节的末尾😁。

就这样。感谢您的阅读。

如果你需要任何帮助或者想讨论什么，请告诉我。在推特或 LinkedIn 上联系我。请务必在下面的评论中留下你的想法、问题或担忧。我很想看看他们。

> 你可以在[维基百科](https://en.wikipedia.org/wiki/Object-oriented_programming)或[极客论坛](https://www.geeksforgeeks.org/object-oriented-programming-oops-concept-in-java/)上了解更多关于 OOPs 的信息。

> *想了解更多信息？*
> 
> *注册我的* [*时事通讯*](https://bit.ly/3Menk8Q) *，把最好的文章放进你的收件箱。*

直到下一次👋

> *探索我在 Python 101 系列中的其他博客*

[](https://betterprogramming.pub/organize-files-and-folders-using-python-660246ef9310) [## 如何使用 Python 组织文件和文件夹

### 3 件主要的事情将帮助我们决定如何组织我们的文件

better 编程. pub](https://betterprogramming.pub/organize-files-and-folders-using-python-660246ef9310) [](/delete-files-and-directories-using-python-5f1741710a5b) [## 使用 Python 删除文件和目录

### 迷你项目| Python 101 系列

levelup.gitconnected.com](/delete-files-and-directories-using-python-5f1741710a5b) [](https://python.plainenglish.io/guide-to-extract-tweets-using-tweepy-d0d25893b3c2) [## 使用 Tweepy 提取推文的综合指南

### 使用 Python 中的 Tweepy 库获取数据的分步指南

python .平原英语. io](https://python.plainenglish.io/guide-to-extract-tweets-using-tweepy-d0d25893b3c2)