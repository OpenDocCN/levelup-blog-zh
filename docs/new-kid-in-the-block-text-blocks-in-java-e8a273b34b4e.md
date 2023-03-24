# Java 中的文本块

> 原文：<https://levelup.gitconnected.com/new-kid-in-the-block-text-blocks-in-java-e8a273b34b4e>

## Java 基础

## 创建多行字符串的更好方法，无需添加冗长的代码

![](img/3832e4189fb191f5a7792decc715ad5e.png)

萨法尔·萨法罗夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与其他编程语言相比，Java 有点冗长。许多人和公司喜欢在他们的生产环境中使用 Java，因为有可用的支持和技能。尽管如此，它仍然是一种较老的编程语言，Java 在最近几年加快了它的步伐。除了提高性能，它还增加了一些通常与 Java 无关的特性，比如 lambdas。有了这些新特性，它试图让程序员的工作变得更容易，同时减少冗长的代码。其中一个尝试是引入“文本块”，在 JDK 13 和 14 中作为预览功能推出。计划在 2015 年 JDK 发布预览版，将于 9 月 20 日发布，OpenJDK 将于 3 月 21 日发布。尽管如此，如果你使用的是 JDK 13 或 14，你仍然可以使用这个功能。

# 多行字符串

![](img/bcfc7f033ff1823c86777ae3953335ce.png)

照片由[布鲁斯·马尔斯](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

多行字符串是跨越多行的文本块。为此，您需要在文本中添加行分隔符，这样编译器就能理解如何呈现该字符串。

```
String myMultiLineText = "This is first line of my multi-line string.\nI'm on second line now";
```

这将产生以下输出:

```
This is first line of my multi-line string.
I'm on second line now!
```

在这里，通过添加“\n”，我在字符串中添加了一个新行。但是，实际上，行分隔符依赖于操作系统。因此，如果您使用的是 Windows/Mac OS 9 或更早版本，您可能必须使用“\r”或“\r\n”作为转义序列。

```
Windows: '\r\n'
Mac (OS 9-): '\r'
Mac (OS 10+): '\n'
Unix/Linux: '\n'
```

现在，为了使我的代码更加通用，我必须考虑这样的情况。Java 有一种从系统属性中获取行分隔符的方法。这是更新后的代码-

```
String newLineChar = System.getProperty("line.separator");
String myMultiLineText = "This is first line of my multi-line string."+newLineChar+"I'm on second line now";
```

好的，很快，我的简单代码变得更加冗长，看起来混乱不堪。想象一下多行字符串有多行(> 5 行)！对眼睛不太好，对吧。我们可以使用像 concat，join 这样的字符串方法，或者使用像 StringBuilder，Guava Joiner 这样的库来使它变得更简洁。

## 使用字符串连接/联接

```
String myMultiLineTextUsingConcat = "first line"
.concat(newLineChar)
.concat("second line");String myMultiLineTextUsingJoin = String.join(newLineChar, "first line", "second line");
```

## 使用 StringBuilder

```
StringBuilder myMultiLineStringBuilder = new StringBuilder()
.append("first line")
.append(newLineChar)
.append("second line")
.toString();
```

## 使用番石榴连接器

```
String myMultiLineTextUsingGuavaJoiner = Joiner.on(newLineChar)
.join(ImmutableList.of("first line", "second line"));
```

因此，有许多方法可以创建这样的多行字符串，其中取决于用例，一种可能比另一种更好。但是，你可以看到，这些看起来干净或者易读的东西，都不会导致更容易出错的代码。

# 文本块

![](img/7e20d0b746cd63f28b91e0448540a5dd.png)

由 [Romain Vignes](https://unsplash.com/@rvignes?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

文本块功能允许您以一种直观的方式创建多行字符串，这是它应该有的方式。

> 文本块是多行字符串文字，它避免了对大多数转义序列的需要，以可预测的方式自动格式化字符串，并在需要时让开发人员控制格式。

我们的多行字符串现在变成了-

```
String myMultiLineText = """
                         first line
                         second line
                         """;
```

这使得它更简单，非常容易阅读和理解。您只需要将您的字符串括在分隔符*s 内，这是一个由三个双引号字符(`"""`)组成的序列。*

与字符串中的字符不同，内容可以直接包含双引号字符。允许在文本块中使用`\"`,但这不是必需的，也不推荐使用。选择厚分隔符(`"""`)是为了让`"`字符可以不转义，也是为了在视觉上区分文本块和字符串文字。

在运行时，文本块被评估为`String`的实例，就像字符串一样。从文本块派生的`String`实例与从字符串派生的实例无法区分。

## 刻痕

请注意缩进，我缩进了字符串，这样我就可以在输出中得到想要的间距。这会产生以下输出-

```
first line
second line
```

但是，如果我想在第二行加一个空格。我可以通过-

```
String myMultiLineText = """
                         first line
                          second line
                         """;
```

根据开始定界的位置，编译器试图理解间距的意图，以便您可以正确地缩进代码，并向文本块添加空格。想象你在字符串中创建 HTML 代码-

```
String html = """
              <html>
                  <body>
                      <p>Hello, world</p>
                  </body>
              </html>
              """;
```

输出的开头没有多余的空格，代码也正确地缩进了。

```
<html>
    <body>
        <p>Hello, world</p>
    </body>
</html>
```

下面是一个 HTML 示例，使用点来可视化为缩进添加的空格:

```
String html = """
..............<html>
..............    <body>
..............        <p>Hello, world</p>
..............    </body>
..............</html>
..............""";
```

所有这些点在编译时处理过程中都被删除了，编译器认为你添加这些点是为了缩进你的代码。

## 格式化

通常，你会想在字符串中插入运行时间值，比如-

```
String welcomeText = String.format("Welcome, %s !", name);
```

您仍然可以使用字符串格式方法来格式化文本块-

```
String welcomeText = String.format("""
                                   Welcome, %s !
                                   """, name);
```

为了避免这种冗长的构造，有一个新的方法*格式化*可以帮助解决这个问题。

```
String welcomeText = """
                     Welcome, %s !
                     """.formatted(name);
```

好多了，对！

![](img/98323196e48f07cd4ced09aab30fb021.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

总的来说，文本块是 Java 中一个很棒的特性，它提供了很多创建多行字符串的功能。将这一点添加到您的代码中，肯定会使您的代码更易读、更干净。开始尝试，享受乐趣！