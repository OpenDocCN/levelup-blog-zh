# JavaScript 标签语句

> 原文：<https://levelup.gitconnected.com/javascript-label-statement-ddba111ce5a8>

![](img/cfe548a8fb3354523c4f7db3fc2ed4b8.png)

照片由[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在我的[另一篇文章](https://deddytandean.medium.com/javascript-break-and-continue-statements-661d645a537d)中，我分享了关于`break`和`continue`的陈述，其中似乎有所遗漏或不完整。

如果你注意到，这些语句只能在一个循环中使用，或者说，只能由嵌套循环中最里面的循环使用。那么，这是否意味着这两个语句不能针对外部循环？难道不能用它们来脱离父循环或者跳过父循环的一次迭代而不是它们所在的循环吗？

JavaScript 有一个相对未知的功能，它允许我们识别一个循环。这种标识允许这两条语句以嵌套循环中的任何循环为目标。这是`label`语句，它允许我们确定一个可以引用的语句。

它可以应用于任何语句。一旦标记完成，它就作为一个标识符在程序的其他地方被引用。

要标记 JavaScript 语句，只需在语句前加上一个`label`名称和一个冒号，然后是语句本身。

语法:

```
label:
statements
```

描述:

*   标签—作为标识符命名
*   语句—任何 JavaScript 语句

举个例子，你可以用一个标签来标识一个循环，然后用`break`或`continue`语句来指示你的程序是想要中断这个被标识的循环还是继续执行。

示例:

```
markedLoop:
    while (theMark === true){
        doSomething();
    }
```

然后，`break`和`continue`语句都可以通过处理它们的标识符来定位某些循环。语法如下。

语法:

```
break *labelName;* continue *labelName;*
```

“continue”语句(没有标签引用)只能用于跳过最内层封闭循环语句的当前迭代，并在下一次迭代中继续执行循环。与“中断”不同，它不会完全终止循环的执行。

在“while”循环中，它跳回到条件。在“for”循环中，它跳转到“最终表达式”。

“break”语句(没有标签引用)只能用于立即终止最内部的封闭循环或开关，并将控制转移给下面的语句。

有了标签引用，break 语句可以用来跳出任何代码块；它终止指定的封闭标记语句。

示例:

```
var cars = ["BMW", "Volvo", "Saab", "Ford"];
var text = "";list: {
    text += cars[0] + "<br>";
    text += cars[1] + "<br>";
    break list;
    text += cars[2] + "<br>";
    text += cars[3] + "<br>";
}document.getElementById("demo").innerHTML = text;// Output:
BMW
Volvo
```

从上面的例子可以看出，有一个“list”标签，其中有一串字符串连接。然而，在它的中间，有一个针对标签的中断。因此，对于输出，它只将四个单词中的两个连接起来。

另一个例子:

```
let x = 0;
let z = 0;labelCancelLoops:
  while (true) {
    console.log('Outer loops: ' + x);
    x += 1;
    z = 1;
    while (true) {
      console.log('Inner loops: ' + z);
      z += 1;
      if (z === 5 && x === 3) {
        break labelCancelLoops;
      } else if (z === 5) {
        break;
      }
    }
  }// Output:
Outer loops: 0
Inner loops: 1
Inner loops: 2
Inner loops: 3
Inner loops: 4
Outer loops: 1
Inner loops: 1
Inner loops: 2
Inner loops: 3
Inner loops: 4
Outer loops: 2
Inner loops: 1
Inner loops: 2
Inner loops: 3
```

对于上面的例子，我们可以看到，当`x`值为 3、`z`值为 5 时，它会跳出外 while 循环。否则，当`x`值不满足时，它将退出内部 while 循环。

当与“标签”语句一起使用时，它与“继续”语句的工作方式类似。

示例:

```
let i = 0;
let j = 10;checkIandJ:
  while (i < 4) {
    console.log(i);
    i += 1;
    checkj:
      while (j > 4) {
        console.log(j);
        j -= 1;
        if ((j % 2) === 0) {
          continue checkj;
        }
        console.log(j + ' is odd.');
      }
    console.log('i = ' + i);
    console.log('j = ' + j);
  }// Output:
0
10
9 is odd.
9
8
7 is odd.
7
6
5 is odd.
5
i = 1
j = 4
1
i = 2
j = 4
2
i = 3
j = 4
3
i = 4
j = 4
```

标记为`*checkIandJ*` 的语句包含标记为`*checkj*`的语句。如果遇到 continue，程序终止当前`*checkj*`的迭代，开始下一次迭代。每次遇到 continue，`*checkj*` 重复，直到其条件返回 false。当返回 false 时，`*checkIandJ*`语句的剩余部分完成，并且`*checkIandJ*`重复直到其条件返回 false。当返回 false 时，程序继续执行`*checkIandJ*`之后的语句。如果 continue 有一个标签`*checkIandJ*`，程序将在`*checkIandJ*`语句的顶部继续运行。

您可以使用多个标签，就像上一个示例中演示的那样。标签似乎对循环断路器有用。如果有嵌套循环(循环中的循环),那么它们特别有用，使用这些标识符，您可以有条件地指定何时以及从哪个循环中退出。

目前`label`到此为止。希望这篇文章对你有帮助。如果你认为这篇文章是有帮助的，并且可以帮助其他人，请分享给他们阅读。也欢迎你的想法和评论！

感谢阅读~