# JShell，Java REPL

> 原文：<https://levelup.gitconnected.com/jshell-the-java-repl-21ba403c2dc5>

## 简单的命令行原型和更多

![](img/28f44105ec98912757637b49c38cebe4.png)

照片由 [Ahmed Sobah](https://unsplash.com/@so_obarey?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/shell?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

快速运行一些代码而不启动一个成熟的 IDE 或者创建一个*临时*项目会非常有帮助。它可以用于构建代码原型，尝试一些新功能，或者运行一个更大项目的一小部分。

许多语言都包含一个 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) ，一个读取-评估-打印循环，它评估输入的声明、语句和表达式，并立即显示结果。有了 Java 9，我们终于也有了一个。

## 例子

为了更好地形象化这些例子，我避免使用 Gists。相反，我对所有用户输入都使用**粗体**，不强调 JShell 输出。以`#`开头的行是出于解释的目的添加的，并不是 JShell 输出的一部分。

# 基本用法

要启动一个新的 JShell 会话，只需在您喜欢的终端/命令行中键入`jshell`:

```
**jshell [options] [load-files]**
```

## 片段

JShell 评估你扔给它的所有东西:表达式、语句、导入或定义。这些 Java 代码块被称为“片段”。

```
# Expressions
jshell> **2 + 3**
$1 ==> 5# Statements
jshell> **var a = 2 + 3**
a ==> 5# Imports
jshell> **import java.time.***# Definitions
jshell> **boolean greaterZero(int i) {**
   ...> **return i > 0;**
   ...> **}**
|  created method greaterZero(int)
```

单行代码片段不需要分号。如果我们定义一个方法或类，我们仍然需要分号来表示花括号中的内容。

## 临时变量

代码片段返回的任何值都会自动保存到一个名为`$<number>`的“临时变量”中。现在表达式的结果可供以后使用。

要列出活动会话的所有变量，键入`/vars`，这将打印所有临时和非临时变量:

```
jshell> **2 + 3**
$1 ==> 5jshell> **String.valueOf($1)**
$2 ==> "5"jshell> **String nonScratch = "value"**
nonScratch ==> "value"jshell> **/vars**
|    int $1 = 5
|    String $2 = "5"
|    String nonScratch = "value"
```

## 进口

默认情况下，只有一小部分 JDK 会自动导入。键入`/imports`列出当前 JShell 会话的所有导入的包和类:

```
jshell> **/imports**
|    import java.io.*
|    import java.math.*
|    import java.net.*
|    import java.nio.file.*
|    import java.util.*
|    import java.util.concurrent.*
|    import java.util.function.*
|    import java.util.prefs.*
|    import java.util.regex.*
|    import java.util.stream.*
```

我们可以导入其他包和类，就像在“普通”Java 代码中一样:

```
jshell> **import java.time.***jshell> **import static java.util.stream.Collectors.***
```

或者，我们可以指示 JShell 在启动时使用预定义的加载文件`jshell JAVASE`加载一组不同的包

这将从 JDK 装载大约 170 个不同的包，而不仅仅是最少的。

## 基于上下文的自动导入

没有人想把处理 Java 代码所需的所有导入都打出来。JShell 为您提供了保护，这是一件好事！

要导入一个类型，我们可以在任何类型的末尾按下`shift-tab i`, JShell 会尝试根据上下文找到正确的类型:

```
#                    cursor position
#                          ▼
jshell> **var now = LocalDate.now() <shift-tab i>**
0: Do nothing
1: import: java.time.LocalDate
Choice: **1**
```

并不总是会找到导入候选项。但是大多数时候，它会让使用 JShell 变得更容易。

## 变量表达式

另一个方便的快捷方式是`shift-tab v`，它将一个表达式转换成一个变量创建语句:

```
jshell> **3 + 3 <shift-tab v>**
# New cursor position
#           ▼
jshell> int  = 3 + 3
```

变量类型会被自动推断和插入，只要给它一个名字，按`enter`，你就把你的表达式转换成一个有名字的变量了。

# 标签的力量

