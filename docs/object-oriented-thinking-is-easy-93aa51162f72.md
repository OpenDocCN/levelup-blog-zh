# 面向对象的思维很容易

> 原文：<https://levelup.gitconnected.com/object-oriented-thinking-is-easy-93aa51162f72>

![](img/4b4794950634128d257dc1424a6b19cf.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

面向对象编程(OOP)并不是在计算机程序中建模世界的完美方式。然而，这比试图理解处理器芯片、虚拟机甚至操作系统的复杂性要容易得多。

有很多面向对象的术语，但是没有一个会让学生气馁。任何值得学习的东西都会有很多相关的术语。老师应该尽可能清楚地解释术语。稍后我将回到行话。

OOP 的基本思想很简单:你编写代表问题域中对象的类。例如，如果你正在写一个汽车租赁帐单的程序，你将创建对象来表示汽车、客户帐户、发票等等。

你不用担心诸如芯片的寄存器有多少位、操作系统如何调度线程等细节。你甚至不必担心你正在使用的编程语言的晦涩难懂的特性。

OOP 思想的实现并不总是符合预期。C++和 Java 在这方面受到了很多批评。

我们当然可以不同意将 OOP 概念转化为工作解决方案的最佳方式，但是通常 OOP 编程语言确实简化了编程任务。

OOP 的一个著名弱点是香蕉猴子丛林问题。你需要一个物体(香蕉)来做一件简单的小事。但是你需要一些东西来拿香蕉(猴子)。接下来，您知道，您已经将整个生态系统(丛林)导入到您的项目中。

这里有一个例子，不太引人注目:使用 Java 和 Java 虚拟机(JVM)，向用户的临时目录写一个文本文件，文本为“Hello，world！”

这是一项很容易的任务。我们需要从`java.io`包中导入三到四个类。

```
package org.example.fileops;import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileWriter;
import java.io.IOException;
```

`FileHelloWorld`类将只包含一个静态过程`main()`。

```
public class FileHelloWorld {

    public static void main(String[] args) {
        String tempDirPath = System.getProperty("java.io.tmpdir");
        String nameWithExtension = "HelloWorld.txt";
        String filename = tempDirPath + File.separatorChar 
                + nameWithExtension;
        File file = new File(filename);
        try {
            file.createNewFile();
            try (FileWriter writer = new FileWriter(file)) {
                writer.write("Hello, world!");
            }
            System.out.println("Please look in " + tempDirPath 
                    + " for " + nameWithExtension);
        } catch (IOException ioe) {
            System.err.println(ioe.getMessage());
        }
    }

}
```

我运行这个，果然:

> run:
> 请在 C:\ Users \ AL \ AppData \ Local \ Temp \中查找 HelloWorld.txt
> 构建成功(总时间:0 秒)

我们可以通过添加检查文件是否已经存在并请求用户允许覆盖它来增加循环复杂度。那就需要更多的物体。

顺便说一下，这还需要我们写一个 If-Else 语句，可能是嵌套的，无论如何都要避免，因为这会让我们看起来像不懂事的乡巴佬。

不过，说真的，我怀疑您的 temp 文件夹中是否已经有了 HelloWorld.txt 文件。然而，这个`main()`忽略了`createNewFile()`函数的返回类型，它是一个布尔原语(Java 语言中小写的“`boolean`”)。

这实际上比 If 语句更让我烦恼。但这并不能证明我的观点，即需要一大堆对象来完成一个小任务。

目前，`FileHelloWorld`需要一个`File`对象和一个`FileWriter`对象，一个`IOException`对象以防在创建、打开、写入和关闭文件时出错…

和大约八个不同的`String`实例(如果我没有数错的话；请在评论中告诉我——请记住`String`文字是对象，不是原语。如果我们想给这个简单的程序一个图形用户界面，还需要几十个对象。

我确信这个程序可以更简单，无论是用 Java 还是其他编程语言。即便如此，Java 开发工具包的面向对象设计使我们不必担心几个重要的细节。

首先，如何查看文件分配表(FAT)来检查是否有足够的空间来存放新文件？Mac OS 用 FATs 吗？Linux 呢？有任何有效的安全限制吗？诸如此类。

原来我们不需要导入`FileNotFoundException`。它由`IOException`通过继承层次结构覆盖。

当谈到 OOP 时，继承是人们不同意的事情之一，这导致一些人编造故事，艾伦·凯告诉所谓的专家他们都错了。

所有主要的 OOP 语言(例如 C++、Java、C#，仅举三个例子)似乎都有继承，所以如果我们想用这些语言编程，我们必须处理继承。

继承带来多态性。这仅仅意味着传递物体的灵活性。如果一个类中的单元接受一个给定类的参数，它也接受该类的子类的参数。

例如，假设我们编写一个类，它的构造函数需要一个`URLConnection`。但是我们不关心它是 HTTP 还是 HTTPS 连接。这两种类型之间的任何不同都由适当的虚函数查找来解决。

多态性可以使我们不必编写一些 If-Else 语句，从而降低我们编写的类的圈复杂度。但是，不要忘乎所以，要务实。有时一个恰当的 If 语句比一个做作的枚举更好。

封装是 OOP 基本思想的自然结果:每个对象都知道自己的状态，并控制其他对象如何查询或改变该状态。

就计算机芯片而言，没有像`FileWriter`对象、`Invoice`对象这样的东西，或者我们的程序可能需要的任何类型的对象。

计算机芯片根据寄存器和堆栈来“思考”。例如，如果从堆栈中按位弹出一个值，或者 flags 寄存器等于零，程序可能会转到某一行。

从那以后，芯片可能会从堆栈中弹出另一个值，并与主寄存器进行位与运算。该操作的结果可能触发到程序中另一个位置的另一个跳转。

即使对类有所了解的 JVM，除了编程到对象中的内容之外，对对象也一无所知。

对我们来说，这是有意义的，例如，`CheckDeposit`扩展了`Deposit`，后者又扩展了`Transaction`(后者隐含地扩展了`Object`)。

但是就 JVM 而言，继承层次结构可以很容易地被`Sr82xa_`扩展`Mpla47ñ`，后者扩展`T10Oa3`(后者隐式扩展`Object`)。

如果`Withdrawal`扩展了`Transaction`，那么`Withdrawal`实例可以被发送给需要`Transaction`实例的函数，但是不能被发送给需要`Deposit`实例的函数。

那只是常识。但是我们必须向电脑解释。

像行话一样，首字母缩写词使 OOP 的学习曲线变得陡峭。最著名的 OOP 首字母缩略词之一可能是 SOLID。这可能感觉就像一个密码，把知道的人和不知道的人分开。

SOLID 只是五个相关 OOP 概念的助记符。在我看来，其中最重要的是单一责任原则。如果你遵守单一责任原则，其他四个坚实的概念几乎会自动到位。

单一责任原则适用于最小的单元，也适用于类、包、模块、程序和整个系统。

一般来说，Java 开发工具包的作者非常善于在类级别遵守单一责任原则。例如，`FileWriter`类只有一个责任:写文件。要读取文件，你需要类似于`FileReader`(也来自`java.io`)的东西。

从包装的角度来看，就不是这样了。在我看来，`java.util`中至少有一组类和接口应该被分离到不同的包`java.collections`中。当然，现在改变已经太晚了。

在类的层次上，单一责任原则仅仅是对 OOP 概念的一种肯定，即计算机程序中的对象反映了我们试图解决的问题领域中的对象。

例如，纸质发票可能会在适用时列出货币兑换，但它的唯一职责是列出要记入客户账户的费用，而不是按需执行任意的货币兑换。

同样，`Invoice`对象可能需要调用`CurrencyConverter`对象来获得货币兑换，但是`Invoice`对象的唯一职责是列出要向`Account`对象收取的费用。任意货币转换由`CurrencyConverter`单独负责。

接口分离原则(实线中的 I)可以用单一责任原则来表述:接口不应该强迫实现类违反单一责任原则。

人们似乎想出了玩具例子来解释接口隔离原则，这比他们用来解释单一责任原则的玩具例子更愚蠢。

想出既现实又容易理解的例子有时会很难。

例如，关于接口分离原则，我想到了`Animal`类和`GlideCapable`和`FlightCapable`接口。希望读者能理解我举这个例子的意图，并原谅这个例子有多愚蠢。

玩具例子的愚蠢不应该使我们看不到 OOP 的要点:通过允许我们把我们的程序看作是对我们手头问题的心理模型中的对象进行操作来简化编程，而不是考虑计算机芯片或运行这些程序的操作系统的螺母和螺栓。

如果 OOP 的现实被证明是复杂的，那是因为 OOP 思想的实现增加了不必要的复杂性，而不是因为 OOP 思想有任何固有的缺陷。