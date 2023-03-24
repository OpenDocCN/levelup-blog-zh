# 如何学习任何语言的基础知识

> 原文：<https://levelup.gitconnected.com/how-to-learn-the-basics-of-any-language-5b917aee4e43>

## 逐步指南掌握任何编程语言的基础

![](img/432fedfc0f2976a3f9ef01c8f5553506.png)

由 [Unsplash](https://unsplash.com/s/photos/abc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Element5 数码](https://unsplash.com/@element5digital?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

学习编码很有趣，但是通读教科书的所有章节并不容易！因此，这里有一个简单的策略，我遵循它来学习我所学的几乎每一种语言的基本语法，无论是 Python、Java、C++、PHP、C#、Javascript 还是任何其他语言，只是为了一个简单的计算器！

## 先决条件

***注:*** 本文是学习 ***编程*** ***语言*** 而已！这不是针对框架和库的。

要理解这一点，你必须熟悉编程的基本原理和基础。理论一定要先搞懂！

# 熟悉基础知识

首先，让我们明确需求。我们需要一个简单的计算器，接受 2 个整数和 1 个算术运算符作为单独的输入。我们最终输出算术表达式的结果。

通过以简单的形式完成这些步骤，您应该已经了解了该语言的这些基本概念:

*   在 IDE 上初始化项目
*   简单输入和输出
*   if 条件和 case 语句

以下是一些你可以改进的地方和我见过的许多初学者犯的一些错误:

*   不要马上写代码，但是，永远记住使用算法或流程图。它有助于您理解编码时的数据流
*   不要写多个 print 语句来留出空行，可以使用\n 来换一个新行，使用\t 来留出一个制表符的空格。最好将打印语句值保留为常量，并在编码时使用它们，这样，如果将来有任何更改，将只有一个地方进行这些更改。例如，欢迎消息:

```
# Language: PHP<?php# Instead of printing this 
echo "----Welcome Message----";# We follow the Best Practices
$welcome_Message = "----Welcome Message----";
echo $welcome_Message;?>
```

*   尝试 if 条件和 case 语句。不要因为试图理解其中一个概念而偷懒，因为你将来需要理解这两个概念。

# 更进一步

现在让我们对代码进行改进。我们将把代码分成更小的函数段。这个过程叫做*模块化*。所以首先，我们将首先划分算术运算。我们将有 04 个简单的函数；加减乘除。

主函数是获取单个输入，调用各自的函数并产生输出的地方。

现在你已经运行了，确保你的评论。这是一个非常重要的最佳实践，因为它指导你自己和任何阅读你的代码的人你做了什么。一个很好的例子是命名您的函数:

```
// Language: C++// Addition function
double addition(a,b) {
    return a + b;
}// Subtraction function 
double subtraction(a,b) { 
    return a - b;
}// Multiplicaiton function 
double multiplication(a,b) {
    return a * b;
}// Division function 
double division(a,b) {
    return a / b;
}
```

我们可以通过将获得输入的代码行移动到另一个函数来进一步简化代码，可以称之为 GetInputs()。

```
# Language: Python# Get Input function
def getInput():
    num1 = int(input("Enter first number: "))
    num2 = int(input("Enter second number: "))
    operator = input("Enter first number: ")
    return num1, num2, operator
```

此时，您应该熟悉编写函数和模块化代码。除此之外，你将学习如何用这种语言编写代码，比如注释和组织函数。

# 获得高级 I

现在我们有了一个功能齐全的简单计算器，下面是我们可以做出的改进，让你像程序员一样思考！

如果为输入输入空值或 null 值，会发生什么情况？那么，您最终会得到代码崩溃或一些错误消息！因此，让我们做一些异常处理！您可以使用 try-catch 块来处理这些错误，并在屏幕上显示错误消息。

这个过程叫做*验证*。这是编程时非常重要的概念。一些初学者在让输入进入系统后对其进行验证。然而，执行验证的最佳实践是在获取数据本身的输入时。

```
// Language: C#// Validate Input Function
bool ValidateInput (num1, num2, opp){
    bool errorFlag = false;
    if (num1 == "" || num2 == "" || opp == "") {
        errorFlag = true;
    }
    if (opp != '+' || opp != '-' ||opp != '*' || opp != '/') {
        errorFlag = true;
    }
    return errorFlag;
}
```

是时候让我们更上一层楼了！我们现在将修改 GetInputs()函数来接受一个数据数组。用户将编写一个由 2 个数字和 1 个算术运算符组成的有效数学表达式。这些输入将存储在一个数组中，然后通过算术运算符进行处理。

此时，您必须熟悉输入验证、try-catch 块和数组，它们是您所学的许多编程语言中的一个重要特性。

# 获得高级 II

现在我们已经完成了大部分的基本概念，剩下的就是你在编程时遇到的最重要和最有用的东西了。

文件写入是一个重要的功能。现在让我们获取一个输入和输出列表，并将它们保存在一个文本文件中。为此，我们可以使用 2D 数组、并行数组或字典，无论这种语言支持什么。

```
// Language: Java// Write to File Function
public class WriteToFile {  
    public static void main(String args[]){    
        try{    
            FileWriter fw = new FileWriter("C:\\testFile.txt");    
            fw.write("Calc File");    
            fw.close();    
        }catch(Exception e){
            System.out.println(e);
        }    
        System.out.println("Success...");    
     }    
}
```

此时，我们几乎已经完成了编程语言的大部分基础知识，并且对语法更加熟悉。简单来说:

*   启动项目
*   输入和输出
*   职能和程序
*   数据类型
*   评论
*   条件语句和迭代
*   确认
*   数组和字典

最重要的是，你已经覆盖并实现了编程的 ***最佳实践*** ！

# 前方的路

从现在开始，尝试开发更多更高级的概念。一个好的发展是使用 ***链表*** 来接受大范围的值作为输入。这可能是一个带有括号和多个运算的长算术表达式。例如: ***(9 x -2 ) -10 + 15 / 100***

因此，我们将涵盖数组搜索、迭代和链表等概念。此外，您可以更深入地了解 OOP！

> 你不能像看《网飞》一样“狂吃”代码！

当你的代码工作时，稍微休息一下，然后*欣赏一下*自己！慢慢来，一口一口地享受。 ***关键*** 是要有创造力，享受你的编码。

# 结论

这是我学习大多数编程语言时遵循的一个非常简单的策略。这些功能和创意会变得复杂。作为软件工程的学生，学习是一种常规，我相信这篇文章已经帮助你实现了你所寻求的。

当然，如果你有更好的学习编程语言的方法或策略，请随意在回复中留下，因为这将使读者受益。祝编码愉快！

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)