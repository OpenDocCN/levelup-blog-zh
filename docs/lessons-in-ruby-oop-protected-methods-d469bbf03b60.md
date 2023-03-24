# Ruby OOP 课程:受保护的方法

> 原文：<https://levelup.gitconnected.com/lessons-in-ruby-oop-protected-methods-d469bbf03b60>

![](img/e06b1606311c4ab1caea9865a1bb68f8.png)

由 [Bonnie Kittle](https://unsplash.com/@bonniekdesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/fence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

正如我之前在说过的[，涉水通过](https://medium.com/@callie_buruchara/lessons-in-ruby-oop-duck-typing-2ced1809ca76)[启动学校](https://launchschool.com/)的 RB120 和面向对象编程范式是同时压倒一切和大开眼界。我意识到我们必须首先“艰难地”学习 RB101 中的一切，但获得 OOP 如此荣耀地提供的所有工具和更好地为我的大脑组织也是非常美妙的。

尽管如此，这仍然是一个很大的飞跃。当我在为 RB129 评估学习时，我一直停留在受保护的方法上。我理解私有方法的需要和公共方法的使用；但是这个明显必要的中间地带的目的是什么呢？

在我们明确讨论受保护的方法之前，先讨论一下方法访问控制的目的是有帮助的。

封装是 OOP 编码范例的众多好处之一，它允许我们，代码的作者，隐藏代码功能的某些部分，然后只暴露我们有意选择的部分。方法访问控制——使用公共、私有或受保护的方法——是我们用来实现意图的工具。

让我们首先展示过程化编程是如何缺乏这种封装的(对比是清晰之母！).看看下面的代码:

特别感谢我的朋友 Stephen 用这种方式向我解释了封装！(显然是从[马特](https://medium.com/@mattdjohnston92/towards-a-conceptual-model-of-object-oriented-programming-118eb971659f)那里学来的！所以谢谢你，马特！)

在`line 1`时刻，我们初始化局部变量`person`，并将其赋给`'Sally'`。我们可以立即在`line 2`上输出它——也就是说，没有任何东西保护它或阻止我们访问它。在`line 5`上，我们看到我们甚至可以改变变量的值。同样，没有任何东西可以保护这个局部变量不被访问或操纵。对于一个更复杂的程序，我们会遇到作用域问题，这可能会阻止我们操作变量，但这更多的是初始化位置的结果，而不是有意的保护。

如果我们想保护`person`呢？如果我们想使用它的功能，但不让任何人直接看到它，或者至少不改变它，会怎么样？

进入 OOP！(提示英雄音乐)

封装允许我们有意隐藏或暴露代码的功能。我们可以做出有意识的选择，而不是让一切暴露和脆弱。

一旦我们在代码中引入类，就会发生这种情况:

在我们的`Person`类中，我们使用我们的构造函数(`initialize`)来接收一个参数，并将其分配给新的`Person`对象的`@name`实例变量。现在，如果我们想要访问那个`name`，我们必须有意识地创建一个这样做的方法。我们还没有，所以`line 8`没有给我们名字。但是如果我们这样做:

在`line 2`上，我们用`attr_reader`创建了一个 getter 方法。我们现在可以访问我们的`Person`对象的名称，因为我们有意地为我们创建了一个这样做的方法。太好了！

但是假设我们有一个更复杂的例子。我们有想在实例方法中使用的代码，但是我们不希望它被代码库的其余部分访问。例如:

这里我们有一个`Dog`类，它有一个`older?`实例方法来比较两个对象的年龄。这段代码运行良好！

但是，如果我们不想让世界知道`sadie`的年龄呢？她长大了，她是一个自我意识很强的人，所以我们想尊重她的隐私。我们仍然想知道她是否比另一只狗大，但是我们不需要知道她的确切年龄。我们可以把那部分留给神秘。

因为我们想要隐私，我们可能会尝试使用`private`关键字。然而，如果我们这样做，我们将无法在`line 8`上调用`other`上的`age`。为什么？因为使用`private`方法，你不能在一个显式调用者上使用它们(其中`other`正试图成为一个显式调用者)。因此，既然我们(a)想在一个显式调用者的类内部使用`age` getter 方法，并且(b)不想在类外部使用`age` getter 方法，我们可以使用……`protected`方法！

现在我们可以随心所欲了。

总之，`protected`方法是封装的一部分，它们允许你像在类中使用`public`方法一样使用方法(可以在显式调用者中使用它们，等等)。)，它们将限制访问，并被视为类外的`private`方法。

我希望这有所帮助！。如果你是 [Launch School](http://launchschool.com) 的学生，并且想讨论这些概念或者 OOP 中的任何东西，请随时联系我！向前向上，同学们。

[1]从 Ruby 2.7 开始，你可以在一个显式的接收者上调用私有方法，但前提是那个接收者是`self`…但那是另一篇文章了