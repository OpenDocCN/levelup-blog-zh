# 我是一个懒惰的开发人员，但效率很高😘

> 原文：<https://levelup.gitconnected.com/im-a-lazy-developer-but-be-efficient-600437ecca2e>

## [思维程序员](https://medium.com/tag/thought-programmer)

## 我将一切自动化，以便有时间发明更多东西

*本帖中的故事是我在日常工作中遇到并记住的东西。*

![](img/f70b4c8525e2c77b858dd0c239b84ec7.png)

凯特·斯通·马西森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

各位好，我现在 30 多岁了。我从 16 岁开始写计算机应用程序，我喜欢它。

> 我选择一个懒惰的人去做艰苦的工作。因为一个懒惰的人会找到一个简单的方法去做

我只是一个非常懒惰的开发人员，想让其他人的一切变得更容易。

我总能找到最好、最有效的做事方法，只是因为我懒得用费力的方法做事。

# 较长命令的短别名

像大多数人一样，我真的厌倦了在命令行中键入一个长命令，或者在 bash 历史中搜索以前键入的命令。

```
% ps auxf | sort -nr -k 4 | head -10
```

因此，我使用 bash 别名来避免记住长命令:

```
% alias psmem10 = 'ps auxf | sort -nr -k 4 | head -10' 
```

这里有一些我保存在机器里的别名的例子。

*   如果您想显示所有已定义别名的列表，让我们给出一个不带任何参数的`alias`内置命令。

```
% alias
```

*   我们必须使用以下语法来创建别名。

```
% alias name=value
% alias name='command'
% alias name='command arg1 arg2'
% alias name='/path/to/script'
% alias name='/path/to/script.pl arg1'
```

*   如果我想清除屏幕，我使用下面的别名。

```
% alias c='clear'
% c
```

*   以下别名允许您键入`r`来重复前面的命令，或者键入`r abc`来重复以 **abc** 开头的最后一个命令行:

```
% alias r='fc -s'
% r
```

*   为了节省你的时间。

```
% alias h='history'
% h
```

*   显示开放的端口

```
% alias ports='netstat -tulanp'
% ports
```

*   按文件大小排序

```
% alias lt='ls --human-readable --size -1 -S --classify'
% lt
```

*   按修改时间排序

```
% alias left='ls -t -1'
% left
```

*   如果你想知道一个目录中有多少个文件。

```
% alias count='find . -type f | wc -l'
% count
```

在某些情况下，您需要使用真正的命令，而不是已经存在的别名。

```
% \aliasname
```

分享知识很好，你应该通过评论与大家分享你的别名。

# 自动任务的脚本

凡是能自动化的，我都会自动化。

> 我将一切自动化，以便有时间发明更多东西

由于一切都变得自动化，我有时间为自己想出许多想法，并把它们变成现实。

## 自动化的类型

在我看来，自动化有以下三种类型。

*   ***数据驱动自动化*** 。它包括基于数据指标的自动化操作。
*   ***任务驱动自动化*** 。它包括将原本需要手动完成的任务自动化。
*   ***端到端自动化。*** 当你需要接触这些组件中的几个以实现一个目标时，你可以使用端到端自动化。这是数据驱动和基于任务的自动化的结合。

## 在哪里应用自动化

我已经在日常生活中以不同的方式将自动化应用于许多不同的领域。

例如，您可以编写一个脚本来收集网络统计数据，创建仪表板和报告，并在达到阈值时通知我。

例如，您可以编写一个脚本来打开一个网站，并执行一些任务，如抓取该网站的内容。

2010 年初，我和我的好同事开发了一个非常大的客户服务系统。

我们的资源有限，所以为了有更多的时间开发应用程序，我们编写了许多脚本来自动化手动任务，如 zip 日志、删除文件等。

## 自动化脚本的示例

在这个脚本中，我们将检测用户何时登录或注销系统。

```
numusers=`who | wc -l`
while [ true ]
do
 currusers=`who | wc -l`                          
if [ $currusers -gt $numusers ] ; then
    echo "Someone new has logged on!"
    date
    who
    numusers=$currusers
elif [ $currusers -lt $numusers ] ; then
    echo "Someone logged off!"
    date
    numusers=$currusers
fi
sleep 1                                            
done
```

# 使用重用工程

