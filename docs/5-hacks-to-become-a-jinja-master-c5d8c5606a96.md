# 成为金贾大师的 5 招

> 原文：<https://levelup.gitconnected.com/5-hacks-to-become-a-jinja-master-c5d8c5606a96>

![](img/55cc1e18eec2be099fd342a0228f54ae.png)

曼努埃尔·科森蒂诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当我第一次听说 Jinja 时，我对能够在我的 SQL 查询和 HTML 文件中注入一些 Python 逻辑的想法感到非常兴奋。我终于要在字段列表中循环了！

但是这种纯粹的喜悦并没有持续很长时间，很快就导致了沮丧，因为我意识到很多 Python 内置函数在 Jinja 中并不工作。因此，经过一点研究和大量测试，这里有一些我在编写 Jinja 语法时使用的最有用的技巧！

# 黑客#1:用在线解析器测试

![](img/e6d3f094cd529cf81236c2c9f5e86e6a.png)

作者截图

隔离一段 Jinja 语法来找出问题所在通常很方便；你可以创建一个 Jinja 环境和模板，但是使用像[这样的在线解析器可能会更快！](http://jinja.quantprogramming.com/)

# 黑客#2:使用循环变量

```
{% set names=["peter", "michelle", "richard"] %}
{%- for name in names -%}
{{loop.index}}.{{name}}
{% endfor %}
```

有时候，你想要迭代条目和索引，就像 python *enumerate* 函数一样。Jinja 给了我们一些循环变量，比如返回循环长度的 *loop.length* 或者返回 True 的 *loop.last* 如果这是列表的最后一项。

```
1.peter
2.michelle
3.richard
```

# 黑客#3:创建宏

```
{%- macro greet(name)-%}
Hello {{name}}!
{%- endmacro -%}
{{greet("Javier")}}
```

如果你发现自己一遍又一遍地重复使用相同的 Jinja 语法，你应该创建一些宏。宏是你可以在模板中调用的 Jinja 函数。它们可以接受参数并返回一个字符串，甚至是本地 Python 对象，如列表。

# 黑客#4 拥抱设置块

嵌套 Jinja 时的转义括号不是小菜一碟。谢天谢地，设置块使它更容易！

```
{%- set first_name="Marie" -%}
{%- set last_name="Truong" -%}
{%- set greeting -%}
Hello {{first_name}} {{last_name}}!
{%- endset -%}
{{greeting}}
```

使用 set 块，您可以像在语句之外一样编写 Jinja 语法。

# 黑客#5 学习间距

所有那些烦人的白线…一劳永逸，让我们学习间距。

关键是 Jinja 不添加空格，*您*添加。您在代码中添加了这些空格，但不希望它们出现在编译后的文件中。

![](img/b0f9f5c7fe0f826384160981c4b837ea.png)

作者截图

这里，您跳过了开始语句后的一行。如果您只跳过了块后的一行，您将得到以下结果:

![](img/be99aec23e086b2ff8b9506347baec9e.png)

作者截图

如果你想让所有的块都在同一行，你可以把所有的东西都放在同一行。

![](img/c50adac7fceac9181fda8fcbdd38639f.png)

作者截图

可读性不是很好，是吗？幸运的是，有一种比从代码中删除空格更好的方法。

在 beginning 语句的结束%之前添加减号会删除块开头的白线。

![](img/e672267cc26988cef9b646e751054feb.png)

作者截图

在 ending 语句的开始%后面添加一个减号可以删除块末尾的白线。

![](img/d373bbfc0b91526fa4a319f2e30d7110.png)

作者截图

我希望这些提示对你的下一个模板有所帮助。如果你想了解更多关于 Jinja 的信息，你可以查看他们的文档。

感谢您的阅读！