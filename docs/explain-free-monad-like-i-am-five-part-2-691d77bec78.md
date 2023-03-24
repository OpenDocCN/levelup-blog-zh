# 像我五岁一样解释自由单子(第二部分)

> 原文：<https://levelup.gitconnected.com/explain-free-monad-like-i-am-five-part-2-691d77bec78>

![](img/663691c7ea1a9faa706a7490ec96bb4d.png)

在这篇文章的第一部分中，我们讨论了自由单子如何从函子构建单子。为此，您可以创建一个类似 DSL 的程序并生成另一个程序。您编写的程序变成了表示领域逻辑的普通数据结构。一旦你编写了程序，你就可以用解释器连接这些程序。您可以在 dev 和 QA 上切换不同的解释器。您还可以抽象出程序的实现，或者在不改变领域逻辑的情况下优化它们。

我们首先用这些代数创建一个 Todo 应用程序:

首先，我们创建了一个代数`FlatMap`和`Pure`，并介绍了如何通过将 Todo 代数包装到“自由”数据结构中来制作类似单子的程序:

然后，我们通过创建`Action[A] => Free[Action, A]`的隐式转换，创建了一种将这些代数“提升”到“自由”单子的方法:

最后，我们用我们构建的“自由”代数连接程序和解释器。

注意:这是“像我五岁一样解释自由单子(第一部分)”的简短回顾，我解释了我如何得出这些结论的思考过程。如果你有兴趣，在进一步阅读之前先看看。

[](/explain-free-monad-like-i-am-five-part-1-5bee794074bd) [## 像我五岁一样解释自由单子(第 1 部分)

### 以函数的方式用简单的数据结构构造复杂的程序。

levelup.gitconnected.com](/explain-free-monad-like-i-am-five-part-1-5bee794074bd) 

# 问题

上面的解决方案似乎可以使用免费的 Monad 为 Todo 应用程序构建一个程序。然而，该解决方案仅适用于该特定应用。我们不能使用我们的`runProgram`函数或将我们的代数“提升”到“自由”版本。如果我们需要为我们构建的每个程序实现`runProgram`,并且在每次构建新的代数集时创建一个隐式转换，这将是非常乏味的。

# 广义升力

为了推广 lift，我们只需要将`Action`抽象成一个`F[_]`类型的构造函数:

但是，如果我们这样做，将会导致编译错误，因为很多时候，我们希望提升具体的类型构造函数，而不是一般的“密封特征”:

这将在 for-comprehension 中抛出一个错误，因为它不能自动将值提升到`Action`密封特征。

一种方法是创建一个“智能构造器”，首先将值设置为一般代数`Action`，并定义将常规输入抽象为“自由”版本的函数。

记住“自由”类型的返回是由广义代数返回类型定义的。例如，`create`将返回一个`TodoAction[Todo]`，因为`Create`的代数返回一个`Action[Todo]`:

```
case class Create(description: String) extends Action[Todo]
```

然后我们可以用这些智能构造函数来构造我们的程序:

# 一般化运行程序

引用自[上一篇](https://edward-huang.com/functional-programming/scala/programming/monad/2020/09/06/explain-free-monad-like-i-am-five-part-1/)文章中的`runProgram`:

当前运行程序有一个`execute`功能与带有`Action`的功能相关联。此外，`Free[Action,A]`仅限于特定的`Todo`应用。如何进一步推广这个来取`F[_]`的任何一个程序？

我们可以通过采用类型构造函数`F[_]`来一般化`runProgram`，但是我们如何一般化`execute`函数呢？

不知何故，`execute`函数应该是用户在创建程序时定义的通用函数。因此，需要将某个`trait`作为参数传入。如果仔细观察上面的实现，函数`execute(fa)`接受一个`F[A]`并返回一个`A`。然后，该`A`可以是`fn`功能的输入。因此，我们需要定义接受一个`F[A]`并返回一个`A`的`execute`函数。让我们在一个`Executor`特征中定义`execute`函数:

我们将`Executor`特征类作为一个参数传入`runProgram`中，并在该`Executor`特征中调用`execute`:

然后，我们可以在`Executor`、`execute`函数中为 Todo 应用程序定义解释器，并将值传递给`runProgram`:

```
runProgram(program, executor)
```

通过提供解释我们的代数的自定义“execute ”,我们终于可以在任何程序上运行`Free[F,A]`。然而，我们能进一步推广这一点吗？

# 一般化执行器

当前的`Executor`函数接收一个`F[A]`并返回一个`A`。但是，该功能仅限于纯效果类型。我们可以通过推广它的效果类型来进一步推广它。如果你不确定 effect 是什么意思，看看我的文章[在函数式编程中“effect”或“effective”是什么意思？](https://edward-huang.com/functional-programming/scala/monad/2020/06/21/what-is-effect-or-effectful-mean-in-functional-programming/)

函子之间的这种变换叫做[自然变换](https://typelevel.org/cats/datatypes/functionk.html)。我们可以有`F[A] => G[A]`，而不是有一个等同于`F[A] => Id[A]`的`F[A] => A`签名，其中`G[_]`可以是任何类型的构造函数。

让我们重构我们的`Executor`函数，加入两个类型构造函数:

然后，`runProgram`会带一个额外的类型构造函数`G[_]`:

您注意到上面的代码无法编译。最初，`execute`函数返回`A`。然而，在将我们的`Executor`函数改为返回`G[A]`后，我们不能将结果传递给`FlatMap`中的`f`函数。

我们可以通过要求`G`为单子来解决这个问题，并使用`flatMap`将`G`中的值绑定到`f`:

# 结论

在本文中，我们将学习如何通过推广我们的“免费”包装器来进一步推广我们的应用程序，以便在各种应用程序中使用它。

首先，我们推广了`lift`函数，并引入了一个“智能构造器”来构造我们的程序。然后，我们通过提交一个`Executor`特征来概括我们的`runProgram`，即运行程序的解释器。最后，我们通过在我们的`Executor`特征上引入自然转换来进一步推广我们的`Executor`特征。

自由结构以不同的名称存在于`Cats` [库](https://typelevel.org/cats/datatypes/freemonad.html)中。希望你能理解免费的概念以及免费是如何运作的。也有自由应用的，区别在于`runProgram`和它的“自由”代数，它不是一个单子，而是一个应用功能。

这篇文章的所有源代码可以在[这里](https://github.com/edwardGunawan/Blog-Tutorial/tree/master/ScalaTutorial/freeMonad/src/main/scala/GeneralizedFreeStructure)查看。

来源与参考:[游离单子讲解(pt 1)。构建可组合 DSLs |作者 Oleksii Avramenko | Medium](https://medium.com/@olxc/free-monads-explained-pt-1-a5c45fbdac30)[Cats:free monads](https://typelevel.org/cats/datatypes/freemonad.html)

**感谢阅读！如果你喜欢这篇文章，请随意订阅我的时事通讯中的**[](https://edward-huang.com/subscribe/)****以获得关于科技职业的文章、有趣的链接和内容的通知！****

**你可以关注我，也可以在 [Medium](https://medium.com/@edwardgunawan880) 上关注我，获取更多类似的帖子。**

***原载于【https://edward-huang.com】[](https://edward-huang.com/functional-programming/scala/programming/monad/2020/09/20/explain-free-monad-like-i-am-five-part-2/)**。*****