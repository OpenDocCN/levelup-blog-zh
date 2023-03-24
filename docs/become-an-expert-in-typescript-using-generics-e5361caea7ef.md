# 成为使用泛型的打字专家

> 原文：<https://levelup.gitconnected.com/become-an-expert-in-typescript-using-generics-e5361caea7ef>

使用泛型优化您的代码！

![](img/906f13699adefd462aff7458548f1449.png)

由[索尔马兹·哈塔米安](https://unsplash.com/@solmaz67?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/typescript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

你是否曾经编写过做完全相同事情的函数，而你不得不重复代码来支持多种类型？

如果您的答案是肯定的，那么有一个使用*泛型* **的代码复制解决方案！**

泛型是允许你开发更多可重用代码的方式。虽然语法在开始看起来有点奇怪，但一点也不复杂。

为了理解*泛型*如何工作，以及何时/如何使用它们，让我们首先开始编写一些带有代码复制的函数，我们将使用*泛型*重构它们。

假设您有一个函数，它返回给定索引处数组的值。如果你想返回*数字*，那么你的函数应该看起来像这样，

返回给定索引处的数值的函数

然后，假设您想要有相同的函数，但是我们想要传递一个由*数字*组成的数组，而不是传递一个由*字符串*组成的数组，并且在给定的索引处返回一个*字符串*值。那么我们的函数应该是这样的，

从数组中给定索引处返回字符串的函数

你能注意到我们在复制所有的东西吗？这两种方法看起来完全一样，唯一的区别是我们传递给函数的数组的类型和返回值的类型。除此之外，一切都一样。那么，我们如何重构这些方法，让一个方法接收并返回正确的值呢？

有两种简单的方法可以解决它，第一种(不是最好的)是使用类型定义。所以，重写后的方法应该是这样的，

从给定索引处的数组中返回字符串或数值的函数

前面的代码有一个主要问题。你能想到任何缺点吗？

如果出于某种原因，您想拆分调用`getValue`方法时得到的字符串，该怎么办？那么你必须做这样的事情，

从 getValue 中拆分结果字符串的函数

注意，我们必须特别检查类型是否是一个`string`。那是因为如果你试图在结果上直接调用`split`， *typescript* 会报错，因为你不能在`number`上调用`.split`， *typescript* 不确定结果是字符串，所以你必须先检查`result`的类型。

我们如何才能进一步简化这一点，避免产生额外的条件？这就是*仿制药*发挥作用的地方！

我们可以像这样简单地重写代码，

使用泛型从特定索引处的数组中获取值的函数

所以等一下…

那个诡异的`T`是什么？而且 typescript 怎么可能不抱怨呢？

那个字母`T`只是一个惯例，你可以写类似`TypeOfArray`或其他不同的东西，但是按照惯例我们通常使用字母`T`。所以，在幕后发生的事情是，typescript 用我们在调用方法时传递的值替换每一次出现的`T`。换句话说，`get<string>(strings, 0)`基本就是用`string`代替`T`。这样，Typescript 知道预期的数组应该是哪种类型以及返回类型是什么。因此我们可以调用`result1`上的`split`而不必检查它的类型。

## 真实世界的例子

到目前为止，我们已经用您可能从未用过的方法探索了假设的情况，所以让我们看看您可以在哪里真正使用*泛型* **！**

一个很好的例子就是处理 HTTP 请求以将数据保存到服务器中的类。你可以在 angular 中实现一个服务，或者在任何其他框架中实现类似的服务。

假设您有一个`Sync class`应该将一些数据保存到服务器中。如果你想在没有*泛型*的情况下做到这一点，一种方法是在你的应用程序中为每个类复制相同的`Sync class`或服务。例如，如果你有一个学生、老师、学校和一个主题`class`，你可以有 4 个不同的同步类，每种类型一个。它看起来会像这样:

学生类和学生同步类

但是那会是一个多么可怕的反模式呢？一种简化的方法是只使用一个 Sync 类来处理所有类型的请求，

保存时同步类(数据:任何)

请注意，我们所做的是将`save(data: Student)`更改为`save(data: any)`。这也是一种**不好的**方式。你应该尽量避免使用`any`。使用`any`只会混淆*类型脚本*，并且不再做进一步的类型检查。你会失去*打字稿*的威力。

我们如何解决这个问题？这也是*仿制药*发挥作用的地方！让我们重构我们的`Sync class`来利用*泛型*，

使用泛型同步类

你现在可以看到，我们已经在我们的`Sync class`中使用了*泛型*，但是为什么我们还要添加那个`HasId` `interface`？这基本上只是告诉 *typescript* 任何其他想要实例化`Sync`类型的**对象**的类应该有一个**可选的** id。如果我们没有明确地让 *typescript* 知道那个 id，那么当在 save 方法上对你的数据进行[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)时，你会得到一个**错误**，因为 *typescript* 无法知道数据包含一个名为`id`的属性。

到目前为止看起来很棒，是吧？让我们看看另一个真实世界的例子，它可能看起来更复杂(但事实并非如此)！

我们来看看下面这个**学生**，

你可以看到我们有两个方法，一个是**获取**学生的属性，另一个是**设置**属性。如果我们有一个**老师** `class`或者任何其他类，这两个方法可能是需要的。因为`get`和`set`不仅仅局限于学生，对吗？

因此，为了防止添加新模型时出现代码重复，让我们重构**学生**并创建一个新的**属性** `class`。

好了，这就是“疯狂”的部分。你现在可能想知道`K extends keyof T`是什么意思，或者为什么`get`方法的返回类型变成了`T[K]`？在 typescript 中，接口的所有键都被视为字符串！

所以基本上，我们在上面的代码中所说的是，当用`StudentProps`实例化对象时，`get`方法将把一个值作为`id`或`name`类型的参数。当用`TeacherProps`实例化一个对象时，那么`get`方法将只接受键`id`、`name` 和`isGoodTeacher`。对`get`的每次调用返回的值类型将是我们想要获取其值的属性的类型！

很棒吧？

## 结论

泛型是一个强大的工具，它允许你设计一些复杂的组合模式，并使你的代码更加可重用。开始时，它们可能看起来有点可怕或怪异，但一旦你使用它们，你就会爱上它们！

## 更多关于泛型？

*   [https://www.typescriptlang.org/docs/handbook/generics.html](https://www.typescriptlang.org/docs/handbook/generics.html)
*   [https://www . tutorialsteacher . com/typescript/typescript-generic-class](https://www.tutorialsteacher.com/typescript/typescript-generic-class)