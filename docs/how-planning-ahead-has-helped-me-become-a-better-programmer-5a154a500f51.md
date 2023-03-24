# “提前计划”如何帮助我成为一名更好的程序员

> 原文：<https://levelup.gitconnected.com/how-planning-ahead-has-helped-me-become-a-better-programmer-5a154a500f51>

## 如果你想让编程变得更容易，提前制定一个计划。

![](img/38118901fae4bab3a2cb4424b287feb8.png)

我以前从未使用过的 seudocode 编程过程(PPP)是我最近学到的，并且发现它非常容易理解。PPP 通过减少编码时必须进行的思考量，使您能够“提前计划”。我发现提前规划一个项目会使编码变得更简单，并产生更好的代码。通过提前制定计划，你变得积极主动，可以预见到前进道路上的许多潜在障碍。

> 在开始编码之前，我认为最好有一个鸟瞰图。

我曾经相信一切都在我的头脑中，因此我总是发现直接进入并开始编码是很诱人的。但在我了解 PPP 之前，这并不是一个好主意。

史蒂夫·麦康奈尔在《代码全集》中将 PPP 描述为

术语“伪代码”是指一种非正式的、类似英语的符号，用于描述算法、例程、类或程序将如何工作。伪代码编程过程定义了一种使用伪代码来简化例程内代码创建的特定方法。

# 你如何使用它？

你说得对，没有标准的伪代码语法，但有一些有效使用它的指导原则。

1.  使用简单的语言来陈述。
2.  伪代码应该是独立于语言的；不要使用编程语法；相反，保持简单。
3.  如有必要，将 If 和 ELSE 等键盘命令大写。
4.  一定要适当缩进。
5.  确保包含详细的标题注释。
6.  最后，将代码放在每个伪代码语句之后。

让我们看一个简单的例子。

```
**This routine returns a success or an error message depending on an Email address that is given by the caller routine. If the email is valid, it returns a success message; otherwise, it returns an error message.**# Derive an email regular expression to test the Email address# IF the email does not adhere to the regex rule. 
    # Keep a record of the email and error message by logging 
    # Return the message "Invalid Email" # ELSE, if the above condition is false the email address matches the regex, determine to see if the database already contains a similar email address. # IF it already does, 
    # Keep a record of the email and error message by logging 
    # Return "Email already exists!" error message# IF the above criteria are not met, 
    # Log the email and success message to keep a record 
    # Return the success message to the caller
```

# 最后的想法

PPP 帮助你将一个大问题分解成可管理的部分。这是一个迭代的过程，你可以检查伪代码语句并很容易地进行修改，因为修改语句比修改代码本身更容易。

> 在蓝图上改一条线比拆墙容易

您可以将伪代码语句转换成代码注释，这也有利于代码审查人员。这使得开发人员在凌晨 3 点调试产品中的问题变得很容易

# **灵感来自**

史蒂夫·麦康奈尔完成代码 2