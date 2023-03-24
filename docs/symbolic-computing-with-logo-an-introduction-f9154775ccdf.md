# 带 Logo 的符号计算:导论

> 原文：<https://levelup.gitconnected.com/symbolic-computing-with-logo-an-introduction-f9154775ccdf>

![](img/9d1792cf923723dac7f0f62bbfaf8f52.png)

杰瑞米·泽罗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

本文使用一种通常被认为是儿童教育编程语言的语言——Logo，向读者介绍了符号计算的世界。我将从描述什么是符号计算开始，然后深入到徽标编程语言的旋风之旅中。在以后的文章中，我将花更多的时间演示如何使用 Logo 来编写一些强大的程序。我希望本系列文章鼓励您使用 Logo 或另一种符号语言(如 Lisp 或 Scheme)探索符号编程。

# 标志编程语言在教育中的简史

Logo 最初是由已故教育计算机科学家西蒙·派珀特和他在麻省理工学院人工智能实验室的同事在 20 世纪 60 年代开发的。Logo 最著名的是它的乌龟图形，这使得儿童(和成人)可以通过在屏幕上移动虚拟乌龟来创建复杂的几何形状和图案，命令如前进 50，向右 90，前进 100。[这里](https://www.youtube.com/watch?v=g6kmVHfMQvY)是一个海龟图形的视频。

徽标和海龟图形在 20 世纪 70 年代开始流行，作为通过海龟图形教授 K-12 学生计算机编程的一种方式。Logo 还被用来教授数学，尤其是几何，因为学生可以用海龟图形创造非常复杂的几何图形。然而，这种语言从未真正流行起来，因为大多数公立学校没有足够的资金来购买对教育产生重大影响所需数量的计算机。此外，没有足够的受过 Logo 培训的教师来提供有效的教学。

# 符号计算导论

除了 power turtle 图形系统，Logo 还有一套强大的符号计算编程特性。符号计算有很多含义，但就我的目的而言，我将使用这个短语来表示用单词和句子而不是更基本的数据(如字符串和数组)进行计算，尽管 Logo 也可以对数字进行计算，但即使在这里，数字也是用符号表示的，而不是用数字数据类型(如整数或浮点数)表示的。

我将通过展示一些简单的*列表处理*的例子来介绍 Logo 中的符号计算。在我开始之前，我正在使用一个名为 Berkeley Logo 的 Logo 版本，我会在文章的最后提供一个链接。

Logo 中的主要数据结构是列表。列表是符号的集合，如单词或数字。列表是通过将符号放在一组方括号内形成的，如下所示:

```
make "words [the quick brown fox jumped over the lazy dog]
```

一旦创建了列表，就可以使用列表处理命令对其进行检查。例如，要查看列表中的第一个单词，我可以写:

```
print first words
```

该命令显示:

```
the
```

我可以像这样显示列表中除第一个单词以外的所有单词:

```
print butfirst words
```

它显示:

```
quick brown fox jumped over the lazy dog
```

您可以组合像`first`和 `butfirst`这样的命令来执行更复杂的计算:

```
print first butfirst words
```

它显示:

```
brown
```

Logo 语言和大多数符号编程语言一样，鼓励使用函数(过程)来完成大多数计算。下面是显示列表第二个元素的过程:

```
to second :list
  output first butfirst :list
end
```

`output`命令类似于其他编程语言中的`return`关键字。

现在让我们使用函数来获取列表的第二个元素:

```
print second words
```

它显示:

```
quick
```

# 徽标中的递归

递归是在 Logo 中迭代数据的首选方式。下面是一个递归过程的示例，它一次一个符号地显示列表的内容:

```
to down :list
  print first :list
  down butfirst :list
end
```

以下是我运行该过程时显示的徽标:

```
down wordsthe
quick
brown
fox
jumped
over
the
lazy
dogfirst doesn't like [] as input  in down
[print first :list]
```

该过程以一个错误结束，因为命令`first`不喜欢空列表(`[]`)。当列表为空时，我需要一种方法来停止这个过程。我可以使用一个`if`语句来完成。下面是我的过程现在的样子:

```
to down :list
  if emptyp :list [stop]
  print first :list
  down butfirst :list
end
```

这里，`if`语句使用内置函数`empty` p 检查列表是否为空。如果是，则发出`stop`命令，这类似于其他语言中的`break`。否则，该过程打印列表的第一个元素，并对除第一个元素之外的所有元素进行递归。

# Logo 中的算术

Logo 可以使用熟悉的符号`+`、`-`、`*`、 `/`进行标准运算:

```
? print 1 + 2 + 3 + 4
10
? print 12 + 23
35
? print 12 - 23
-11
? print 12 * 23
276
? print 12 / 23
0.521739130434783
```

您也可以使用内置函数来做同样的事情:

```
? print sum 12 23
35
? print difference 12 23
-11
? print product 12 23
276
? print quotient 12 23
0.521739130434783
```

Logo 也有模数运算，但不是带运算符的。您必须使用`remainder`功能:

```
? print remainder 17 5
2
```

Logo 还有一个内置的平方根函数:

```
? print sqrt 9
3
```

在以后的文章中，我会有更多关于在 Logo 中做算术和数学的内容。

# 获取徽标

我最喜欢的 Logo 版本是伯克利 Logo (ucblogo)。它最初是由加州大学伯克利分校的名誉教授 Brian Harvey 与该大学的学生和其他同事一起开发的，但现在的主要开发者是 Josh Cogliati。

这里的是一个到 ucblogo 网页的链接。我鼓励您下载适合您最喜欢的计算环境的语言，并开始使用 Logo。我很快会带着一篇关于真正开始徽标编程的新文章回来。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。