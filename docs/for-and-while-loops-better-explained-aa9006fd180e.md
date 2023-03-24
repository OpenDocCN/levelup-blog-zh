# For 和 While 循环，更好的解释。

> 原文：<https://levelup.gitconnected.com/for-and-while-loops-better-explained-aa9006fd180e>

![](img/68ee45344738289170b27b2fffbdb7aa.png)

西西弗斯正在接受他无尽的惩罚。来源:[维基百科](https://en.wikipedia.org/wiki/Sisyphus)

对于新程序员来说，循环的概念似乎难以理解。当提到 While 和 for 循环时，一位 reddit 用户说:“我根本不理解它们。我也不明白它们之间的区别。”如果你不理解`for` 和/或`while` 循环，我有好消息告诉你。西西弗斯是来帮助你的。

看完这篇帖子，你就会明白什么是循环，以及如何在 **python** 和**其他**编程语言中实现循环。

# 什么是循环？:西西弗斯的惩罚

在希腊神话中，**西西弗斯**是伊腓拉的国王。他因自我膨胀的狡诈和欺骗而受到惩罚，被迫将一块巨大的石头滚上山，但当它接近山顶时却滚了下来，永恒地重复这一动作。

![](img/6db52bdbf30a270af40212b3b8380b60.png)

珀尔塞福涅监督西西弗斯。来源:维基百科

# 让我们拯救西西弗斯吧…

我认为西西弗斯已经受够了惩罚，是时候有人把他从这无休止的任务中解救出来了。你和我现在就去做。我们如何做到这一点？简单。我们告诉他什么时候停下来！

假设我们说:“嘿，西西弗斯，当你把石头向上滚动 100 次时，你可以停下来，然后你就自由了”。可怜的西西弗斯会很高兴地知道，他每把石头滚动一次，就离停止这项艰苦的工作更近了一步。

# 好了，故事讲够了…代码好吗？

我们将编写一个程序，使用`while` 和`for` 循环来拯救西西弗斯。

在计算机编程中，循环是一个指令序列，只要满足某个**条件**，这个指令序列就会**重复**。两种常见的循环类型是`while`和`for`循环。

## **用 while 循环拯救西西弗斯……**

现在，假设我们想用 *python* 编写一个程序，使用 while 循环来拯救 Sisyphus，我们应该这样写:

```
#*pseudo-code for saving Sisyphus*while(number_of_roll_up < 100):
  roll_up_the_stone()
```

我们的小程序规定，如果西西弗斯还没有把石头卷起 100 次，他应该重复卷起它。简单。但是我们如何记录他卷起石头的次数呢？我们包括一个**计数器**！更正确的程序应该是:

```
number_of_roll_up = 0 #Sisyphus is yet to start rolling nowwhile (number_of_roll_up < 100): #condition
   roll_up_the_stone()
   number_of_roll_up += 1 #increment number_of_roll_up each time
```

(*注意，‘#’表示 python 中的注释*)

您不需要担心`roll_up_the_stone()`函数的具体实现。首先，我们有**初始化器** ( `number_of_roll_up = 0`)，它为我们用于计数的变量设置一个初始值。

`number_of_roll_up`变量作为**计数器。**

而这个语句:`number_of_roll_up += 1`意味着我们在循环的每一次迭代之后，将`number_of_roll_up`变量的值增加 1 *。这个语句叫做**增量。**增量跟踪循环已经执行的次数。*

在每次卷起结束时，我们将`number_of_roll_up`加 1，程序**在重复循环块**之前再次检查条件。下面是同样用 javascript 写的 while 循环。

```
let number_of_roll_up = 0;while(number_of_roll_up < 100) {
  roll_up_the_stone();
  number_of_roll_up++;
}
```

一旦他卷起石头 100 次，条件`number_of_roll_up < 100` 变为假，他将停止卷起石头。

一般来说，`while` 循环可以表示如下(在 python 中):

```
while condition_is_true:
   # do some stuff
```

当条件变为假时，循环停止。在编程术语中，我们说，“我们打破循环”。

## 用 for 循环拯救西西弗斯…

对于大多数非 python 语言中的循环…(javascript，java，c++等。)

记得我们在 while 循环中有一个**初始化器**、一个**计数器**和一个**递增器**，在`for` 循环中也有它们——它采用以下形式:

```
for (**initialiser**; **condition**; **incrementer**) {     
  do_stuff(); 
}
```

`for` 循环在一行中声明了三位信息。

拯救 Sisyphus 的 for 循环程序应该是:

```
for(number_of_roll_up = 0; number_of_roll_up < 100; number_of_roll_up++) {
  do_stuff();
}
```

在 for 循环的执行中，首先执行初始化器，然后检查条件，如果条件为真，则执行`do_stuff()`。循环再次开始，但是现在**在**再次检查条件之前，将执行**增量器**。这一直持续到条件变为假。

**对于 python 中的循环…**

python 中的 For 循环很容易编写。python 中的 for 循环通常不需要预先设置 **initaliser** 。要循环指定的次数，我们可以使用 python 中的`range()`函数。

把这个范围想象成一条“数字线”，如果我们写`range(1, 20)`，我们会得到一条从 1 到…不，不是 20 而是 19 的“数字线”。问题是 python 给出了从 1 到 20 的值，但不包括 20。这意味着范围(5，100)给了我们一条从 5 到 99 的“数字线”。

现在，让我们写一个`for`循环程序来拯救西西弗斯:

```
for i in range(1, 101):
  roll_up_the_stone()
```

默认情况下，python 中的 range()函数的**增量为 1** 。因此，在循环的第一个实例中，变量`i`的值为 1，并且`roll_up_the_stone()`被执行。然后`i`增加到 2，再次执行`roll_up_the_stone()`，然后`i` 增加到 3，再次执行`roll_up_the_stone()`，这一直持续到`i`变为 100。(记住`range(1,101)`给我们从 1 到 100 的值)。

# 结论

循环是多次重复内容的好方法，为了让我们的循环不会永远运行，我们需要一种方法来记录循环已经执行了多少次(计数器**和**),并且我们还需要告诉循环何时停止(条件**和**)。

快乐编码。

**感谢您阅读至此。如果你喜欢这篇文章，请分享、评论并发表👏几次(最多 50 次)。。。也许会对某个人有帮助。**

**关注我的** [**推特**](https://twitter.com/solathecoder) **和 Medium 如果你对未来更深入、更翔实的报道感兴趣的话！**

如需进一步阅读:

*   [https://www.w3schools.com/js/js_loop_while.asp](https://www.w3schools.com/js/js_loop_while.asp)
*   [https://www.w3schools.com/js/js_loop_for.asp](https://www.w3schools.com/js/js_loop_for.asp)
*   [https://www.w3schools.com/python/python_while_loops.asp](https://www.w3schools.com/python/python_while_loops.asp)
*   [https://www.w3schools.com/python/python_for_loops.asp](https://www.w3schools.com/python/python_for_loops.asp)

[](https://gitconnected.com/learn/python) [## 学习 Python -最佳 Python 教程(2019) | gitconnected

### 50 大 Python 教程-免费学习 Python。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/python)