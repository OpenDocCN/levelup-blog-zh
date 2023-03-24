# Python 命令行参数简易指南😎

> 原文：<https://levelup.gitconnected.com/the-easy-guide-to-python-command-line-arguments-96b4607baea1>

![](img/82583f0c8734deaa99a926fd8b0d8c2e.png)

见见你的新指挥官😎

> 想获得灵感？快来加入我的 [**超级行情快讯**](https://www.superquotes.co/?utm_source=mediumtech&utm_medium=web&utm_campaign=sharing) 。😎

Python 的优势之一是它具有做任何事情的能力。它的标准库提供了足够的特性来编写大量有用的脚本和工具。如果除此之外还需要什么，更多的功能就在眼前。

几乎每一种现代编程语言都有从命令行接受参数的能力。这是一个非常重要的特性，因为它允许来自用户的动态输入，无论他们是否编写了程序。

Python 自带了几个不同的库[允许你的 Python 代码从命令中获取用户输入，包括`sys.argv`、`getopt`和`argparse`。今天，`argparse`是迄今为止最好和最常用的。](https://www.tutorialspoint.com/python/python_command_line_arguments.htm)

[Python argparse 库](https://docs.python.org/3.3/library/argparse.html)作为 Python 3.2 标准库的一部分发布。在那个版本之后，由于它的流行，它被集成到 Python 2.7 和所有未来的 Python 版本中，很快成为处理命令行参数的黄金标准。它有几个可取的特性，使得在 Python 中使用命令行参数非常灵活，对我们的程序非常有用:

*   您可以为命令行参数设置帮助消息和文档
*   以清晰易读的方式设置参数的默认值
*   对于特定的命令行参数，有一定数量的选项
*   输入时自动类型转换
*   它支持单个参数有可变数量的参数
*   它可以自动应用一个动作或功能到你的输入，如你所指定的

记住所有这些关键特性后，让我们带您参观一下 Python 的`argparse`库。

[](https://gitconnected.com/resume-builder) [## 软件工程师简历生成器| gitconnected

### 免费打造不到 5 分钟的高质量软件工程简历。同步您的个人资料，我们会处理…

gitconnected.com](https://gitconnected.com/resume-builder) 

# 初始设置

首先，打开一个空白的 Python 脚本。argparse 库是 Python 内置的，所以我们不需要任何安装过程。下面的代码显示了如何为 argparse 创建初始设置。

上述代码有 3 个重要组成部分:

1.  导入`argparse`
2.  创建参数解析器(带描述)
3.  解析命令行参数

第 2 步是创建解析器对象，通过它我们可以添加命令行参数以及每个参数的一些选项。步骤 3 运行一个函数，该函数实际上从命令行中提取用户输入的参数。

美妙之处在于,`argparse`将通过这个简单的设置自动使用命令行。试着运行末尾带有`--help`的程序，看看`argparse`如何打印出我们对 argparse 教程*的描述！*

现在让我们添加一个名为“a”的命令行参数，如下面的代码所示。要将命令行参数传递给 Python 脚本，通过运行`python3 argparse_2.py --a=5`来执行它

注意我们是如何使用`.add_argument()`函数将“a”作为命令行参数传递的。为了访问我们从`a`得到的变量，我们使用`args.a`。

需要注意的一点是，如果我们不在命令行为`a`指定一个值，那么`args.a`将会是`None`。解决这个问题的一个方法是在参数解析器中为`a`指定一个默认值，如下所示。请注意，我们这次还添加了一个描述。

在这种情况下，如果我们不通过命令行为`a`传递一个值，那么它的值将默认为 1。通过为`help`变量添加一个字符串，我们还能够在应用`--help`时为每个变量打印出一个更健壮的描述:

```
usage: run.py [-h] [--a A]A tutorial of argparse!optional arguments:
  -h, --help  show this help message and exit
  --a A       This is the 'a' variable
```

# 命令行参数的常规用法

让我们用`argparse`来探究一些更有趣的选项。首先，我们可以指定每个变量的类型信息，这样就可以在输入时进行类型转换。

确保用户总是为特定参数传递值的一个好方法是使用`required`关键字。当设置为`True`时，强制用户输入该值，否则程序将抛出错误并停止。

如果我们为参数`--name`提供一个值，那么一切都好！如果我们不这样做，我们将得到如下错误消息:

```
usage: run.py [-h] [--a A] --name NAME
run.py: error: the following arguments are required: --name
```

我们还可以使用`choices`参数来限制特定命令行参数的*可能值*。当您的代码中有一组基于特定字符串执行特定操作的 if-else 语句时，这尤其有用。查看以下代码中的示例。

现在，如果我们输入选择列表中的任何值，代码都会正常运行，并接受我们的`education`参数。但是如果你给它一些列表中没有的东西，比如说数字`5`，那么你会得到一个类似下面的信息，告诉你从列表中选择一个东西！

```
usage: run.py [-h] [--a A] --education {highschool,college,university,other}
run.py: error: argument --education: invalid choice: '5' (choose from 'highschool', 'college', 'university', 'other')
```

# 高级用法和技巧

现在是时候用一些高级的 argparse 变得活泼一点了！

`action`参数允许我们指定一些我们希望参数解析器采取的动作。例如，我们可能想要一个参数，如果它存在或者是一个常量，那么它会自动设置为布尔值`True`。这两种情况都显示在下面的示例中。

在上面的代码中，我们基本上说的是，如果命令行参数`a`存在，那么`a`将是`True`，否则`a`将是`False`。类似地，如果命令行参数`b`存在，那么它应该被设置为值`10`，否则它将是`None`，因为没有默认值！

我们也可以建立所谓的*互斥组。*它定义了一组互斥的解析器命令行参数——也就是说，这些命令行参数不能同时传递。

以下面的代码为例。

`argparse`参数`a`和`b`被添加到同一个互斥组中。因此，Python 不允许你像这个`python3 argparse_8.py --a --b`一样同时发送`a`和`b`，你必须一次传递一个。当您希望确保用户不会同时传递任何冲突的变量时，这很有用，可以避免任何混乱或错误。

# 喜欢学习？

在推特上关注我，我会在这里发布所有最新最棒的人工智能、技术和科学！也在 LinkedIn 上与我联系！