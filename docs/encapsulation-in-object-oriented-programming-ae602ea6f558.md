# OOP 封装到底封装了什么？

> 原文：<https://levelup.gitconnected.com/encapsulation-in-object-oriented-programming-ae602ea6f558>

## 是一只鸟！是一架飞机！是封装！

## 抽象和封装是齐头并进的。

既然我们已经讨论了抽象(在这里阅读全部内容！)，我们将进入 OOP 的下一个支柱，**封装**！很难区分抽象和封装。有些人根本不把它们分开，认为它们是一个硬币的两面。也就是说，如果这一开始会让你的大脑受到一点伤害，那是非常正常的。我鼓励你多看几遍这两篇关于这些话题的博客，一旦你对这两个话题有了更多的了解，我保证它们最终会吸引你的。

![](img/f9b05f54d24562efe4e23ffa188df61c.png)

It’s an object! Or maybe a class! It’s ENCAPSULATION! (Photo by [贝莉儿 DANIST](https://unsplash.com/@danist07?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/plane-blueprint?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText))

# 在抽象的引擎盖下。

如果抽象是为了简单和易于使用/理解而降低复杂性，那么封装就是在这种简化背后发生的事情。例如，假设你看到一架飞机从头顶的天空中飞过。飞机的形状是一个熟悉的、*抽象的*概念，足以让你自信地指着它说“这是一架飞机！”尽管你不太可能有把握说出这架飞机是波音 737、747 还是 767，或者飞机上宣传的是哪家航空公司。然而，因为“飞机”是一个抽象的概念，你可以假设关于这架飞机的一些事情:它可能有翅膀，运载乘客或货物，并且是为了飞行。

抽象让你专注于一个对象或类做什么而不是如何做，而封装是隐藏一个对象或类如何工作的内部细节的过程。在我们的飞机例子中，抽象是帮助你假设 ***飞机做什么*** 的东西，而封装则是 ***飞机如何做:飞机如何飞行，如何着陆，如何加油，等等。封装是关于幕后发生的事情。***

![](img/be17e7410b2a3ed4dfc64b469c55c666.png)

软件工程和航天工程基本是同一个领域……对吧？对我来说有道理。(照片由[斯文·米耶克](https://unsplash.com/@sxoxm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/blueprint?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄)

## 封装这个词的一个定义是:

> *“把(某物)装在或好像装在胶囊里”*

OOP 编程中这种“胶囊”最普遍的例子是一个类。在一个类中，我们将封装属于该类的属性和方法。如果我们创建了一个 plane 类，我们可能有一个 fuelLevel 属性，也可能有一个 maxCapacity 属性。我们也可能有几个方法和或函数，比如起飞( )和着陆()。在抽象的罩下“隐藏”这些细节的过程就是我们在 OOP 中所说的封装，“将数据和行为一起绑定在一个单一的[单元](https://stackoverflow.com/questions/16014290/simple-way-to-understand-encapsulation-and-abstraction)”。

![](img/4c58795eefd710668e81c39fa0cefbb7.png)

你可以选择红色胶囊或蓝色胶囊……无论哪种方式，OOP 仍然会有点复杂(图片由[paweczerwi ski](https://unsplash.com/@pawel_czerwinski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/pill?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄)

## “隐藏”细节是封装的另一个重要方面。

隐藏某些数据的能力允许我们决定如何与一个对象交互。对于任何对象，必然会有我们可以公开的数据和我们希望保密的数据。我们想保护飞机乘客的个人信息。然而，将燃料水平公之于众可能是重要的，以便相关方知道还剩多少燃料。再说一遍，我们不希望任何人都可以重置我们的燃料数量。我们希望建立一种方法，以便只有当授权方执行给飞机加油的功能时，才调整燃料水平。

## 那么，我们封装的时候要做什么呢？

我们围绕绑定在一起时最有效的目标属性和功能划定界限。我们用类来划分这些界限。当我们构建这些类时，我们建立了与其中的数据和函数进行交互的规则和限制。这就是封装。

下周，[传承 ！](https://medium.com/irregularly-scheduled-programming/inheritance-in-object-oriented-programming-d8808bca5021?source=---------3------------------)

延伸阅读:

*对*[*Java 的抽象与封装的分解访问*](https://javarevisited.blogspot.com/2017/04/difference-between-abstraction-and-encapsulation-in-java-oop.html)

*各种有帮助的答案上* [*栈溢出*](https://stackoverflow.com/questions/16014290/simple-way-to-understand-encapsulation-and-abstraction)

*在*[*FreeCodeCamp*](https://www.freecodecamp.org/news/object-oriented-programming-concepts-21bb035f7260/)上向一个 6 岁的孩子解释 OOP