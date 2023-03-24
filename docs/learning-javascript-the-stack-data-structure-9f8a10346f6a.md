# 学习 JavaScript:堆栈数据结构

> 原文：<https://levelup.gitconnected.com/learning-javascript-the-stack-data-structure-9f8a10346f6a>

![](img/8edca36ac7b93a4d3aa30e35659eaf0c.png)

伊瓦·拉乔维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

堆栈数据结构在计算机科学中有多种用途。在编程语言中，函数调用是通过使用堆栈来控制何时调用函数来实现的。在应用程序中，堆栈用于计算数字表达式，并可用于在不同基数之间转换数字，例如从十进制到二进制或从二进制到十六进制。

堆栈也不太难实现，因为堆栈可以执行的操作数量有限。三个主要的操作符是:1)把一个新的项目推到堆栈上；2)从堆栈中弹出顶部项目；以及 3)检查堆栈顶部的项目。您还可以实现一些其他的实用函数，但是要正确使用堆栈，您所需要的就是这三个操作。

在本文中，我将向您展示如何用 JavaScript 构建一个 stack 类，并使用它来解决几个有趣的编程问题。

我在本文中使用的是 SpiderMonkey JavaScript shell，但是您应该能够使用其他 JavaScript 实现，而无需对我的代码进行太多更改。

# 描述堆栈

堆栈是一种数据结构，其中数据被添加到堆栈的顶部，一次一个数据项，并且数据仅从顶部从堆栈中移除。现实生活中有很多栈的例子。

一个例子是装入打印机的纸叠。当打印机想要打印一页时，它从纸堆的顶部取出一张纸，然后在上面打印。如果您想在有信头的纸上打印，或者打印一些尚未装入打印机的纸，您必须将那张纸放在纸叠的顶部。

如果您想从纸堆中取出纸张，您必须从顶部取出，因为您无法接触到顶部纸张下面的纸张。最后，你能在纸堆中看到的唯一一张纸是最上面的那张，因为它覆盖了所有其他的纸。

另一个真实的堆栈示例是自助餐厅的托盘。当顾客想要一个托盘时，他们必须从堆叠的顶部拿取。如果洗碗工想增加一些托盘，他或她必须将干净的托盘放在托盘堆的顶部。如果您想查看堆叠中的托盘，您只能查看顶部的托盘，因为它覆盖了所有其他托盘。

事实证明，这种设计对于许多基于计算机的应用程序也是有用的，其中一些我在上面提到过。

# 堆栈类设计

我的堆栈实现使用 JavaScript 类，而不仅仅是一个对象。我喜欢 JavaScript 类的抽象，如果您选择将实现转移到另一种语言，如 C++或 Java，JavaScript 设计将比使用 JavaScript 原型实现堆栈更有用。如果您不熟悉 JavaScript 类，这里的[是我写的一篇文章，详细讨论了如何创建和使用 JavaScript 类。](/learning-javascript-an-introduction-to-classes-part-1-d4c07b59ee3c)

设计高效堆栈类的关键是使用正确的底层数据结构来存储堆栈项目。我将使用一个数组作为我的 stack 类的数据存储。JavaScript 数组有几个很好的内置方法，使得使用它们很容易。

堆栈操作实现为可从`Stack`实例调用的类方法(函数)。除了`push`、`pop`和`top`之外，我还将实现一个`size`方法来确定堆栈中元素的数量，一个`empty`方法来测试堆栈实例中是否有数据，一个`clear` 方法来删除堆栈中的所有内容。

由于堆栈的操作数量如此之少，我们已经准备好进入堆栈类实现了。

# 堆栈类实现

正如堆栈操作列表很短一样，实现一个`Stack`类所需的代码也很短。具有讽刺意味的是，要实现的前两个方法`push`和`pop`具有相同名称的函数，用于 JavaScript 数组的相同目的。下面是`Stack`类的定义:

```
class Stack {
  constructor() { 
    this.dataStore = []
  } push(element) {
    this.dataStore.push(element)
  } pop() {
    this.dataStore.pop()
  } top() {
    return this.dataStore[this.dataStore.length-1]
  } size() {
    return this.dataStore.length;
  } empty() {
    if (this.dataStore.length == 0) {
      return true;
    }
    return false;
  } clear() {
    if (this.dataStore.length == 0) {
      return;
    }    
    while (this.dataStore.length > 0) {
      this.dataStore.pop();
    }
  }
}
```

构造函数方法初始化底层数据存储结构，即`dataStore`数组。

堆栈的顶部总是底层数组的最后一个元素，所以我们使用公式从长度中减去 1 来返回顶部的元素。

