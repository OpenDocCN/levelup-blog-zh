# Ruby 元编程简介

> 原文：<https://levelup.gitconnected.com/introduction-of-meta-programming-in-ruby-9f02426dcf8c>

![](img/4502f1c780fbdddfad60d641326fff72.png)

丹尼尔·弗兰奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> D 尽管元宇宙对元编程越来越感兴趣，但今天许多新开发人员仍然不知道元编程。在本文中，我们将探讨什么是元编程，为什么它是有益的，以及如何将它结合到 Ruby 语言中以强调该语言的独特性。

**简介**

前缀“元”在今天可能被过度使用，以暗示一些神秘的、超出正常人理解范围的东西，然而元编程的真正内涵可能更容易获得和理解。元编程只是指编写能够操作或生成代码的代码的技术。

在传统编程中，我们编写可以接收、操作和输出数据的代码。程序本身是静态的，这意味着所有的代码在执行阶段保持不变，而数据是动态的。这种类型的编程是大多数人所熟悉的。我们写一个函数，可以处理一些信息，输出期望的解给我们。

然而，在元编程中，程序和程序执行都变成了动态的，这允许我们编写一个函数来处理一些代码，并且可以输出经过修改和处理的代码版本。本质上，我们对待代码就像对待数据一样。代码和数据之间的二元性或可交换性的一个很好的类比是粒子(如电子)表现出的波粒二元性。

元编程的概念相当简单，但是我们可以用它实现多种功能。下面列出了元编程的几个目标:

*   代码生成
*   代码完成
*   代码反射

Ruby 中的元编程

**在了解了什么是元编程的所有相对模糊和理论性的定义之后，让我们真正熟悉一下这个相当强大的概念。作为一名全栈开发人员，我看到元编程在 Ruby 语言中无处不在，因此在下面的章节中，我将通过 Ruby 中的例子来巩固抽象的概念和理论。**

**考虑下面的例子:**

```
class Person def initialize(name) @name = name end def self.breathe end def dance puts "I am dancing!" endend
```

**考虑到添加了“self”前缀，我们可以很容易地识别出“dance”是一个实例方法,“breathe”是一个类方法。**

**现在，让我们实例化“Person”类:**

```
Ana = Person.new("Ana")
```

**实例“Ana”可以调用实例方法“dance”来通知用户她正在跳舞:**

```
Ana.dance
# => "I am dancing!"
```

**Ruby 语言在对用户隐藏代码方面做得非常好。我们在这里没有看到的是，Ruby 为对象“Ana”创建了一个元类，而“Ana.dance”方法保存在该元类中。**

**元类，也称为特征类或单例类，是在程序执行过程中生成的。这就是元编程发挥作用的地方。我们编写的代码生成了帮助我们创建元类的新代码。**

**回想一下，Ruby 中的几乎所有东西都是对象，这意味着 Ruby 还为“Person”类生成了一个元类。事实上，类方法“breathe”包含在其中。**

**一开始可能很难理解这个概念。简单地把元类想象成一个对象的“个人助理”。这个“个人助理”帮助一个对象承载所有的方法。“Ana”对象有一个方法“dance ”,由它的元类携带，同样是一个对象的“Person”类有一个方法“breathe ”,由“Person”的元类携带。这种类比过于简单，当然也不是对象和元类之间关系的准确轮廓，而只是简单地把这看作是对元类的模糊解释。**

**在了解了 Ruby 如何在内部利用元编程之后，作为开发人员，我们可能想要利用元编程的多功能性。事实上，我们在 Ruby 中有多种方法可以做到这一点。**

****定义 _ 方法方法****

**define_method 方法是一种强大的元编程方法，它允许我们在执行过程中定义方法。**

**让我们考虑下面的例子:**

```
class Person def watch_tv puts "I am watching tv" end def watch_netflix puts "I am watching netflix" endend
```

**上面的代码似乎是重复的，但是我们没有办法让它变得枯燥。实际上，我们可以通过使用 define_method 来干燥代码。考虑下面的代码，看看如何做到这一点:**

```
class Person ["tv", "netflix"].each do |method|
    define_method "watching_#{method}" do
      puts "I am watching #{method}"
    end
  endend
```

**我们来分解一下上面的例子。我们首先遍历一个包含字符串的数组，然后使用强大的 define_method 方法根据数组中的字符串定义方法名和方法体。这种方法的强大之处在于，“观看电视”和“观看网飞”这两种方法是由我们编写的代码生成的，因此程序执行是动态的。这是利用元编程能力的一个基本例子。强烈推荐进一步阅读。**

****结论****

**现在，让我们重温一下前缀“meta”。“元”的内涵来源于希腊语，在希腊语中，它只是“超越”的意思。因此，元程序仅仅是超出常规程序一步的程序。有了编写和操作常规程序的能力，就好像它们是输入数据一样，元编程可以有效地为我们现有的编程框架增加一个额外的抽象层，并拓宽编程语言的表达能力。**

**显然，我有意忽略了元编程的许多细微差别，以使阅读尽可能流畅和简洁。我们可能会问:元类在继承树中是如何表现的？你的问题的答案可能就在我附在下面的进一步阅读材料清单中。这篇文章有望成为您深入元编程相关研究之旅的 kickstarter，但绝不是该主题的全面指南。**

**[](https://www.toptal.com/ruby/ruby-metaprogramming-cooler-than-it-sounds) [## Ruby 元编程比听起来更酷

### Ruby 元编程是 Ruby 最有趣的一个方面，它使编程语言能够实现一个更好的编程环境。

www.toptal.com](https://www.toptal.com/ruby/ruby-metaprogramming-cooler-than-it-sounds) [](https://www.bigbinary.com/learn-rubyonrails-book/rails-macros-and-metaprogramming) [## Rails 宏和元编程

### 元编程是一种代码对代码而不是对数据进行操作的技术。可以用来写程序…

www.bigbinary.com](https://www.bigbinary.com/learn-rubyonrails-book/rails-macros-and-metaprogramming) [](https://en.wikipedia.org/wiki/Metaprogramming) [## 元编程-维基百科

### 元编程是一种编程技术，在这种技术中，计算机程序有能力将其他程序视为自己的程序

en.wikipedia.org](https://en.wikipedia.org/wiki/Metaprogramming) [](https://www.jianshu.com/p/d3b637ece518) [## 一文读懂元编程

### 元编程（Metaprogramming）是编写、操纵程序的程序，简而言之即为 用代码生成代码。元编程是一种编程范式，在传统的编程范式中，程序运行是动态的，但程序本身是静态的。在元编程中，两者都是动态的[1]…

www.jianshu.com](https://www.jianshu.com/p/d3b637ece518)**