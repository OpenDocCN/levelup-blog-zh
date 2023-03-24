# 学习 C++:使用文本文件

> 原文：<https://levelup.gitconnected.com/learning-c-working-with-text-files-91104a8fab33>

![](img/11c89270703df1312e34fd9fd5dcda3d.png)

[David Ballew](https://unsplash.com/@daveballew?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

C++具有创建、访问和编辑文本文件的能力。在本文中，我将演示如何做到这一点。我将不讨论如何处理二进制文件。

# FileStream 对象、头文件、文件访问和文件名

C++使用文件流抽象来处理文本文件。文件流是进入文本文件进行存储或者从文本文件中出来加载到程序中的数据。输入(`>>`)和输出(`<<`)操作符用于将数据传输到文件或程序中。

要执行文件输入(从文件中接收数据)，必须在程序中包含`ifstream`头文件。要执行文件输出(将数据发送到文件)，您必须在程序中包含`ofstream`头文件。

从程序中访问文件有两种主要方式——顺序访问和随机访问。当使用随机存取访问文件时，程序可以在文件中随机来回移动。这意味着程序可以读取文件中的第一条记录，然后立即移动到第五条记录，或第一百条记录。

另一方面，当按顺序访问文件时，程序只能在文件中向前移动，一次一条记录。这意味着如果你想读取文件中的第五条记录，你必须先读取第一到第四条记录。

顺序文件访问比随机访问更容易处理，所以在本文中我们将专门处理顺序文件。

文件在磁盘上以文件名表示。因为我们使用的是文本文件，所以文件名应该有. txt 扩展名，这样系统就可以将它们识别为文本文件，而不是其他类型的文件。

# 在文本文件中创建和存储数据

我们的第一个任务是在文本文件中创建和存储数据。`ofstream`库包括一个创建新文件的函数——open，它接受一个由您想要使用的文件名组成的字符串参数。

文件分两步创建。第一步是声明文件名，第二步是从新文件调用 open 函数。以下是创建文本文件所需的代码行:

```
ofstream outFile;
outFile.open("myfile.txt");
```

open 函数的参数是您正在编写的输出文件的完整路径。如果你只包括文件名，你的文件将被写入你的程序所在的文件中。否则，如果要将文件写在其他地方，请在文件名前指定完整路径。

下一步是将数据写入文件。当然，这一步将取决于您试图执行的任务。程序的下一部分让用户在提示符下输入文本，然后使用文件名作为输出流的目标将数据写入文件。代码如下:

```
string quit = "n";
string line;
while (quit != "y") {
  cout << "Enter a line of text: ";
  getline(cin, line);
  outFile << line << endl;
  cout << "Stop entering text (y/n)? ";
  getline(cin, quit);
}
```

最后一步，也是重要的一步，是关闭文件。虽然如果程序在关闭输出文件之前结束，您可能不会丢失数据，但是在使用完文件后关闭它们是一个很好的编程习惯。

下面是创建文件并将数据写入该文件的完整程序:

```
#include <iostream>
#include <fstream>using namespace std;int main()
{
  ofstream outFile;
  outFile.open("myfile.txt");
  string quit = "n";
  string line;
  while (quit != "y") {
    cout << "Enter a line of text: ";
    getline(cin, line);
    outFile << line << endl;
    cout << "Stop entering text (y/n)? ";
    getline(cin, quit);
  }
  outFile.close();
  return 0;
}
```

一旦你关闭了一个文件，你就完成了它，直到你想打开它，以便从中读取数据。我将在下一节介绍如何做到这一点。

# 从文本文件中读取数据

要从文本文件中读取数据，必须打开文件进行输入。您需要做的第一件事是声明一个`ifstream`对象来表示一个输入文件。然后，您需要通过提供文件的路径来打开文件。

指定文件路径时需要小心，因为如果路径中有反斜杠，就必须对反斜杠进行转义，这样 C++就不会将文件路径的一部分解释为转义字符。

例如，如果文件路径在名称中包含一个`\t`，C++将把它解释为制表符，系统将抛出一个错误。你需要在你的路径上加两个反斜杠，这样就不会发生这种情况。

在我将要展示的例子中，文件路径是:

`c:\users\mmcmi\documents\words.txt`

相反，我将把路径写成这样:

`c:\\users\\mmcmi\\documents\\words.txt.`

文件打开后，您可以遍历文件并读取每一行。file 对象可以作为循环的条件，意味着当文件中有数据要读取时，文件名返回 true ( `1`)，当没有更多数据要读取时，文件名返回 false ( `0`)。

最后一步是关闭文件以保持良好的软件工程实践，尽管如果你不这样做，操作系统会自动关闭文件。

现在我们准备好查看一个完整的程序，它从硬盘中读取一个文本文件并显示文件中的所有数据。我正在打开和阅读的文件是一本单词词典。

程序如下:

```
#include <iostream>
#include <fstream>using namespace std;int main()
{
  ifstream inFile;
  inFile.open("c:\\users\\mmcmi\\documents\\words.txt");
  string word;
  while (inFile) {
    getline(inFile, word);
    cout << word << endl;
  }
  inFile.close();
  return 0;
}
```

以下是该文件输出的部分显示:

```
…
bloggers
blogging
blogs
blond
blonde
blood
bloody
bloom
bloomberg
blow
blowing
…
```

编写上面程序的一个更简洁的方法是将条件中的文件检查与从文件中获取下一个单词结合起来。这是它的样子:

```
int main()
{
  ifstream inFile;
  inFile.open("c:\\users\\mmcmi\\documents\\words.txt");
  string word;
  while (getline(inFile, word)) {
    cout << word << endl;
  }
  inFile.close();
  return 0;
}
```

当循环到达文件末尾时，`getline` 将返回一个假值，循环停止。

# 在文本文件中处理数字

当数字存储在文本文件中时，它们是以字符串的形式存储的，所以当它们被读入程序时，它们必须被转换成正确的数字数据类型。下面是一个从文本文件中读取一组考试成绩并计算成绩平均值的示例:

```
#include <iostream>
#include <fstream>
#include <string>using namespace std;int main()
{
  ifstream inFile;
  inFile.open("grades.txt");
  string strGrade;
  int grade, total, numGrades;
  total = 0;
  numGrades = 0;
  while (getline(inFile, strGrade)) {
    cout << strGrade << " ";
    numGrades++;
    grade = stoi(strGrade);
    total += grade;
  }
  inFile.close();
  double average = static_cast<double>(total) / numGrades;
  cout << endl << endl << "The average test grade is: "
       << average << endl;
  return 0;
}
```

这个程序使用`stoi`函数将字符串等级转换成整数。

*W* 使用一个包含等级——82，91，77，84，91，63——的文件，这个程序的输出是:

```
82 91 77 84 91 63The average test grade is: 81.3333
```

# 将数据追加到文件中

除了打开文件进行输入或输出之外，您还可以打开一个文件，以便向其中添加新数据。您可以通过添加常量`ios::app`作为 open 函数的第二个参数来实现这一点。以下程序演示了如何将数据追加到现有的成绩文件中，该文件与我们在上面的示例中使用的文件相同:

```
int main()
{
  ofstream outFile;
  outFile.open("grades.txt", ios::app);
  int grade1 = 91;
  int grade2 = 87;
  int grade3 = 93;
  outFile << grade1 << endl;
  outFile << grade2 << endl;
  outFile << grade3 << endl;
  outFile.close();
  ifstream inFile;
  inFile.open("grades.txt");
  int grade;
  while (inFile >> grade) {
    cout << grade <<  " ";
  }
  inFile.close();
  return 0;
}
```

这些就是我想在这篇关于用 C++处理文本文件的文章中讨论的要点。请将您的意见和建议发邮件至 mmmcmillan1@att.net[给我。如果你对我的在线编程课程感兴趣，请访问](mailto:mmmcmillan1@att.net)[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。