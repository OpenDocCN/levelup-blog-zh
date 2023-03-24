# 学习 TypeScript:简介和入门

> 原文：<https://levelup.gitconnected.com/learning-typescript-introduction-and-getting-started-ad10ce54fe48>

![](img/d8dc1dcee01e8f81c448e7cd4edc08aa.png)

由 [Luca Martini](https://unsplash.com/@lucamartini?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这个系列文章*学习 TypeScript* 中，我将记录我学习 TypeScript 编程语言的经历。TypeScript 是 JavaScript 的超集，它提供了比 JavaScript 更严格的类型系统，减少了出现数据类型错误的机会，例如当您无意中试图将一个数字乘以一个字符串或其他一些数据类型不匹配时。

在本文中，我将向您展示如何在您的计算机上安装 TypeScript，以及如何使用 TypeScript 编写一些简单的程序。在那之后，我将通过描述 TypeScript 类型系统以及类型系统如何工作的例子来深入讨论。

我用 JavaScript 展示的例子是使用 Spidermonkey JavaScript 引擎编写的。

# 下载和安装 TypeScript

TypeScript 使用 Node.js (Node)系统运行，因此如果尚未安装 node，您的第一步是下载并安装它。请访问他们的网站[https://nodejs.org](https://nodejs.org)，获取在您的系统上安装节点的说明。

安装了 node 之后，您就可以下载并安装 TypeScript 了。打开命令行窗口，输入以下命令:

`npm install -g typescript`

您可以通过检查版本来确保安装了 TypeScript:

`tsc –version`

暂时就这样了。我们可以安装更多的支持程序，比如检查语法的 lint 程序，但是让我们从简单的开始，随着我们获得更多使用 TypeScript 的经验，我将从这里开始构建。

# 你好，世界！打字稿

先写一个经典的你好，世界！TypeScript 中的程序。首先，导航到您想要存储 TypeScript 程序的目录，打开一个文本编辑器(现在我只在我的 Windows 机器上使用 Notepad，尽管我将在本文的后面学习 VS 代码)，启动一个名为 helloworld.ts 的新文件，并输入下面的 TypeScript 代码:

```
let greeting: string = "Hello, world!"
console.log(greeting)
```

保存并关闭文件。现在转到命令行，输入以下命令:

`tsc helloworld.ts`

`tsc`是 TypeScript 编译器的名称。这里我调用了我的源文件上的编译器。如果程序编译成功，结果将是一个 JavaScript 文件——hello world . js。然后，我可以通过调用 node 和 JavaScript 文件名来运行该程序，如下所示:

`node helloworld.js`

结果显示在下一行:

`Hello, world!`

您应该注意的第一件事是 TypeScript 编译成 JavaScript 代码。但是即使我们生成的程序是用 JavaScript 代码编写的，编译器仍然对 TypeScript 程序进行类型检查，以确保在整个程序中使用了正确的类型(即使是像这样短的程序)。

这里有一个实验，您可以通过它来了解 TypeScript 编译程序的方式和 JavaScript“编译”程序的方式之间的区别。输入下面的 JavaScript 程序(可以用 node 运行):

```
let vals = [1,2,3];
let val = 2;
console.log(val * vals);
```

这个程序的结果是:

`NaN`

这意味着节点运行了程序，结果是`NaN`。现在将这个程序输入到一个 TypeScript 文件中并编译它:

```
let vals: number[] = [1,2,3]
let val: number = 2
console.log(vals * val)
```

不要担心变量是如何用类型声明的。稍后我将更详细地解释如何做到这一点。

以下是我尝试用 TypeScript 编译这个程序时得到的结果:

```
C:\Users\mmcmi\Documents\ts>tsc types.tstypes.ts:3:13 - error TS2362: The left-hand side of an arithmetic operation must be of type 'any', 'number', 'bigint' or an enum type.3 console.log(vals * val)~~~~Found 1 error.
```

程序无法编译，更不用说运行了，因为类型系统不允许数组与数字相乘。

# 打字稿中的打字简介

JavaScript 是一种*弱类型*语言。这意味着变量和对象可以通过赋值来改变数据类型。例如，我可以用 JavaScript 这样做:

```
let x = 1;
print(x); // displays 1
x = "Hello, world!";
print(x); // displays Hello, world!
```

弱类型语言很难调试，因为变量可以采用任何合法的 JavaScript 类型。

JavaScript 也是一种动态类型语言。这意味着当遇到一个类型时，该语言试图将它赋给一个值，但该语言并没有做太多的工作来强制类型安全。例如，如果您尝试将一个数字乘以数组元素，如下所示:

`let x = 4 * [1]; // y gets 4`

JavaScript 会将数组元素转换为数字 1 并执行乘法。当你试图将一个数字和一个字符串相乘时，也会发生同样的事情:

`let y = 10 * “10”; // y gets 100`

不能像这样混合数据类型是语言类型安全的一部分。当我在上面演示 TypeScript 如何在编译时识别类型错误时，我演示了 TypeScript 如何促进类型安全。

TypeScript 是一种强类型语言。TypeScript 允许您将数据类型赋给变量或对象的一部分。TypeScript 使用一种称为类型批注的技术来实现这一点。我在上一节的例子中也演示了这一点，但是我将在这里更完整地介绍这个概念。

以下是类型注释的一些示例:

```
let number :string = 100
let name :string = "Cynthia"
function square(num :number) :number {
  return num * num
}
```

类型添加在标识符之后(对于变量声明)或参数之后(在函数定义中)，以及函数签名的末尾，以指示函数的返回类型。一旦一个变量被标注了类型，这个变量就不能接受不同类型的值。如果您尝试这样做，将会出现编译时错误，如下例所示:

```
let num :number = 1
num = "two"
```

以下是错误消息:

```
test.ts:2:1 - error TS2322: Type 'string' is not assignable to type 'number'.2 num = "two"
  ~~~
```

您不必为 TypeScript 注释类型来对程序执行类型检查。TypeScript 还将根据被赋值的值的类型来推断数据类型。上面的例子可以不用类型注释写成:

```
let num = 1
num = "two"
```

编译器仍然会抱怨一个字符串被错误地赋给了一个数字。JavaScript 也做类型推断，但是是以弱方式而不是强方式，因为它不强制类型安全。

在我谈论类型推断的时候，让我再来区分一下 TypeScript 和 JavaScript。JavaScript 是一种*动态类型*语言，而 TypeScript 是一种*静态类型*语言。静态类型语言允许类型注释，例如在 TypeScript 中使用的类型注释，而动态类型语言在后台进行数据类型赋值。在 JavaScript 中，您可以使用`typeof`运算符查看变量的当前类型，如下例所示:

```
js> let x = 1;
js> print(typeof(x));
number
js> x = "Jane";
"Jane"
js> print(typeof(x));
string
js>
```

`x`的数据类型随着分配给它的不同数据类型而变化。这演示了动态类型。你也可以说 JavaScript 确实是运行时类型，因为数据类型是在运行时决定的，甚至是在程序运行的时候。

静态类型化意味着类型是在编译时决定的，一旦类型被建立，它就不能通过赋值被覆盖，正如我们在上面的错误例子中看到的。静态类型使程序更容易调试，因为您不必跟踪在程序中的任何特定点变量中存储了什么类型的数据。

正如我们所看到的，静态类型还可以防止您在尝试对混合数据类型执行非法操作时出错，例如尝试将一个字符串乘以一个数字。JavaScript 将执行这些操作，结果从`undefined`到`NaN`不等。

在这个例子中可以看到动态类型语言和静态类型语言的另一个区别。下面是一个有错别字的 JavaScript 程序:

```
let number = 5;
numbr = (number + 10) / 2; // user typed numbr instead of number
print(number); // displays 5 rather than 10
```

如果您尝试在 TypeScript 中运行此程序，您会得到以下错误消息:

```
test.ts:2:1 - error TS2552: Cannot find name 'numbr'. Did you mean 'number'?2 numbr = (number + 15) / 2
~~~~~
test.ts:1:5
1 let number = 5
      ~~~~~~
'number' is declared here.Found 1 error.
```

静态类型的语言帮助你发现错误，比如动态类型的语言所忽略的错误。对于一次性脚本来说，必须处理这个错误可能没什么大不了的，但是它可能会在大型项目中导致大量无效的调试时间。

我关于 TypeScript 的介绍性文章到此结束。在我的下一篇文章中，我将花一些时间向您介绍健壮的 TypeScript 类型系统以及如何使用它。'

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。