# 无需 Pythonpath 技巧即可导入您自己的 Python 代码

> 原文：<https://levelup.gitconnected.com/import-your-own-python-code-without-pythonpath-tricks-9068495c1bba>

## 使用一行代码和适当的代码结构

> **“我甚至不能导入我自己的脚本！”**
> 
> **“但它在我的笔记本电脑上工作正常！”**

是的，苦难之神有许多面孔。*最后一个是我的最爱。*

让我来解释一下这个问题。
你的同事给了你这些精彩的、全新的 Python 脚本，来*终结世界上所有的不公正*。您已经准备好运行它们，但是**您不能导入它们的任何函数和类**。

**你不仅不能将它们从一个脚本导入到另一个脚本中，**你也不能从你正在运行的 Python 解释器中导入它们。或许你可以，但前提是*木星*和土星成一直线。在同一个文件夹里。或者像一个 *PyCharm* 。没有人能做得和他一样。

所以你*(或者你的好心同事给你)*选择了东拼西凑的方式。

*苦难之道。*

**你修改了你的** `PYTHONPATH` **。**或类似。你被迫这样做。**而且很乱。**

![](img/163e838d27eac7c9365f6bfbbf3db529.png)

让我们试着把它分类。照片由 [Noor Sethi](https://unsplash.com/@noorsethi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 代码的导入混乱

你可能已经注意到了，我正在谈论 Python 导入可能产生的**混乱。或者，更好的是，**乱七八糟的配置**能产生的混乱。**

因为进口本身没有缺点，*可怜的小生物* *(那不是真的，我确实有一些关于那方面的话要说……但不是现在)。*

真正的问题是它们被使用或应该被使用的方式。

# 从戏法到鬼

我见过几个避免这个导入问题的方法。
这里只是特别提一下，**强调为什么它们总体上不好**以及它们会带来什么风险。

> **如果你只是想知道最终的工作方案，而不知道为什么比这些招数好，那就跳过这一部分吧！**

## `PYTHONPATH`的幽灵

第一个是最快的:改变`PYTHONPATH`环境变量。

基本上，它像系统路径变量一样工作。**它告诉 Python 在哪里搜索额外的脚本和模块。**您只需将包含文件夹的路径添加到它的值列表中，就可以开始了。

*漂亮的把戏。*

但是你不得不分享你的代码，甚至只和你的合作者分享，或者因为某些原因你不得不把它转移到其他机器上。什么都不管用了。

**每次**，你都要手动设置`PYTHONPATH` **。**您必须在自述文件中的每个地方都用大写字母写下此警告。

这个鬼魂会永远缠着你。

![](img/1393d924af12a901431e68b1f9b9b5db.png)

PYTHONPATH 幽灵。照片由 [Syarafina Yusof](https://unsplash.com/@grimmwwald?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

此外，你**不能创建一个通用的脚本**来避免这一点，因为你不知道你的其他同事会把这些代码放在哪里，所以你**不知道指定的绝对路径**。
但是好吧，也许这是我的 DevOps 的一面说话，或者我的*什么是-这-请-自动化-一切*偏执。我承认。

还有另一个更糟糕的问题。也许您在配置文件中导出了那个定义，因为您不想在每次打开 shell 或 IDE 时设置它。然而，现在你正和几个不同的项目一起工作，并且定义开始冲突。没有分离。

*哎哟。*

我不是说完全不对*(或者我是？—咳嗽)*。
*它在简单的情况下肯定有效*，但是**难道就没有另一个普遍有效的解决方案吗？**也许有什么东西可以**自动化**并且**可以在任何地方运行**？

> 另外，你注意到上面的“附加”定义了吗？
> " **它告诉 Python 在哪里搜索*附加的*脚本和模块。"**
> 它来自 [Python 文档](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH)。
> 
> 我的代码怎么可能是一个 ***加法*** ？它是要运行的主要代码！
> 更不用说，就在那个页面上，还有一个关于 Python 替代实现的不同行为的[警告](https://docs.python.org/3/using/cmdline.html#command-line-and-environment)——尽管我从未注意到这个特殊案例中的区别。

## sys.path 的主幽灵

*“但是我可以自动完成这种配置！我可以在我的脚本中修改* `*sys.path*`

*但是……*为什么？**

*除了与之前的 Ghost 几乎相同的问题之外，**这个解决方案将应用程序代码与系统配置代码混合在一起**。*

*当某样东西闻起来很臭时，它通常是腐烂了。这个案例并没有什么不同。混合范围总是一个坏主意。*

*此外，你是**在运行时玩弄环境变量**。为什么要等这么久来设置您的代码？设置是*要做的第一件事*，而不是最后一件！它使您尽快意识到错误，从可能的原因列表中排除下列组件。*

*当您不得不分叉您的进程时，或者当您使用一个没有警告您就这么做的库时，会发生什么？是继承了这些环境变量吗？你每次都要担心这个问题吗？*

*![](img/166aa376872db9dc7b78fda306c80462.png)*

*让我们找出正确的解决方案。[基特·苏曼](https://unsplash.com/@cobblepot?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照*

# *显而易见的解决办法*

*如果你密切注意，你会看到它。
看你不得不**从其他库**导入函数和类的方式，我的意思是。除了无缝工作的事实之外，它们在任何地方都可以工作，没有任何变化。*

*那么，你为什么要使用不同的机制呢？*

*它们之所以有效，是因为这些包被放在`site-packages`文件夹下，默认情况下 Python 会检查这个文件夹。现在的问题是:到底是谁在把他们转移到那里？*

*答案很简单。**是你的** `pip install` **命令**(或等同)。*

*是的，我将把重点放在 pip 案例上，其中您也有一个**虚拟环境**。当然，康达恩维斯也是如此。
*这里不适合列举拥有虚拟环境的优势，对吗？**

# *安装您自己的代码*

*所以这个想法是将你的代码和你的其他包一起安装。这意味着你的代码也必须被构造成一个包。*

*不要绝望，这很容易！请记住两件事。*

## ***(1)你只需要把你的代码收集到至少一个文件夹里，里面有一个空的** `**__init__.py**` **文件**。*

*这些文件对于将它们的文件夹标记为 **Python 模块是必不可少的。**通过这种方式，它们被识别用于安装(并因此被导入)。它们是强制性的。*

*假设你的包裹是`justice`:*

```
*justice\
    ...
    innerfolder\
        **__init__.py**
        some_script.py
        ...
    ...
    **__init__.py**
    one_of_your_scripts.py
**setup.py***
```

*因此，每个文件夹都有一个`__init__.py`。*

## ***(2)还有一个必不可少的棋子:** `**setup.py**` **档。***

*默认情况下，`pip install <package>`命令**会搜索并使用`setup.py`文件**。它告诉了几件事:关于你的包的元数据，在哪里可以找到脚本和*(可选但建议)*有什么要求。*

```
*from setuptools import setup, find_packages# List of requirements
requirements = []  # This could be retrieved from requirements.txt# Package (minimal) configuration
setup(
    name="justice",
    version="1.0.0",
    description="End world injustices",
    packages=find_packages(),  # __init__.py folders search
    install_requires=requirements
)*
```

*在那里编写需求会让您的`pip install <package>`命令**也安装您的包依赖关系**。*是的，只需一声令下！*也不需要运行`pip install -r requirements.txt`。双重福利！*

> *这里有很多要说的，也是关于发行版的(source，wheel)。也许在下一篇文章里！*

*因此，现在您需要做的一切就是使用`pip install <path_to_setup.py_folder>`安装您的代码，或者在本例中使用`pip install .`*

*哇哦。一切都好吗？**就凭那个？***

*是的，差不多了。*

*因为，实际上，**你不能为你的代码**那样做。这个安装会将您的脚本复制到`site-packages`文件夹中，就这样。如果你的代码改变了怎么办？**正在开发**，所以这是很有可能的。**你每次都必须安装它。**一点都不顺手。*

## *这就是为什么你应该使用**这个单一的一行程序救世主:***

```
*pip install -e .*
```

*![](img/0afbbbac5b9813f02274f23aa93fd654.png)*

*这才是真正的绝招！照片由[阿齐兹·阿查基](https://unsplash.com/@acharki95?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄*

# *开发模式安装*

*`-e`选项告诉 pip 在“开发模式”下安装它。基本上，它把**只是一个对你的包的引用**放在`site-packages`文件夹中。所以你可以改变它，而不用担心安装了！*

***你只要一劳永逸地做到这一点，**即使你改变了代码。*

*如果你在虚拟环境中安装了你的包，**你可以从任何地方**导入你的代码，从任何 shell，只要你激活了那个虚拟环境。*

*例如:*

```
*from justice import one_of_your_scripts

one_of_your_scripts.a_function()*
```

*最重要是，**您可以在任何地方运行确切的安装命令。因为这也适用于你的同事。您不必担心安装的路径。*这意味着简单的自动化。****

***快速、干净、通用。***

***真正的解决方案应有的一切。***

# *资源*

*[[1] Python 文档](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH)*