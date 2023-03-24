# 用 Java 编写玩具图书编目应用程序

> 原文：<https://levelup.gitconnected.com/programming-a-toy-book-cataloguing-application-in-java-25cd033436a0>

ava 是一种流行的编程语言，在 IT 领域需求量很大。我正在学习 Java SE 8 助理认证，我觉得学习这种语言的最好方法是开始用它编程。*Objects First With Java*([Barnes&kling 2009](https://www.bluej.org/objects-first/))是一本旨在教授 Java 编程基础的教科书，我将讨论我对一个练习的解决方案，该练习涉及在该语言的[Java 8 版本](https://www.oracle.com/java/technologies/java8.html)中编写一个玩具图书编目桌面应用程序。

![](img/469e7150f2bd9ee2b88e77c3c0b9bd69.png)

图片来源:[野生 Kratts 维基(2021 年 11 月 17 日修订)](https://wildkratts.fandom.com/wiki/Blue_Heron_Power?oldid=78815)

# 目录一览

1.  练习写作
2.  粗略测试
3.  最后的想法
4.  参考

# 练习记录

我想指出的是，我是从第一人称学生的角度而不是从教程的角度来写这篇文章的。对于对编写 Java 类的类似教程的方法感兴趣的读者，我推荐先阅读 Java 教科书中的*对象([Barnes&kling 2009，第 2 章](https://www.bluej.org/objects-first/))，然后阅读本文。*

教材([第 53 页](https://www.bluej.org/objects-first/))提供了以下入门代码作为学生练习的基础:

```
/**
 * Starter code to Chapter 2 of Object First with Java (Fourth Ed)
 * Modified by A.S. "Aleksey" Ahmann <alexander.ahmann@outlook.com>
 *
 * A class that maintains information on a book.
 * This might form part of a larger application such
 * as a library system, for instance.
 *
 * @author David Barnes, Michael Kolling and A. S. "Aleksey" Ahmann
 * @version 1 Dec., 2022
 *
 * Starter code adapted from Barnes & Kolling (2009, p. 53)
 */

public class Book {

    // The fields
    private string author;
    private string title;

    /**
     * Set the author and title fields when this object
     * is constructed.
     */
    public Book(String bookAuthor, String bookTitle) {
        author = bookAuthor;
        title = bookTitle;
    }

    // Add the methods here (my code) ...

}
```

然后，这本书向读者提出挑战，要求他们解决大量的练习，逐步将起始代码开发成一个完全开发的(玩具)应用程序，可以用 Java 类和方法跟踪书籍。我将在这一部分提出并讨论我的答案。

## 练习 2.75: `getAuthor`和`getTitle`

> 向该类添加两个访问器方法——`getAuthor`和`getTitle`——它们分别返回`author`和`title`字段的结果。通过创建一些实例并调用这些方法来测试您的类。巴恩斯·柯林(2009 年，第 53 页)

这个很简单。我在`Book`类中添加了以下内容:

```
public String getAuthor() {
    return this.author;
}

public String getTitle() {
    return this.title;
}
```

我将在本文后面测试它们的功能。

## 练习 2.76: `printAuthor`和`printTitle`

> 向 outlook `Book`类添加两个方法`printAuthor`和`printTitle`。这些应该分别将`author`和`title`字段打印到终端窗口。”巴恩斯&科林(2009 年，第 53 页)

我的回答需要使用`System.out.println`方法(见 [Java T Point n.d.](https://www.javatpoint.com/system-out-println-in-java) )打印出`author`和`title`字符串:

```
public void printAuthor() {
    System.out.println(this.author);
}

public void printTitle() { 
    System.out.println(this.title);
}
```

## 练习 2.77: `getPages`

> 向`Book`类添加另一个字段`pages`来存储页数。这应该是类型`int`，它的初始值应该传递给单个构造函数，以及`author`和`title`字符串。为此字段包含一个适当的`getPages`访问器。— [巴恩斯&科林(2009 年，第 53 页](https://www.bluej.org/objects-first/))

我的回答包括将下面的`private int`添加到`Book`类中声明的字段中:

```
// The fields
private String author;
private String title;
private int pages; // my addition
```

然后，我实现了返回`pages`字段的方法:

```
public int getPages() {
    return this.pages;
}
```

## 练习 2.78: `printDetails`

> "向`Book`类添加一个方法`printDetails`。这应该会将作者、标题和页面的详细信息打印到终端窗口。您可以选择如何设置细节的格式。例如，所有三个项目可以打印在一行上，或者每个项目可以打印在单独的一行上。你也可以选择加入一些说明性的文字来帮助用户理解:“`Title: Robinson Crusoe, Author: Daniel Defoe, Pages: 232`
> 
> — [巴恩斯&科林(2009 年，第 5 页](https://www.bluej.org/objects-first/)第 4 页)

我对这个练习的解决方案是:

```
public void printDetails() {
    System.out.printf("Title: %s, Author: %s.\n", this.getAuthor(), this.getTitle());
}
```

与`printAuthor`和`printTitle`不同，这个解决方案使用了`System.out`的`printf`函数，它允许我指定一个格式字符串并动态插入字段(参见 [Chugh 2022](https://www.digitalocean.com/community/tutorials/java-printf-method) )。这在这种特殊情况下很有用，因为我将打印出一个字符串，其中包含作者、标题和提供上下文的自定义消息。我将在以后的练习中修改这个函数。

## 练习 2.79: `refNumber`及其相应的访问器和赋值器

> 向`Book`类添加另一个字段`refNumber`。例如，该字段可以存储库的参考号。它的类型应该是`String`，并在构造函数中初始化为零长度字符串(`""`)，因为它的初始值不会通过参数传递给构造函数。相反，使用以下签名为它定义一个赋值函数:
> 
> `public void setRefNumber(String ref)`
> 
> 该方法的主体应该将参数值赋给`refNumber`字段。添加一个相应的`getRefNumber`访问器来帮助您检查 mutator 是否正常工作。”— [巴恩斯&科林(2009 年，第 5 页](https://www.bluej.org/objects-first/)第 4 页)

问题的第一部分很简单，我在类的字段部分添加了`private String refNumber`。接下来，我如下实现了`setRefNumber`:

```
public void setRefNumber(String x) {
    if (x.length() < 3) {
        System.out.println("Please enter a string with length greater than or equal to 3");
        return;
    }
    this.refNumber = x;
}
```

请注意，我确实有点违反规则，使用了`x`作为输入，而不是使用说明中指定的`ref`——我喜欢稍微改变一下配方😉

但更重要的是，我使用 Java 的`length()`函数(参见[教程点 n.d.](https://www.tutorialspoint.com/java/java_string_length.htm) )计算出输入参数`x`的长度，并定义了接受长度为三(3)或更长的字符串的方法。如果条件不满足，`setRefNumber`将打印错误信息，并通过`return;`语句终止。

我编写了以下访问器来返回`refNumber`字段:

```
public String getRefNumber() {
    if (this.refNumber == "")
        return "ZZZ";
    return this.refNumber;
}
```

`refNumber`字段的访问器和赋值器都“更聪明”,因为它们使用条件逻辑来确定输入是否有效；但是它可能会崩溃，因为我没有对不能被解析为`int`的`Strings`执行任何检查。这毕竟是一个玩具应用程序😉

## 练习 2.80 和 2.81

> “修改您的`printDetails`方法，以包括打印参考号。但是，该方法应该只在设置了引用号的情况下才打印引用号——也就是说，`refNumber`字符串的长度不为零。如果尚未设置，则打印字符串`“ZZZ”`代替。提示:使用条件语句，其测试调用`refNumber`字符串的`length`方法。巴恩斯·科林(2009 年，第 5 页)
> 
> "修改您的`setRefNumber` mutator，以便它仅在参数是至少三(3)个字符的字符串时设置 refNumber 字段。如果小于三(3)，则打印一条错误消息，并保留该字段不变。— [同上](https://www.bluej.org/objects-first/)

我将跳过练习 2.80(我没有实现它，只是稍微改变了一下食谱😉)但是我会注意到，这个实现涉及到使用条件逻辑来打印字符串的两个版本:一个将在没有设置`refNumber`时打印“ZZZ ”,另一个将在设置了`refNumber`时打印。

关于习题 2.81，我已经在习题 2.79 中解决了。我现在可以进行最后的练习来完成玩具图书编目应用程序。

## 练习 2.82: `borrowed`及其各自的`accessor`和`mutator`

> "向`Book`类添加另一个整数字段`borrowed`。这记录了一本书被借走的次数。向该类添加一个赋值函数`borrow`。每次调用该字段时，它都会更新 1。包括一个访问器`getBorrowed`，它返回这个新字段的新值作为结果。修改`printDetails`,使其包含该字段的值和一段解释文本。— [巴恩斯&科林(2009 年，第 5 页](https://www.bluej.org/objects-first/) 4 页)

好了，这是关于玩具图书编目应用的练习题的最后一部分。我首先在`Book`类的字段部分将`borrowed`定义为一个`private int`。下面是我对`borrowed`方法的实现:

```
public void borrow() {
    this.borrowed++;
}
```

在我看来这是不言自明的。下面是我对`getBorrowed`访问器的实现:

```
public int getBorrowed() {
    return this.borrowed;
}
```

最后，下面是我对`printDetails`的修改，包括了`pages`和`borrowed`字段的信息:

```
public void printDetails() {
    System.out.printf("Title: %s, Author: %s, Pages: %d, Borrowed: %d.\n", this.getAuthor(), this.getTitle(), this.getPages(), this.getBorrowed());
}
```

现在我可以开始测试这个漂亮的小图书编目应用程序了🔥

# 粗略测试

下面是一个标准的 main 方法，我可以用它来生成简单的测试用例，并对类进行初步评估:

```
public static void main(String[] args) {
    Book objBook = new Book("The Black Swan", "Nassim Taleb", 666);
    System.out.printf("Testing the getAuthor, getTitle and getPages functions: %s, %s, %d.\n", objBook.getAuthor(), objBook.getTitle(), objBook.getPages()); // Solution to Exercise 2.75 & 2.77
    objBook.printAuthor(); // Solution to Exercise 2.76
    objBook.printTitle(); // Soultion to Exercise 2.76 (ii)
    objBook.printDetails(); // Solution to Exercise 2.78

    objBook.setRefNumber("42"); // Solution to Exercise 2.81 (bad entry)
    System.out.printf("Testing Reference Number: %s\n", objBook.getRefNumber());
    objBook.setRefNumber("777"); // Solution to Exercise 2.81 (good entry)
    System.out.printf("Testing Reference Number: %s\n", objBook.getRefNumber());

    // Solution to Exercise 2.82
    for (int i = 1; i <= 5; i++) 
        objBook.borrow();

    objBook.printDetails(); // Solution to Exercise 2.82
}
```

我将编译它，运行它并讨论它的结果:

```
**dev@dev-java:~/intro-to-java/ch2$ javac Book.java**
**dev@dev-java:~/intro-to-java/ch2$ java Book**
Testing the getAuthor, getTitle and getPages functions: The Black Swan, Nassim Taleb, 666.
The Black Swan
Nassim Taleb
Title: The Black Swan, Author: Nassim Taleb, Pages: 666, Borrowed: 0.
Please enter a string with length greater than or equal to 3
Testing Reference Number: ZZZ
Testing Reference Number: 777
Title: The Black Swan, Author: Nassim Taleb, Pages: 666, Borrowed: 5.
**dev@dev-java:~/intro-to-java/ch2$**
```

我将讨论一些代码行及其输出。

## 定义一个`Book`的实例

我将从定义一个`Book`类的实例开始:

```
Book objBook = new Book("The Black Swan", "Nassim Taleb", 666);
```

书的`title`是`The Black Swan`，它的`author`是`Nassim Taleb`，它有`666`页。这将是评估某些功能和`printDetails`的基础。

## 评估`getAuthor`、`getTitle`和`getPages`

下面一行代码将评估关于`author`、`title`和`pages`的`Book`类的访问器:

```
System.out.printf("Testing the getAuthor, getTitle and getPages functions: %s, %s, %d.\n", objBook.getAuthor(), objBook.getTitle(), objBook.getPages()); // Solution to Exercise 2.75 & 2.77
```

如果 Java 应用程序工作正常，应该会分别打印出`Nassim Taleb`、`The Black Swan`和`666`。确实如此:

```
Testing the getAuthor, getTitle and getPages functions: The Black Swan, Nassim Taleb, 666.
```

从这个初步评估来看，`author`、`title`和`pages`字段的存取器正在工作。

## 评估`print*`方法

以下代码行评估`printAuthor`、`printTitle`和`printDetails`方法是否有效:

```
objBook.printAuthor(); // Solution to Exercise 2.76
objBook.printTitle(); // Soultion to Exercise 2.76 (ii)
objBook.printDetails(); // Solution to Exercise 2.78
```

如果工作正常，它应该打印出“标题:黑天鹅，作者:纳西姆塔勒布，页数:666，借阅:0”。事实证明，确实如此:

```
Title: The Black Swan, Author: Nassim Taleb, Pages: 666, Borrowed: 0.
```

## 评估`refNumber`及其访问器和赋值器

下面将测试`refNumber`的访问器和赋值器的正确性:

```
objBook.setRefNumber("42"); // Solution to Exercise 2.81 (bad entry)
System.out.printf("Testing Reference Number: %s\n", objBook.getRefNumber());
objBook.setRefNumber("777"); // Solution to Exercise 2.81 (good entry)
System.out.printf("Testing Reference Number: %s\n", objBook.getRefNumber());
```

我给出了一个不满足至少三(3)个字符长规范的参考号测试用例(42)，以及另一个满足三个或更多字符长规范的参考号测试用例(777)。如果我的应用程序工作正常，当试图访问它时，它会打印出第一种情况和`“ZZZ”`的错误消息，并在第二种情况下设置参考号，因为它符合规范。

```
Please enter a string with length greater than or equal to 3
Testing Reference Number: ZZZ
Testing Reference Number: 777
```

它按照它的本意工作。

## 评估`borrow`和`getBorrowed`

最后，我想看看`borrow`和`getBorrowed`方法是否如预期的那样工作。以下代码对它们进行评估:

```
// Solution to Exercise 2.82
for (int i = 1; i <= 5; i++) 
    objBook.borrow();

objBook.printDetails(); // Solution to Exercise 2.82
```

我的推理是这样的: `printDetails`方法的第一次调用已经打印出了一个值为 0 的`borrow`，因为它还没有被调用。然而，我将使用上面定义的`for`循环调用 borrow 方法五(5)次，然后再次调用`printDetails`。如果`borrow`和`getBorrowed`方法如预期的那样工作，在这个 for 循环执行之后，我将得到一个借位值五(5)。检查输出:

```
Title: The Black Swan, Author: Nassim Taleb, Pages: 666, **Borrowed: 5.**
```

我的结论是，粗略地看，所有的方法和字段都像预期的那样工作。

# 最后的想法

我正在尝试不同种类的内容，试图为计算和计算教育领域做出贡献。我不认为自己是一名教师，因为我没有专家身份的编程经验，所以我是从第一人称的角度而不是从教程的角度来写这篇文章的。我强烈建议对 Java 编程感兴趣的读者首先阅读 Java 教科书中的 T13 对象，以便更好地了解如何用 Java 编程，或者如果读者有经验的话，如何提高自己的技能。

对于任何有兴趣跟随我学习编码的读者，我建议查看我的[大学课程 GitHub 资源库](https://github.com/Alekseyyy/SNHU)，因为我在那里不仅存储了我的课程，还存储了许多其他与 ICT 相关的课外活动；以下是它的链接:

[](https://github.com/Alekseyyy/SNHU) [## GitHub - Alekseyyy/SNHU:我大学时做的事情

### 我是南新罕布什尔大学的本科生，主修计算机科学，辅修…

github.com](https://github.com/Alekseyyy/SNHU) 

此外，如果任何读者想看更多我的技术文章(其中大部分讨论计算，但也有一些其他科学工程主题)，我邀请他们查看我的一系列技术文章，我在这些文章中讨论了这些主题:

![Aleksey](img/886245347566d4089464c40d5ec5ccfc.png)

[阿列克谢](https://medium.com/@EpsilonCalculus?source=post_page-----25cd033436a0--------------------------------)

## 技术报道

[View list](https://medium.com/@EpsilonCalculus/list/technical-writeups-63f8cfbee59c?source=post_page-----25cd033436a0--------------------------------)43 stories![](img/e91d6d5a97594a0fdfb555761a774256.png)![](img/637c9938ab55d24718c20c200ddda1a0.png)![](img/78df73524bea8b23f1be91acbb669dc6.png)

最后，这是用 Java 编写的玩具图书编目应用程序的成品:

来源:[《阿列克谢》(2022)](https://gist.github.com/Alekseyyy/a621a72c2cf9b6487cf8313ccc2908eb)；注意，该文件应该保存为`Book.java`以便编译。我把它保存为`medium.03122022.Book.java`来帮助组织我的 GitHub gists。

# 参考

《阿列克谢》(2022)。*我的中等代码片段的要点*。GitHub Gists。[https://gist . github . com/Alekseyyy/a 621 a 72 C2 cf 9 b 6487 cf 8313 CCC 2908 EB](https://gist.github.com/Alekseyyy/a621a72c2cf9b6487cf8313ccc2908eb)

巴恩斯和科林(2009 年)。*Java 的对象优先:使用 BlueJ 的实用介绍*【第四版】。皮尔逊教育国际。

查格(2022)。 *Java printf() —将格式化字符串打印到控制台*。数字海洋。2022 年 12 月 3 日检索自:[https://www . digital ocean . com/community/tutorials/Java-printf-method](https://www.digitalocean.com/community/tutorials/java-printf-method)

Java T 点(未标明)。`System.out.println()`中的*爪哇*。2022 年 12 月 3 日检索自:[https://www.javatpoint.com/system-out-println-in-java](https://www.javatpoint.com/system-out-println-in-java)

甲骨文公司(未标明)。*甲骨文认证助理，Java SE 8 程序员*。2022 年 12 月 2 日检索自:[https://education . Oracle . com/Oracle-certified-associate-Java-se-8-programmer/trackp _ 333](https://education.oracle.com/oracle-certified-associate-java-se-8-programmer/trackp_333)

甲骨文公司(未注明)。 *Java 8 中央*。2022 年 12 月 2 日检索自:[https://www.oracle.com/java/technologies/java8.html](https://www.oracle.com/java/technologies/java8.html)

PYPL 编程语言流行指数。2022 年 12 月 2 日检索自:[https://pypl.github.io/PYPL.html](https://pypl.github.io/PYPL.html)

教程点(未注明)。 *Java — String* `length()` *方法*。2022 年 12 月 3 日检索自:[https://www.tutorialspoint.com/java/java_string_length.htm](https://www.tutorialspoint.com/java/java_string_length.htm)

Wild Kratts Wiki(未注明)。*蓝鹭动力*。2021 年 11 月 17 日修订。2022 年 12 月 3 日检索自:[https://wildkratts.fandom.com/wiki/Blue_Heron_Power?oldid=78815](https://wildkratts.fandom.com/wiki/Blue_Heron_Power?oldid=78815)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)