事实上，对于类似的问题，我们总是使用相同的解决方案。作为一名程序员，你有多频繁地看到自己一遍又一遍地编写相同的代码框架？

经常是吧？因此，我应用重用工程来获得以下好处。

*   ***缩短软件开发时间。当一个正在运行的应用程序需要一段已经存在的代码时，我节省了时间。***
*   **更大的时间和成本效益。在一个良好的实践环境中，现有的代码伴随着现有的文档、现有的质量保证和兼容的测试结果。**

在开发软件系统的过程中，实现高水平的重用可能是最难实现的目标。如果可以重用其他产品或开源软件中的大型组件，您可以节省大量时间和精力，但是您需要知道应该重用哪些组件以及如何实现它们。

以下是一些方法。

*   模式的重用。
*   创建通用程序或可配置框架，为各种用例及环境支持类似的解决方案。
*   使用带有模型模式的模型驱动方法。

**后方更多此处**:[*https://level up . git connected . com/software-engineering-best-practices-for-reuse-in-software-development-6006 F9 d8d 364*](/software-engineering-best-practices-for-reusing-in-software-development-6006f9d8d364)

# 编程方面我是个懒人…

在编程中，我也应用懒惰的编码技术，这也是程序员日常习惯的一部分。

## **惰性实例化**

这意味着除非你真的需要，否则不要花费任何时间和资源去创造东西。

惰性实例化的一个用例是在单例模式中。惰性实例化确保对象在真正需要的时候被创建。

```
class Singleton:
    __instance = None
    def __init__(self):
        if not Singleton.__instance:
            print(" __init__ method called..")
        else:
            print("Instance already created:", self.getInstance())
    @classmethod
    def getInstance(cls):
        if not cls.__instance:
            cls.__instance = Singleton()
        return cls.__instance## class initialized, but object not created
s = Singleton() # Object gets created here
print("Object created", Singleton.getInstance()) ## instance already created
s1 = Singleton()
```

## ***懒惰初始化***

这是一种缓存未更改数据的技术。

## 懒惰评估

它是一种求值策略，将表达式的求值延迟到需要其值时。

由`range()`(或者 Python2.x 中的`xrange()`)返回的对象被称为惰性可迭代对象。生成器不是将整个范围`[0, 1, 2,..., 9]`存储在内存中，而是存储一个`(i=0; i<10; i+=1)`的定义，并仅在需要时计算下一个值(也称为惰性求值)。

# 但是成为一个懒惰的人并不总是好的，但确实很有趣

在 15 年的经验中，我发现懒惰的行为在某些情况下是不好的。

*   你可以跳过最重要的来节省时间。谨记“ ***做尽可能少的工作来完成任务，但不能少了*** ”。
*   代码可能有气味，糟糕的代码气味经常被懒得收拾残局的程序员忽略。
*   懒惰的程序员不仅会忽略清楚的技术债务，他们甚至会忽略技术债务的报告。
*   作为一般的聪明人，大多数程序员都非常独立，喜欢“走自己的路”因此，他们未能建立和执行标准

是的，一个懒惰的人并不总是好的，但肯定是有趣的。

***懒惰的人很可能更聪明、更成功、更优秀的员工。同意吗？***

感谢阅读，不要忘记我的一些建议。以下是我的 10 篇最佳文章。

1.  [如何设计一个可以扩展到你的第一个 1 亿用户的系统](/how-to-design-a-system-to-scale-to-your-first-100-million-users-4450a2f9703d)
2.  在我 15 年的软件工程师生涯中，我学会了避免的事情
3.  [停止在你的代码中使用‘else’关键字](https://javascript.plainenglish.io/stop-using-the-else-keyword-in-your-code-907e82b3054a)
4.  我是如何根据目的对 50 种图表类型进行分类的？
5.  [不要做一个愚蠢的程序员](/dont-be-a-stupid-programmer-be53ed49f99)
6.  软件架构:你需要知道的最重要的架构模式
7.  [重新思考 JavaScript 中的坚实原则](https://javascript.plainenglish.io/rethinking-solid-principles-in-javascript-7effdd4dc37d)
8.  [这些瓶颈正在扼杀你的代码](https://towardsdatascience.com/how-did-i-classify-50-chart-types-by-purpose-a6b0aa5b812d)
9.  [每个开发者都必须知道的前 20+Github Repos](/top-10-github-repos-every-developer-must-know-9da14292e284)