标签完成功能非常强大，可以用来完成代码片段和命令。多次按下`tab`可增加提供的信息量。

```
jshell> **System.out.print <tab>**
print(     printf(    println( jshell> System.out.print
```

如果我们找到了正确的方法，我们可以通过在括号后再次按下`tab`来查看所有可用的签名:

```
jshell> **System.out.println(**
Signatures:
void PrintStream.println()
void PrintStream.println(boolean x)
void PrintStream.println(char x)
void PrintStream.println(int x)
void PrintStream.println(long x)
void PrintStream.println(float x)
void PrintStream.println(double x)
void PrintStream.println(char[] x)
void PrintStream.println(String x)
void PrintStream.println(Object x)<press tab again to see documentation>jshell> System.out.println(
```

如输出所示，再次按下`tab`(一次又一次)将循环浏览每个签名的文档。

## 方法和类

方法和类可以“照常”声明:

```
jshell> **String concat(String a, String b) {**
   ...> **return a + b;**
   ...> **}**
|  created method concat(String,String)jshell> **class SimplePojo {**
   ...> **private String value;**
   ...> **String getValue() {**
   ...> **return this.value;**
   ...> **}**
   ...> **void setValue(String newValue) {**
   ...> **this.value = newValue;**
   ...> **}**
   ...> **}**
|  created class SimplePojojshell> **var pojo = new SimplePojo()**
pojo ==> SimplePojo@67424e82jshell> **pojo.setValue("a value")**jshell> **pojo.getValue()**
$7 ==> "a value"
```

## 向前引用

由于*前向引用*，我们的方法和类可以使用变量和其他尚未声明的构造。不过，JShell 警告我们:

```
jshell> **void forwardReference() {**
   ...> **System.out.println(notYetDeclared);**
   ...> **}**
|  created method forwardReference(), however, it cannot be invoked until variable notYetDeclared is declaredjshell> **forwardReference()**
|  attempted to call method forwardReference() which cannot be invoked until variable notYetDeclared is declaredjshell> **var notYet = "variable is declared"**
notYetDeclared ==> "variable is declared"jshell> **forwardReference()**
variable is declared
```

这使我们能够更容易地设置我们的环境，而不是在创建方法之前确保一切就绪。

## 控制流

支持 Java 控制流语句:

*   `if` - `else`
*   `? :`三元运算符
*   `for`和`while`-循环
*   `switch`报表

# 历史

有多种方法可以处理以前的代码片段。

## 列表

命令`/history`将以纯文本显示当前活动会话的所有片段和命令。

```
jshell> **2 + 4**
$1 ==> 6jshell> **String test = "42"**
test ==> "42"jshell> **/history**2 + 4
String test = "42"
/history
```

相反，使用`/list`,我们得到一个代码片段列表，而不是命令，以及它们对应的 ID。

```
jshell> **2 + 4**
$1 ==> 6jshell> **String test = "42"**
test ==> "42"jshell> **/list** 1 : 2 + 4
   2 : String test = "42";
```

该 ID 可以在其他命令中使用。

## 搜索

JShell 支持向后和向前搜索:

*   `ctrl-r` -向后
*   `ctrl-s` -向后

# 命令

我们已经了解了一些可用的命令，比如`/vars`和`/list`。

许多命令都有不同的选项。我们可以通过键入`/?`或`/help`来查看它们，以获得简短的概述。有关命令的详细解释，请使用`/? <command>`或`/help <command>`。

## 命令列表

```
 Command            | Description
--------------------|------------------------------------
 /!                 | Rerun last snippet
 /-<n>              | Rerun the last <n>-th snippet
 /<id>              | Rerun specific snippet
                    |
 /list              | List source of session
 /vars              | List variables
 /types             | List types
 /methods           | List methods
 /imports           | List imports
                    |
 /history           | Plain text history
 /drop <name or id> | Delete source entry
 /edit              | Open all source in editor
 /edit <id>         | Open specific source in editor
 /open <file>       | Open file as source input
 /save <file>       | Save snippets to <file>
                    |
 /reload            | Reset and replay each snippet
 /reset             | Reset JShell
 /env               | View or change the evaluation context
 /set <key> <value> | Configure JShell
 /exit              | Quit JShell
```

