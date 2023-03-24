# 每个初学编程的人都需要学习的 4 个程序模板

> 原文：<https://levelup.gitconnected.com/4-program-templates-every-beginning-programmer-needs-to-learn-3f46d738e3ad>

## 程序模板是成功编程的关键心理表征

![](img/ed7a271eb549bba0bda0e89a9f6f8e1e.png)

程序模板是编程代码使用的常见模式，作为编程问题的解决方案一再出现。计算机编程的一个关键技能是学会识别这些模式，然后将它们应用到独特的情况中。在这篇文章中，我将讨论每个初学编程的学生应该记住的四个程序模板，并在编程实践中练习识别和应用。

程序模板，尤其是这些开始模板，通常在教科书或编程教师中没有具体教授。不过，它们应该是，因为学习它们会给学生提供关键的心理表征，使他们编写程序更容易。

我正好有一本旧的 Pascal 教科书，由 Mike Clancy 和 Marcia Linn 所著的 *Designing Pascal Solutions* ，它广泛地教授和使用程序模板，如果你对学习程序模板感兴趣，你应该找这本书。我在本文中讨论的程序模板直接来自这本书。

这本书也是一本强调基于项目的学习的教科书的一个很好的例子。我发现，当学生有一系列项目要做时，而不仅仅是无关的习题集，他们会学得更好。我将在另一篇文章中更多地讨论用于计算机编程教学的基于项目的学习。

## 输入-处理-输出

在很多方面，你写的每一个计算机程序都是这个模板的应用。如果你不只是写一句“你好，世界！”程序，你的程序将接受一些数据(输入)，以某种方式转换(处理)它，显示结果(输出)或对它做其他事情，比如把它写到一个文件。对于初学编程的人来说，应用这个模板是开始编写程序解决问题的最佳方式。

这个模板可以用伪代码写成:

*输入所有值*
处理所有值
输出处理结果

下面是一个使用此模板的问题示例:

首先从键盘输入三个成绩，求出平均成绩。把这三个等级加在一起，得到一个总数。将总数除以年级数，得到平均值。显示平均值。

下面是用 Python 解决这个问题的示例代码。我要求学生在模板的每一步之前添加注释，以表明他们对模板的了解。

```
#input
grade1 = int(input("Enter the first grade: "))
grade2 = int(input("Enter the second grade: "))
grade3 = int(input("Enter the third grade: "))
num_grades = 3
#processing
total = grade1 + grade2 + grade3
average = total / num_grades
#output
print("The grade average is",average)
```

让学生记住这个程序模板有助于他们组织如何着手解决问题的思路。这减轻了认知负荷，这样他们就可以集中精力于正确的语法以及解决问题所必需的逻辑。

## 提示，然后阅读

这个模板是一个非常基本的模式，但是初学者不一定知道。在讲授这一点时，我强调提示需要提供非常丰富的信息，以便用户准确输入所需的信息。

该模板的伪代码是:

*显示要求特定输入(值)的消息
读取输入(值)*

以下是 JavaScript 中该模板的一些代码示例:

```
putstr("Enter your six-digit id number: );
id_number = readline();
putstr("Enter your first name: ");
firstName = readline();
putstr("Enter your last name: ");
lastName = readline();
putstr("Enter your salary: ");
salary = parseInt(readline());
```

对于新的编程学生来说，学习和理解清晰的提示对于指导用户如何接收输入的重要性尤为重要。我发现这种指导也有助于学生了解数据特殊性的重要性，以便计算出正确的值。

```
string firstName;
# say "Hello" to a name five times
for (int i = 1; i <= 5; i++) {
 #input one (also prompt and read)
 cout << "What is your first name? ";
 cin >> firstName;
 #process one
 cout << "Hello, " << firstName << "!" << endl;
}
```

## 做某事特定的次数

这是我向学生介绍`for`循环时教给他们的第一个模板。事实上，我没有先明确介绍循环，我介绍了模板，然后演示了 for 循环作为实现模板的一种方法。

以下是模板:

*重复指定次数:
执行某个动作*

我在课堂上演示的第一个例子是一个经典的“你好，世界！”示例—显示“你好，世界！”屏幕上五次。以下是用 C++编写的代码:

```
// write "Hello, world!" to the screen five times
for(int i = 1; i <= 5; i++) {
  cout << "Hello, world!";
}
```

然后，我将修改该示例，并将其与“输入一个，处理一个”模板相结合，让学生提示用户输入他们的名字，并让程序向输入的名字问好:

```
string firstName;
# say “Hello” to a name five times
for (int i = 1; i <= 5; i++) {
 #input one (also prompt and read)
 cout << “What is your first name? “;
 cin >> firstName;
 #process one
 cout << “Hello, “ << firstName << “!” << endl;
}
```

在这一点上，我的学生已经准备好了一个问题，所以我给他们这个:提示用户输入五个等级，并把它们加起来。以下是我正在寻找解决方案的代码:

```
int grade;
int total = 0;
const int NUM_GRADES = 5;
#enter and total five grades
for (int i = 1; i <= NUM_GRADES; i++) {
 #input one — also prompt and read
 cout << “Enter a grade: “;
 cin >> grade;
 #process one
 total = total + grade
}
```

请注意，每个步骤的注释都是必需的。同样，让我的学生为模板中的每个步骤输入注释有助于在他们的脑海中巩固该过程的步骤。

## 输入一，处理一

这个模板是在我教学生循环的时候引入的。我通常把它作为一个问题的一部分来介绍，这个问题涉及到让用户输入一些数据，然后对这些数据做一些事情。

该模板的伪代码是:

*重复以下步骤:
输入一个值
用值*做一些事情

我通常用这个问题来介绍模板，并让我的学生练习编写循环:编写一个程序，提示用户输入分数，并将分数值添加到总变量中。

我正在寻找的代码看起来像这样(在 Python 中):

```
count = 1
total = 0
while count <= 5:
 #input one
 grade = int(input(“Enter a grade: “))
 #process one
 total = total + grade
 count = count + 1
```

模板中步骤前的注释很重要，我总是需要它们，这样我知道学生理解模板是如何工作的。这些注释不仅确认了他们的模板知识，还通过明确地说出他们在模板步骤中的位置，帮助学生建立模板的坚实的心理表示。

## 程序模板是大量的编程专业知识

专业领域的研究人员早就认识到，专家通过“分块”他们领域的关键技能和技术成为专家。经典的例子是国际象棋大师们通过多年的深入研究，对成千上万个棋盘位置了如指掌。关于组块，需要知道的重要一点是，这些组块不是通过玩更多的国际象棋游戏而习得的。成为国际象棋大师的人从完整的游戏中孤立地研究棋盘位置，然后他们能够应用这种知识，以便在实际的国际象棋比赛中获得对对手的优势。

初学编程的人也可以使用程序模板来获得同样的好处。花些时间记住各种程序模板，包括它们为什么以及如何被用来解决经常遇到的编程场景，你会发现你会更快地掌握你的编程语言。

然后，当你觉得你已经掌握了这些模板，开始用它们来解决越来越难的问题。这是刻意练习的精髓，也是某个领域的专家与众不同的地方。通过一次又一次地解决同样的问题，你不会成为专家。你必须不断用新的有趣的问题挑战自己。

*原载于 2020 年 2 月 25 日 https://thelearningprogrammer.com*[](https://thelearningprogrammer.com/4-program-templates-every-beginning-programmer-needs-to-learn/)**。**