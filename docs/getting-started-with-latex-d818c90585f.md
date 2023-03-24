# 乳胶入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-latex-d818c90585f>

## 学习 LATEX 的一步一步的完整指南

## 学习乳胶从基础到高级水平

![](img/6d49e0144e7c80747787c6345c877355.png)

图片来源:[https://en.wikipedia.org/wiki/LaTeX](https://en.wikipedia.org/wiki/LaTeX)

这是 LaTex 入门系列文章的第 2 部分，解释了如何在 LaTeX 中创建表格和列表。

本系列文章的第 1 部分介绍了基本的软件安装以及如何用 LaTeX 编写一个简单的文档。

# 创建表格

在`LaTeX`中，我们可以使用`table`、`tabular`，或者两种环境的组合来创建表格。`table`环境提供了额外的功能，比如表格的定位、标题、标签和引用。然而，实际的内容在`tabular`环境中。

我们将开始创建最简单的表，然后根据我们的选择逐步开发它。可以使用`tabular`环境创建一个简单的表格，这是在 LaTeX 中创建表格的默认方法。下面的代码块创建了一个非常简单的表格，有三列，文本居中。

```
\begin{center}
  \begin{tabular}{c c c}
    aa & bb & cc \\
    11 & 22 & 33
  \end{tabular}
\end{center}
```

`{ c c c }`指定列数和列内文本的位置。我们也可以使用`l`和`r`分别将文本左右对齐。`&`用于标记列，`\\`用于插入新行。

让我们在表格中添加一些样式来保持信息的组织性。我们将使用`{ |c|c|c| }`向列添加垂直线，以将它们彼此分开。此外，我们将使用`\hline`在表格的顶部和底部添加一条水平线。

此时的代码将如下所示:

```
\begin{center}
  \begin{tabular}{ |c|c|c| }
    \hline
    aa & bb & cc \\
    \hline
    11 & 22 & 33 \\
    \hline
  \end{tabular}
\end{center}
```

# 表格的定位、标题、标签和引用

为了添加额外的特性，比如定位、标题、标签和对表的引用，我们将使用`table`环境作为`tabular`环境的包装器。

为了定位表格，我们需要将表格放在`table`浮动环境中。

```
\begin{table}[h!]
  \centering
  \begin{tabular}{ |c|c|c| }
    \hline
    aa & bb & cc \\
    \hline
    11 & 22 & 33 \\
    \hline
  \end{tabular}
\end{table}
```

`[h!]`将在此放置表格，覆盖默认 LaTeX 行为。你可以在这里找到其他说明符[的细节](https://github.com/m-yahya/latex-tutorial/tree/02-insert-images#using-the-figure-environment)。

类似地，我们可以在同一个`table`环境中分别使用`\caption{}`和`label{}`命令添加标题和标签。然后`\ref`和`\label`可以用来引用文档中的表格。

```
\begin{table}[h!]
    \centering
    \caption{A Simple Table with Caption and Label}
    \label{table1}
    \begin{tabular}{ |c|c|c| }
        \hline
        aa & bb & cc \\
        \hline
        11 & 22 & 33 \\
        \hline
    \end{tabular}
\end{table}
```

最后，通过在`\begin{document}`之后使用`\listoftables`创建一个表列表。

# 创建列表

在本节中，我们将了解如何创建不同类型的列表以及如何自定义列表样式。

一般来说，我们可以在`LaTeX`中创建以下不同类型的列表:

*   无序列表
*   有序列表
*   嵌套列表

# 无序列表

在 LaTeX 中，使用`itemize`环境创建一个无序列表，然后使用`\item`将每个条目放入环境中。

```
\begin{itemize}
    \item The un-numbered item in the list
    \item Another un-numbered item in the list
    \item One more un-numbered item in the list
\end{itemize}
```

# 有序列表

可以使用`enumerate`环境创建一个有序列表，然后将条目放入环境中。

```
\begin{enumerate}
    \item The first item in the list
    \item The second item in the list
    \item The third item in the list
\end{enumerate}
```

# 嵌套列表

在`LaTex`中，一个列表可以包含另一个列表作为其项目。换句话说，我们可以在 LaTeX 中创建嵌套列表。以下代码块显示了一个嵌套列表的示例:

```
\begin{enumerate}
    \item The first item in the list
    \item The second item in the list
          \begin{enumerate}
            \item sub-item a
            \item sub-item b
            \item sub-item c
          \end{enumerate}
    \item The third item in the list
\end{enumerate}
```

# 自定义列表样式

**无序列表**

默认情况下，LaTeX 在未编号的列表中使用黑点作为项目符号。可以通过以下方式将此默认行为更改为粗体破折号、破折号和星号:

```
\begin{itemize}
    \item[--] Dash item
    \item[$-$] Bold Dash item
    \item[$\ast$] Asterisk item
\end{itemize}
```

**订购单**

使用下面给出的`enumitem`包，可将有序列表的编号样式更改为`Roman`、`Arabic`和`Alphabetical`。

```
\documentclass[12pt, letter]{article}
\usepackage{enumitem}
% other packages\begin{document} \begin{enumerate}[label=\roman*]
    \item The first item with Roman numbers
    \item The second item with Roman numbers
    \item The third item with Roman numbers
\end{enumerate}\begin{enumerate}[label=(\arabic*)]
    \item The first item with Arabic numbers
    \item The second item with Arabic numbers
    \item The third item with Arabic numbers
\end{enumerate}\begin{enumerate}[label=(\alph*)]
    \item The first item with Alphabetical numbers
    \item The second item with Alphabetical numbers
    \item The third item with Alphabetical numbers
\end{enumerate}\end{document}
```

从这个 [GitHub](https://github.com/m-yahya/latex-tutorial/tree/03-create-tables-lists) repo 中下载示例项目文件，在 Texmaker 中打开`main.tex`文件，点击*运行*按钮，瞧🎉。

在下一部分，我们将学习如何在`LaTeX`中创建一个书目。

# 继续…

# 资源

[https://www.latex-project.org/](https://www.latex-project.org/)

[https://texfaq.org/](https://texfaq.org/)