数组中元素的数量是堆栈的大小，所以我们返回数组的长度作为存储的堆栈元素的数量。

如果堆栈中有 0 个元素(长度等于 0)，那么它是空的。

通过弹出数组中的所有元素来清除堆栈。

# 堆栈测试程序

在我演示使用堆栈来解决一些有用的问题之前，我将编写一个快速程序来测试所有的类方法。下面是测试程序:

```
let myStack = new Stack();
myStack.push("Mike");
myStack.push("Terri");
myStack.push("Meredith");
putstr("Top of stack: ");
print(myStack.top());
putstr("Number of elements on the stack: ");
print(myStack.size());
if (!myStack.empty()) {
  myStack.clear();
}
putstr("Top of supposedly empty stack: ");
print(myStack.top());
myStack.clear();
putstr("Number of elements on the stack: ");
print(myStack.size());
```

这是程序的输出:

```
Top of stack: Meredith
Number of elements on the stack: 3
Top of supposedly empty stack: undefined
Number of elements on the stack: 0
```

现在我已经完成了 Stack 类的演示，让我们继续使用这个类来解决一些问题。

# 将十进制数转换为二进制数

堆栈可用于将数字从一种基数转换到另一种基数。我将通过编写一个将十进制数(基数为 10)转换为二进制数(基数为 2)的程序来演示这一点。该算法的工作原理是，首先获取基数进行转换，然后将该值推送到堆栈上。然后你用这个数除以基数，这个商就是新的基数。

要获取转换后的数字，可以通过弹出堆栈来构建一个字符串，直到堆栈中不再有数字。

下面是使用`Stack`类将十进制数转换成二进制数的程序:

```
let digits = new Stack();
const base = 2;
putstr("Enter the base number: ");
let decimal = readline();
putstr("Decimal number: ");
print(decimal);
while (decimal != 0) {
  let digit = decimal % base;
  digits.push(digit);
  decimal = parseInt(decimal / base);
}
let converted = "";
while (!digits.empty()) {
  converted += digits.top();
  digits.pop();
}
putstr("Binary number: ");
print(converted);
```

下面是该程序几次运行的输出:

```
C:\js>js stack.js
Enter the base number: 123
Decimal number: 123
Binary number: 1111011C:\js>js stack.js
Enter the base number: 1024
Decimal number: 1024
Binary number: 10000000000
```

# 寻找回文

对于我使用堆栈的第二个例子，我将演示如何使用堆栈来确定一个单词是否是回文。回文是一个向前和向后拼写相同的单词。比如 racecar 是回文；鲍勃是一个回文；你好不是回文。

我最喜欢的回文实际上是这样一句话，当你去掉空格后，它就变成了回文:一个人计划修建巴拿马运河。

您可能很快就能看到如何使用堆栈来确定一个单词是否是回文。一个程序遍历一个字符串，提取每个字母并把它推到一个堆栈上。当你用完了所有的字母时，遍历堆栈，将每个字母弹出到一个新的字符串中。如果原始字符串和从堆栈中构建的字符串相同，则该单词是一个回文。

下面是一个程序，它使用堆栈来确定一个单词是否是回文:

```
let letters = new Stack();
putstr("Enter a word: ");
let originWord = readline();
putstr("Word: ");
print(originWord);
let stop = originWord.length;
for (let i = 0; i < stop; i++) {
  letters.push(originWord[i]);
}
let reversed = "";
while (!letters.empty()) {
  reversed += letters.top();
  letters.pop();
}
putstr("Reversed word: ");
print(reversed);
if (reversed == originWord) {
  print(originWord + " is a palindrome.");
}
else {
  print(originWord + " is not a palindrome.");
}
```

下面是该程序几次运行的输出:

```
C:\js>js stack.js
Enter a word: racecar
Word: racecar
Reversed word: racecar
racecar is a palindrome.C:\js>js stack.js
Enter a word: hello
Word: hello
Reversed word: olleh
hello is not a palindrome.C:\js>js stack.js
Enter a word: amanaplanacanalpanama
Word: amanaplanacanalpanama
Reversed word: amanaplanacanalpanama
amanaplanacanalpanama is a palindrome.
```

现在，我已经为您提供了两个如何使用堆栈解决问题的示例。如果你对更多涉及堆栈的问题感兴趣，可以在网上做一些搜索，探索如何使用堆栈将中缀算术方程转换为后缀方程，或者如何使用堆栈平衡语句中的符号，比如算术中使用的括号。

感谢您的阅读，请回复这篇文章或给我发邮件，告诉我您的意见和建议。