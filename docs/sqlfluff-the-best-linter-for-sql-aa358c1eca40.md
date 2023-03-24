# 如何用 SQLFluff 格式化 SQL 代码

> 原文：<https://levelup.gitconnected.com/sqlfluff-the-best-linter-for-sql-aa358c1eca40>

## 为什么我相信它会彻底改变✨的生态系统

在过去的十年中，软件生态系统已经为自己配备了许多工具，以帮助开发人员和其他数据工程师在团队中工作。

例如，在 PHP 语言世界中，有“ [PHP 标准建议](https://www.php-fig.org/psr/)”(PSR)指出应该如何从视觉和架构的角度编写代码。

**编码风格指南**方便新团队成员的融入，减少审查代码时的精神疲劳。它们存在于大多数编程语言中，软件检测错误，有时甚至能够自动纠正代码。

![](img/d396fc976ac55f49895d26105843a8ae.png)

照片由[赛基兰·阿纳加尼](https://unsplash.com/@anagani_saikiran?utm_source=medium&utm_medium=referral)拍摄

然而，尽管是最古老和最常用的语言之一，SQL 仍然没有官方的最佳实践库，更没有“编码风格指南”

在这种背景下， [SQLFluff](https://docs.sqlfluff.com/en/stable/) 用软件打开大门，确保你的 SQL 代码满足其社区定义的规则(*目前主要来自 dbt 核心团队*)，我说它将成为生态系统中事实上的*标准是认真的！*

![](img/01db3106caf69e63a05acfbb8276d226.png)

图片取自文档网站:[https://www.sqlfluff.com/](https://www.sqlfluff.com/)

## 什么是棉绒？

linter 是一个根据一系列规则检查代码的工具。这可以是美学规则(分号应该在一行的末尾，还是在最后一个表达式的右边？)或者关于社区的最佳实践(如果可以的话，你最好使用一个 **WHERE** 而不是 **HAVING** for performance！).

一些 linters 可以检测错误，而另一些可以删除错误并修复代码。

> 例如，Python 中最流行的 linter 之一是 [flake8](https://flake8.pycqa.org/en/latest/) ，PHP 中最流行的 linter 是 [PHP-CS-Fixer](https://cs.symfony.com/) 。

## SQLFluff 的安装和发现

SQLFluff 是一个用 Python 编写的软件，最近发布了一个稳定版本，但其开发非常活跃(2022 年 8 月 31 日 1.3 版)。所以您可以从 pip 安装它:

```
pip install sqlfluff
```

要快速测试该工具，最好使用文档中提供的示例:

我向你保证这段代码是错误的🤡

使用" **lint"** 命令，该工具将显示所分析的 SQL 代码中的错误:

```
~\Projects\sqlfluff_article
❯ sqlfluff lint .\table_creation.sql --dialect mysql
== [table_creation.sql] FAIL
L:   1 | P:   1 | L010 | Keywords must be lower case.
L:   1 | P:   1 | L036 | Select targets should be on a new line unless there is
                       | only one select target.
L:   1 | P:  27 | L010 | Keywords must be lower case.
L:   1 | P:  41 | L009 | Files must end with a single trailing newline.
All Finished 📜 🎉!
```

每个错误对应于软件中定义的一个规则,您可以忽略该规则。您也可以创建自己的规则，但这将是下一篇文章的主题🤓

要修复错误，您可以尝试使用 **fix** 命令。

我**强烈建议你回读**结果，如果你正在修复长请求的代码，并确保一切正常。

对于上述类型的错误(空格或换行符)，该工具将完美地完成工作:

```
~\Projects\sqlfluff_article 
❯ sqlfluff fix .\test.sql --dialect mysql 
==== finding fixable violations ====
== [test.sql] FAIL
L:   1 | P:   3 | L003 | First line has unexpected indent
L:   1 | P:  11 | L039 | Unnecessary whitespace found.
L:   1 | P:  14 | L039 | Unnecessary whitespace found.
L:   1 | P:  26 | L009 | Files must end with a single trailing newline.
L:   1 | P:  27 | L001 | Unnecessary trailing whitespace.
==== fixing violations ====
5 fixable linting violations found
Are you sure you wish to attempt to fix these? [Y/n] ...
Attempting fixes...
Persisting Changes...
== [test.sql] PASS
Done. Please check your files to confirm.
All Finished 📜 🎉!
```

## SQLFluff 如何与 MySQL、大查询、雪花和 dbt 一起工作

SQL 不是一种统一的语言，现有的实现有几种:MySQL、Big Query 和 SnowFlake 等等。

数据分析师和工程师也倾向于使用模板引擎，比如 Jinja，它被 [dbt](https://docs.getdbt.com/) 用来开发这种旧编程语言的功能。

在这种特定情况下，您的 SQL 代码不仅包含 SQL，还混合了 SQL 和模板引擎运算符和函数:

如何“lint”这段代码？😟

这就是 SQLFluff 的两个基本概念的来源:方言和模板。

方言定义了所使用的 **SQL 实现**。

> 对于软件开发人员来说，它可能是 MySQL 或 PostgreSQL，而对于数据分析师或工程师来说，它可能是 BigQuery 或 T21。

模板引擎是数据函数在这个上下文中使用的一个概念，但它也会对 web 开发人员说话(想想 Jinja、Twig、handlebar……)。

据我所知，开发人员不使用模板引擎来编写 SQL，而是使用 ORM(对象关系映射)来将他们的数据库系统链接到他们应用程序中的业务对象。

然而，模板引擎有利于编写复杂的 SQL 查询或使代码更容易维护。

很容易为 Twig 或 Jinja 编写一个函数，它将利用编写它们的编程语言的强大功能，并利用这种功能来编写 SQL，甚至在某些情况下弥补 SQL 语言功能的不足。

在数据生态系统中，最好的模板引擎被称为 [dbt](https://docs.getdbt.com/) :它有许多特性和好处，来自一个非常丰富的宏和其他函数生态系统，用于分析和兼容许多流行的 SQL 方言。

为了更好地配置方言和模板引擎，我们可以创建一个名为。sqlfluff ":

在这个配置文件中，您还可以修改(删除、添加、配置)将应用于您的项目的规则。

## SQLFluff 是 CI/CD-Ready！

由于该工具在命令提示符下可用，因此进入您的 CI/CD 工作流非常合适！以下是运行 GitHub 操作的配置示例:

本文结束；感谢阅读😜

不要犹豫，在评论中与我分享你的实验结果吧！

如果你喜欢这篇文章，并希望在新文章发布时得到通知，请不要犹豫，关注[我](https://mickael-andrieu.medium.com/)！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级初创公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)