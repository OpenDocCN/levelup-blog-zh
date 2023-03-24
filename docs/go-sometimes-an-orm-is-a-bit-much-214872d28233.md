# Go:有时候一个 ORM 有点多

> 原文：<https://levelup.gitconnected.com/go-sometimes-an-orm-is-a-bit-much-214872d28233>

![](img/39216bc30ae0c2897d3c9abfc3816524.png)

当你需要的时候，一个 ORM 是很棒的，但是有时候实现它的魔力所需要的努力是痛苦的。例如，如果您有一个包含学生数据的详细多列表的现有数据库，并且只需要某个年级学生的姓名，ORM 可能会妨碍您而不是帮助您。ORM 希望您创建模型对象，并通常以漂亮的一对一模式将它们与模式相关联。如果您只想执行特定的查询，这并不是一个很好的选择。当然，您总是可以用更基本的代码绕过 ORM，但是，使用一些泛型和函数式编程，您可以创建一个简单的函数来查询数据片段:

上面的内容看起来没什么，但它让您可以执行任意查询，并以简单、可靠、一致、干巴巴的方式将它们映射成任意类型。回到我们的学生示例:

您为返回数据定义一个结构，提供一个*绑定器*函数来绑定结果列结构中的元素，然后调用*查询*，您将得到一部分结果！这里的好处是它是特设的，结构可以是任何东西，只要有相应的 *binder* 函数。没有将模型与模式相关联，没有疯狂的结构注释，没有魔法，只是*给我一部分 X* 。该函数对模型或模式没有任何要求，它只是返回任意查询产生的任意结构的片段，除了琐碎的*绑定器*。

要查看这个函数和其他一些类似的函数，请查看 [gendb](https://github.com/nwillc/gendb) repo out。

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在转型理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)