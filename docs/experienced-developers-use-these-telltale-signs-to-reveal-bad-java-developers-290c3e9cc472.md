# 有经验的开发人员，使用这些警告标志来揭露糟糕的 Java 开发人员

> 原文：<https://levelup.gitconnected.com/experienced-developers-use-these-telltale-signs-to-reveal-bad-java-developers-290c3e9cc472>

## 以下是我用来揭露糟糕的 Java 开发人员的特征

![](img/8d4876c6c1fd369050d80f9926d741cf.png)

照片由来自[佩克斯](https://www.pexels.com/photo/man-person-people-woman-6914069/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[马体·米罗什尼琴科](https://www.pexels.com/@tima-miroshnichenko?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

*你和糟糕的 Java 开发人员一起工作过。*

这就是你在这里的原因。我也有同样的问题。我有一次不愉快的经历，这使我辞去了这份工作。

*姑且称这个烂开发者为 Jim 吧。*

吉姆:“祝你假期愉快，我会修复那个 403 错误的。”

*我:“好的，这是配置中的另一个角色，在您启用它之后，您应该修复角色问题。”*

我度假回来发现一个 404 错误。

*我:“嘿，Jim，为什么我现在看到这个 404 错误？是否添加了预期角色？”*

*Jim:“我已经删除了整个配置，所以现在我们得到 404。我还没有尝试添加这个角色。”*

我们又损失了一周时间，推迟了功能发布。

在这个过程中，我会看到很多带注释的测试。当我们把它推向 QA 环境时，我们收到了很多抱怨。你可以猜到是谁解决了这些问题。

我不会把任何代码交给他。因为他没有主人翁意识，所以他的代码是垃圾。代码工作正常，按照他的标准，这就足够了。

吉姆的同事也离开了。 我不知道为什么，但他的代码也是垃圾。他复制粘贴了一堆代码就走了。这段代码不工作，我花了一周时间解决这个问题。

当我离开公司时，另一个人取代了我。甚至几周后他就辞职了。

如果我知道吉姆的能力，我会更加小心。我不会在休假时期待问题得到解决。我不会那么信任他。

你如何判断一个糟糕的 Java 开发人员？有哪些不好的 Java 开发人员特质？

我会求助于代码审查。这就是你看到开发商素质的地方。下面是在代码审查中要寻找的内容。

# 优秀的 Java 开发人员重视清晰性

优秀的 Java 开发人员会求助于更新的 Java 特性。

他知道`Optional`和`Optional`是如何工作的。[我见过很多对](https://blog.devgenius.io/experienced-developers-are-allergic-to-these-5-bad-optional-practices-ee1014940198?source=user_profile---------2-------------------------------) `[Optional](https://blog.devgenius.io/experienced-developers-are-allergic-to-these-5-bad-optional-practices-ee1014940198?source=user_profile---------2-------------------------------)`的误用和滥用。只来自糟糕的工程师。

*好的开发者不虐* `*Optional*` *。*

`Optional`用来表示没有价值。有些开发人员会用它来创建自我提升的解决方案。你就是这样引入 bug 的。

*我也见过* `*orElse*` *和方法一起使用。*块`orElse`中的方法将会执行并困扰你。另一个你应该小心的误用是`isPresent`和`get`的用法，并且不是按照这个特定的顺序。

`Optional`过度工程会打击 app 性能。[在执行时间上，性能损失可能高达两倍。](https://blog.devgenius.io/experienced-developers-are-allergic-to-these-5-bad-optional-practices-ee1014940198)这些可以复利，你将需要偿还它们。

***这些都说明了 codebase 的质量。这些可以显示开发人员对清晰性的重视超过了自我提升的解决方案。***

# 他们不虐待兰姆达斯

*糟糕的 Java 开发者不看重函数接口。*

使用 lambdas 有很多好处。Lambdas 的性能更好，更容易编写，也更容易阅读。除了好处，它们也显示了 Java 开发者的素质。

***他们不懂兰姆达斯的好处。他们会求助于急切的实现而不是懒惰的实现。这更难维护，但他们不关心这个。***

***好的开发者使用功能接口。他们甚至创造定制的。他们应用现有的代码来创建简洁的代码。这段代码也更容易维护。***

***“我们可以在这里使用 lambda 或者方法引用。”*** —对不良开发商 PR 的常见 PR 评论。

***“这个λ应该小一点，这个很难跟。”*** —你必须写的另一条评论。

# 对异常缺乏了解

*糟糕的 Java 开发者不知道如何处理异常。双关语。*

他们不知道已检查或未检查的异常。它们会抛出 catch 块并丢失堆栈跟踪。或者他们不使用检查异常来翻译异常。不管怎样，不好的事情发生了，你深陷其中…

***我要说的是*** [***异常处理是 Java 开发的重要方面***](https://medium.com/javarevisited/3-exception-practices-to-improve-your-java-skills-597780317e5f) ***。***

你应该注意例外的做法。最好的办法是研究一下减贫战略。你可以在我的代码审查食谱中学习一般的技巧。根据经验，要小心异常逻辑。

***理解异常翻译。*** 詹姆斯·高斯林指出这是显示错误的更好方法。以下是他对异常翻译的看法。

[***詹姆斯·高斯林***](https://web.archive.org/web/20060407124638/http://www.artima.com/intv/solid3.html) *:这就是异常翻译可以成为一件好事的地方。如果你有一个应该读取数据库的东西，并且在它内部的某个地方，它可能会得到一个* `MalformedURLException` *，把它作为* `MalformedURLException` *向外传播可能是没有意义的。*

最好想想什么样的异常是接口的一部分，而不仅仅是底层方法是如何实现的。在 Java 异常机制中，异常可以引用其他异常作为其原因。于是你创造了一个新的 `IOException` *，你把它的起因设定为这个* `MalformedURLExceptio` *。你把它们捆在一起。这非常有效。*

不要和我犯同样的错误。 评估你的工作环境。不要浪费你的时间。在代码评审中标记糟糕的代码质量。不要让代码质量让你讨厌你的工作。

*糟糕的 Java 开发人员还有其他危险信号。*这些只是冰山一角。尽管如此，我不能在一篇文章中列出它们，因为我可以写一本关于危险信号的书。