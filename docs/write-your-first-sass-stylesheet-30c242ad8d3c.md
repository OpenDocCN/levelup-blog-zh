# 编写您的第一个 SASS 样式表

> 原文：<https://levelup.gitconnected.com/write-your-first-sass-stylesheet-30c242ad8d3c>

![](img/e5ce73696194e42a2f6d4023f9c79e8d.png)

# 什么是萨斯？

Sass 或语法上令人敬畏的样式表是一种预处理器脚本语言，它被解释或编译成级联样式表。SassScript 本身就是脚本语言。Sass 由两种语法组成。最初的语法称为“缩进语法”，使用类似于 Haml 的语法。

# 为什么要顶嘴？

使用 Sass，您将能够编写干净的代码，还可以防止 CSS 代码的重复。维护 CSS 代码也容易多了。

# Sass 语法

Sass 支持两种语法。

*   SCSS(时髦的 CSS):它使用文件扩展名。scss。这是最简单的语法，也是最受欢迎的。
*   缩进语法:它使用文件扩展名。萨斯。它使用缩进而不是花括号，不像 SCSS。

我将在本文中使用 SCSS 语法。

# 编译 SASS

你的浏览器不能直接解释 Sass。在浏览器执行它之前，必须先将其转换为 CSS。编译 Sass 代码就是解释代码让浏览器理解，这就是 CSS。

# 我们如何编译？

我们需要先安装 sass，您的计算机上必须安装 node 和 npm。

`npm install -g sass`

如果需要帮助安装，可以参考[官方 Sass 网站](https://sass-lang.com/)。

我们现在可以编译 sass 文件了。

假设我们创建了一个名为`styles.scss`的 Sass 文件，我们想要编译它。转到文件所在的目录，在终端中运行以下命令

`sass styles.scss styles.css`

sass 命令有两个参数，第一个参数是 sass 文件的输入，第二个参数是编译后的 CSS 文件的保存位置。这里，CSS 文件将保存在与 sass 文件相同的目录中。

您还可以使用`--watch`标志查看单个文件。监视标志监视您的 sass 文件的更改，并在每次保存 sass 文件时重新编译。您不必每次都手动运行该命令。

`sass — watch styles.scss styles.css`

# 在萨斯筑巢

我们首先要看的 sass 的第一个特性是嵌套。Sass 允许您将 CSS 选择器嵌套在另一个选择器中。例如

```
.parent { margin: 30px 10px; .child { background-color: green; } .another-child { background-color: violet; }}
```

上面的 SCSS 代码在编译时会产生下面的 CSS 代码

```
.parent { margin: 30px 10px;}.parent .child { background-color: green;}.parent .another-child { background-color: violet;}
```

您还可以嵌套属性。让我们看看怎么做。

```
.box { padding : { top: 10px; bottom: 20px; right: 30px; left: 40px; }}
```

上面的 SCSS 代码将编译成下面的 CSS 代码

```
.box { padding-top: 10px; padding-bottom: 20px; padding-right: 30px; padding-left: 40px;}
```

请注意:在声明填充的属性名之后的冒号符号。每当您想要嵌套 CSS 属性时，您可以在属性名称后附加一个冒号:符号。

# 变量

Sass 中的变量就像任何编程语言中的变量一样。变量用于存储数据，以便可以重复使用。在 Sass 中，$符号用于定义变量。

```
$bg-color: red;$text-color: #fff;$width: 100%; .box { background-color: $bg-color; color: $text-color; width: 100%;}
```

上面的 SCSS 代码将产生下面的 CSS 代码

```
.box { background-color: red; color: #fff; width: 100%;}
```

Sass 还支持许多其他特性，但是嵌套和变量应该可以让您启动并运行。