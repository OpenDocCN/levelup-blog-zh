# 使用 isort 自动对 Python 模块导入进行排序

> 原文：<https://levelup.gitconnected.com/use-isort-to-sort-your-python-module-imports-automatically-40918f3e2a8b>

## 让我们以统一的方式对模块进行分类

当你的 Python 程序变得更大时，你会导入越来越多的模块。如果你不把它们整理好，它们看起来会很乱。但是，如果您手动格式化它们，将会非常麻烦。更糟糕的是，如果你在一个团队中工作，每个人可能都有他/她自己的模块排序习惯。因此，我们最终会在同一个存储库中拥有不同的格式。

如果你有这个问题，那么你来对地方了。 [***isort***](https://github.com/PyCQA/isort) 模块将为您解决这个问题！

> isort 是一个 Python 库，可以按字母顺序对导入进行排序，并自动按类型分成几个部分。

![](img/834e7e8cb9191d33dddba836a23bd9df.png)

图片来自 [Pixabay](https://pixabay.com/photos/tea-teabag-teas-drink-herbal-tea-1252397/)

在我们使用 *isort* 格式化我们的代码之前，让我们先安装这个库。由于 *isort* 是一个非常基本且通用的工具，您可以将它安装在您系统的库中。但是，如果您无法将其安装在系统库中，或者想要安装不同版本的*或*，您可以将其安装在[虚拟环境](https://lynn-kwong.medium.com/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)中。

```
$ **pip install isort**
```

在本帖中，我们将使用[这个回购](https://github.com/lynnkwong/isort-demo)进行演示。这是一个以不同方式运行 Scrapy 蜘蛛的实际例子。如果你对代码感兴趣，请看看[这个帖子](https://lynn-kwong.medium.com/how-to-run-scrapy-spiders-in-your-program-7db56792c1f7)。

该回购协议的结构如下:

```
├── pre-commit
├── **pyproject.toml**
├── README.md
├── scraping_proj
│   ├── __init__.py
│   ├── items.py
│   ├── middlewares.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders
│       ├── authors.py
│       ├── __init__.py
│       └── quotes.py
├── scraping_script_with_api_and_signals.py
├── scraping_script_with_api.py
├── scraping_script_with_subprocess.py
└── scrapy.cfg
```

特别的，我们将在`pyproject.toml`中有 *isort* 的配置，这是一个统一的 Python 项目设置文件，即将介绍。

这个 repo 中每个文件中导入的模块以非标准的方式按字母顺序排序。例如:

模块的顺序不符合 [PEP8](https://peps.python.org/pep-0008/#imports) 。对于 PEP8，模块应按以下顺序导入:

> 1.标准库导入。
> 
> 2.相关第三方进口。
> 
> 3.特定于本地应用程序/库的导入。

然而，PEP8 没有定义同一组中的模块应该如何排序。此外，对于来自同一个模块的导入应该如何分组也没有定义。

所有这些基本细节都可以由 *isort* 库自动处理。我们可以使用*或*单独格式化一个文件或递归格式化一个文件夹。让我们用它来格式化整个项目文件夹:

```
$ **isort .**
```

圆点表示当前目录。当前文件夹和子文件夹中的所有 Python 文件都将被递归格式化。

上述模块应按如下方式分类:

现在第三方图书馆被放在本地图书馆之前。此外，每组中的模块按照模块路径的字母顺序进行排序。并且从相同模块导入的函数/类被分组在一起，也按字母顺序排序。

如果您对默认格式结果不满意，可以指定一些选项来自定义格式。*或*最常用的选项是`--line-lenghth`和`--src`，它们指定了导入行的最大长度和本地包的路径，即源路径。如果*或*不能区分第三方库和本地库，您必须指定`--src`选项并在那里添加您的本地包。

让我们为上面的*或*命令设置线路长度和源路径:

```
$ **isort --line-length 79 --src scraping_proj .**
```

模块应该按照默认方式进行排序。

我们之前提到过，有一个“神奇”的文件`pyproject.toml`，可以存储统一的 Python 项目设置。您可以将不同工具的设置放在该文件中，如[](https://github.com/psf/black/blob/main/pyproject.toml)*[*setup tools*](https://lynn-kwong.medium.com/how-to-configure-build-and-deploy-your-python-projects-to-pypi-dac40803fdf)*isort*等。*

*让我们将 isort 的设置放在`pyproject.toml`中:*

*如果您想了解*或*的其他配置选项，请查看此处的[。](https://pycqa.github.io/isort/docs/configuration/options.html)*

*在我们将设置放入`pyproject.toml`后，我们不再需要在命令行上指定*或*的参数。如果你不相信，你可以把`line_length`选项改成一个小数字，再次运行*或*命令，你就会看到区别。*

*现在我们知道如何使用 *isort* 在命令行上显式格式化代码的模块导入。如果我们能够通过*或*实施一些预提交策略，将会更加有用和方便。有了这样的策略，如果开发人员的代码不能通过*或*的静态代码检查，他/她的代码将不被允许提交。*

*让我们为*或*创建一个简单的预提交钩子。关于 git 钩子的基础知识，请查看[这篇文章](https://lynn-kwong.medium.com/use-pre-commit-commit-msg-and-pre-push-git-hooks-to-fix-your-python-code-asap-77d80d3ce412)。*

*要启用 Git 挂钩，我们必须将相应的脚本文件放在`.git/hook`目录中。脚本文件必须有一个预定义的名称，没有任何扩展名，并且是可执行的。例如，脚本文件可以是`pre-commit`、`commit-msg`或`pre-push`。*

*我们将创建的脚本名为`pre-commit`，内容如下:*

*我们使用 [ANSI 转义序列](https://gist.github.com/fnky/458719343aabd01cfb17a3a4f7296797)根据消息是错误、警告还是信息，用不同的颜色回显消息，这使得钩子的信息更容易识别。*

*此外，*或*的`--check`选项只会检查模块的格式，不会就地更改。当没有任何改变时， *isort* 命令返回 0，当文件被重新格式化时返回 1。*

*请注意，`pre-commit`脚本必须是可执行的，否则它将无法工作:*

```
*$ **chmod +x pre-commit**$ **cp pre-commit .git/hooks***
```

*现在，如果您尝试提交(您不需要进行更改并将它们添加到[暂存区](https://lynn-kwong.medium.com/understand-different-git-states-and-the-corresponding-file-states-fc62348e81d7)来触发预提交挂钩)，您将看到*或*的预提交策略失败:*

*现在让我们用*或*格式化代码并再次提交。这一次，您应该看到策略成功通过:*

```
*$ isort .$ git commit*
```

*作为手动创建的`pre-commit`脚本的替代方案，我们可以使用 [*预提交*](https://pre-commit.com/) 实用程序(与预提交 Git 挂钩同名)以更标准的方式管理预提交挂钩。我们可以在同一个配置文件中设置多个 pre-commit 钩子，即`.pre-commit-config.yaml`，并逐个运行。现在有很多 linters 提供预提交钩子，我们可以直接使用它们。 [*isort*](https://pycqa.github.io/isort/docs/configuration/pre-commit.html) 就是其中之一。要了解更多关于*预提交*实用程序的信息，请查看[官方页面](https://pre-commit.com/)。*

*如果我们在这里使用*预提交*实用程序来管理预提交 Git 挂钩，我们不需要在本地安装*或*，而是需要安装*预提交*:*

```
*$ **pip install pre-commit***
```

*然后我们需要创建另一个“神奇的”文件`.pre-commit-config.yaml`,并将 Git 预提交钩子的配置放在那里。对于*或*，配置为:*

*   *`repo` —包含相应预提交钩子的源代码的 GitHub repo。*
*   *`rev`—`git clone`的修订或标签，指定将要安装的代码版本。*
*   *`hooks` —指定挂钩映射列表。挂钩映射配置将使用哪个挂钩。钩子的默认配置将从相应的 GitHub 库中读取。如果你去*或*的 GitHub 库，你会看到一个名为`[.pre-commit-hooks.yaml](https://github.com/PyCQA/isort/blob/main/.pre-commit-hooks.yaml)`的文件，它定义了*或*钩子的默认配置。*
*   *`hooks.id` —GitHub 库的`.pre-commit-hooks.yaml`中定义的钩子的 id。在同一个`.pre-commit-hooks.yaml`中可以定义多个钩子，我们需要指定使用哪一个。*
*   *`hooks.name` —钩子执行期间将显示的钩子名称。*

*注意，即使我们使用带有预定义钩子的*预提交*实用程序，仍然可以从 `pyproject.toml`中读取*或*的参数。*

*既然已经创建了`.pre-commit-hooks.yaml`，并且在其中定义了钩子。我们可以使用`install`选项运行*预提交*，这将在`.git/hooks`文件夹中创建一个新的`pre-commit`脚本:*

```
*$ **pre-commit install**
pre-commit installed at .git/hooks/pre-commit*
```

*请注意，上面创建的旧的`pre-commit`脚本将被重命名为`pre-commit.legacy`，当我们提交一些东西时，它仍然会被使用。如果你不想要了，你可以删除它。*

*此外，应该注意的是，由*预提交*实用程序创建的钩子将只检查被修改的文件。当第一次使用`--all-files`选项安装时，我们可以针对所有文件运行一个钩子:*

```
*$ **pre-commit run --all-files***
```

*然后，您可以添加所有格式化的文件，并为其创建一个提交。稍后当您修改任何 Python 文件时，由*预提交*实用程序创建的预提交挂钩将只针对被修改的文件运行。例如:*

*使用预提交钩子的两种方式各有利弊，即手动编写`pre-commit`脚本和使用*预提交*实用程序。通过手动编写的`pre-commit`脚本，我们可以拥有更加灵活的策略。如上所示，我们可以为警报消息显示不同的颜色。然而，当挂钩需要在不同的项目之间共享时，使用*预提交*实用程序会更好。此外，它还可以方便地使用用不同语言编写的第三方钩子。可以选择最适合自己实际使用案例的方式。*

*本文通过实际例子介绍了用于自动 Python 模块排序的便捷工具 *isort* 。现在你可以开始在工作中使用它了。它将从手动修复 Python 模块导入的琐碎重复工作中为您节省大量时间。更重要的是，这将使代码在您的团队中更加一致。*

*相关文章:*

*   *[使用预提交、提交消息和预推送 git 挂钩尽快修复你的 Python 代码。](https://lynn-kwong.medium.com/use-pre-commit-commit-msg-and-pre-push-git-hooks-to-fix-your-python-code-asap-77d80d3ce412?source=your_stories_page----------------------------------------)*
*   *使用 black、mypy 和 pylint 让你的 Python 代码更加专业。*