## 命令缩写

我们不需要输入完整的命令，只需让它具有唯一性即可:

```
jshell> **/set feedback verbose**
```

和...相对

```
jshell> **/se fe v**
```

`/s`对`/set`来说还不够，感谢`/save`。

# 外部代码

Java 提供了很多功能，但是能够在 JShell 中使用任何 JAR 文件使它成为一个强大的原型工具。

## 类路径

我们可以在启动时指定类路径:

```
**jshell --class-path [directory or JAR files]**
```

或者我们可以在活动会话期间设置它:

```
jshell> **/env --class-path [directory or JAR files]**
```

注意，会话将被重置，所有不在启动脚本中的内容都将消失。

## Java 9 模块

[模块](https://www.oracle.com/corporate/features/understanding-java-9-modules.html)的用法类似于使用类路径:

```
**jshell --module-path [module path] --add-modules [module]**
```

这也可以在活动会话中设置:

```
jshell> **/env --module-path [module-path] --add-modules [module]**
```

# 配置

在`/set`的帮助下，我们可以按照自己的喜好配置 JShell。

## 基本配置

```
 Config key /values | Description
--------------------|--------------------------------------
 editor <command>   | Specify editor for "/edit"
 start <file>       | Contents of file will become default
                    | snippets/commands at startup
 feedback <mode>    | Verbosity of feedback
 <none>             | Display editor, start and feedback
```

像其他命令一样，`editor`有额外的选项，你可以用`/help /set editor`查看。

## 坚持

默认情况下，所有`/set`命令仅在当前会话期间设置。要永久设置配置选项，我们需要在末尾添加参数`-retain`。

但是要注意，并不是每个保留的选项都可以像编辑器选项一样用`-delete`删除。我试图找出 JShell 保存其配置的位置，但找不到，即使在检查了[源代码](https://github.com/openjdk/jdk/blob/master/src/jdk.jshell/share/classes/jdk/jshell/tool/package-info.java)之后也找不到。

## 高级配置

我们可以非常详细地配置 JShell 提供的输出。但是这会使这篇文章的长度增加两倍或三倍。如果您对配置输出感兴趣，您应该查看一下`/set mode`和`/set format`选项。

[JShell 反馈模式文档](https://docs.oracle.com/javase/9/jshell/feedback-modes.htm#JSHEL-GUID-906D1D0B-76AD-4AFD-9229-96C6FD823B4A)(甲骨文)

# 使用脚本

## 启动脚本

如前所述，JShell 加载了一个默认的*启动脚本*，为会话准备有用的导入。

我们可以在启动时使用我们自己的脚本，按照我们喜欢的方式准备它:

```
**jshell --startup [script]**
```

为了使用多个启动脚本，我们需要使用参数`--start`来代替:

```
**jshell --start [script] --start [script]**
```

或者我们可以用`/set start -retain [scripts]`来代替。

有 3 个内置的启动脚本可用:

*   `DEFAULT`
    加载最小导入量，在没有指定加载文件时使用。
*   装载大约 175 个不同的 JDK 包。
*   `PRINTING`加载与`DEFAULT`相同的导入，但也添加了多个`print`便利方法。

当会话重置时，启动脚本始终运行:

*   初次起动
*   `/reset`
*   `/reload`
*   `/env`

## 加载脚本

也可以将脚本加载到会话中:

```
**jshell [load-file]**
```

或者在会话中使用`/open [load-file]`。

与启动脚本不同，它不会在会话重置时重新加载。

# 资源

*   JShell 用户指南(甲骨文)
*   [JShell 教程](http://cr.openjdk.java.net/~rfield/tutorial/JShellTutorial.html) (OpenJDK)
*   JEP 222 (OpenJDK)
*   [Java 9 —探索 REPL](https://www.baeldung.com/java-9-repl) (Baeldung)
*   [IntelliJ IDEA 中的 JShell 控制台](https://www.jetbrains.com/help/idea/jshell-console.html)