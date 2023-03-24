# 如何在 Go 中使用模板

> 原文：<https://levelup.gitconnected.com/how-to-use-templates-in-go-2e8ddb09159>

## <title>从零升到`+英雄+ `</title>

![](img/9b98fbaf59f986350fba609c4796da31.png)

来自 [Pixabay](https://pixabay.com/illustrations/blueprint-technical-drawing-4056027/)

Go 已经成为现代科技生态系统中最受欢迎的通用编程语言之一。原因之一是我个人最喜欢的语言是因为它鼓励开发者“像程序员一样思考”。虽然有越来越多的精彩的库和包，但 Go 足够模块化，只使用标准库比依赖第三方更容易(如果不是更好的话)创建自己的定制编程解决方案。

这在模板中尤其明显，它允许您以动态但结构化的方式定制输出。模板可用于在网页上显示信息或向客户群发送电子邮件。在本教程中，我们将在以下部分介绍模板:

*   **Go 中模板的基础知识**
*   **将复合数据结构传递给模板**
*   **将函数传递到模板中**

在 Go 中进行 web 开发时，模板极其有用，所以让我们开始吧！

# Go 中模板的基础

模板允许我们动态显示数据输出。比方说，我们想在 HTML 的主体中插入一个变量，我们可以使用模板来实现。通过使用反勾号，我们可以将`first`和`last`变量插入到下面代码片段中的`<h1></h1>`标签中。

运行该程序将会给我们…

```
$ go build
$ ./main <!doctype html>
        <html lang="en">
        <head>
        <meta charset="utf-8">
        </head>
        <body>
        <h1>Izzy Miles thinks Go is awesome!</h1>
        </body>
        </html>

$
```

但这只能让我们到此为止。如果我们有许多模板要即时处理，那该怎么办？我们真的需要自己实现逻辑来解析每一个吗？这就是 Go 的标准库发挥作用的地方——让我们来看看。

## 带文本/模板包的模板

假设我们有多个想要动态访问的模板。使用包`text/template`我们可以高效地将文件解析为模板。我们需要采取两个步骤。第一个是解析一个文件以获得对一个或多个模板的引用。

然后，我们可以调用这些模板上的方法，比如`ExecuteTemplate`，它接受一个作者、文件名和一个数据对象作为参数。`ExecuteTemplate`然后将给定的模板应用于数据对象，然后将输出写入 writer 对象。让我们看一个例子。

首先让我们制作一些新文件，包括我们的`main.go`和一些模板文件。

**注意:**模板文件可以是任何名称和任何扩展名。由您决定是否在代码中引用它们，但标准是使用扩展名`.gohtml`。

```
$ touch main.go one.gohtml two.gohtml three.gohtml
```

然后，您可以随意填充模板文件。现在让我们来看看代码。

同样，我们需要做的第一步是解析模板。我们用`template.ParseFiles()`来做这件事，它获取我们想要解析的任何文件的相对路径。在检查是否有错误之后，我们可以调用`ExecuteTemplate`,它接受一个编写器、模板名和一个要写入的数据对象。我们没有任何数据，所以我们可以从`templateRef`对象中引用`"one.gohtml"`，该对象包含对我们解析过的模板的引用。

接下来，我们还可以使用一些漂亮的类似 UNIX 的方法，比如`ParseGlob`，它将解析任何匹配特定模式的模板。在这种情况下，任何带有`.gohtml`扩展名的模板都将被解析并存储在`templateRef`对象中。然后我们可以在上面看到的解析过的模板上调用`ExecuteTemplate`。

如果我们运行这个程序，我们会得到…

```
$ go run *.go
FIRST TEMPLATE
SECOND TEMPLATE
THIRD TEMPLATE
```

## 将数据传递给模板

很容易将任何数据传递给我们的模板。解析之后，我们可以简单地用任何一个*单个*数据填充数据字段。所以你可以传递一个 int，一个 string，或者我们将要看到的自定义数据类型！

我们所要做的就是在对`ExecuteTemplate()`的调用中指定数据类型，并在模板中指定数据应该放在哪里。我的`one.gohtml`模板是这样的:

```
FIRST TEMPLATE {{.}}
```

其中`{{.}}`符号将引用数据对象的当前状态。因此，如果您发送一个片的位，您将获得每个调用的片的当前版本。

相应的 Go 代码如下:

最后，如果我们运行这个程序，我们将得到以下结果:

```
$ go run *.go
FIRST TEMPLATE received a string object
```

您甚至可以将传递的数据分配给模板中的一个**变量**:

```
{{$myVar := .}}FIRST TEMPLATE {{$myVar}}
```

**注意:**只解析一次模板对效率很重要，所以在做任何进一步的工作之前，编写一个帮助器方法来首先解析所需文件的列表可能是个好主意。

您可以在此阅读更多关于`text/template`包的信息:

 [## 模板——Go 编程语言

### 包模板实现了用于生成文本输出的数据驱动模板。要生成 HTML 输出，请参见包…

golang.org](https://golang.org/pkg/text/template/#pkg-index) 

# 将复合数据结构传递到模板中

我们现在可以向模板传递简单的数据对象，这很好，但这只能让我们到此为止。我们还可以将数组、映射和结构之类的数据结构传递给模板，从而实现更精确、更强大的最终输出。让我们看一些例子。

## 将数组传递给模板

在这个程序中，我们简单地定义了一个名为`wizards`的字符串数组。然后，我们开始解析模板文件的第一步，获取它的指针引用`templateRef`。最后，我们在引用上调用`ExecuteTemplate`,同时也传递`wizards`数组作为我们的数据对象。

为了在模板中显示传递的数据，我们必须指定数据输出的结构。在`wizards.gohtml`文件模板中，我有下面的代码作为一个无序的 HTML 列表。我们可以通过使用关键字`range`开始循环传递的数据对象`.`，这需要放置`end`来结束循环。然后我们可以通过在循环`range`中再次引用*中的`.`来访问单个数组元素。*

```
<body>
    <ul>
        {{range .}}
        <li>{{.}}</li>
        {{end}}
    </ul>
</body>
```

如果我们用`wizards.gohtml`模板运行上面的 Go 代码片段，我们将得到以下输出:

```
$ go run *.go
<body>
    <ul>

        <li>Harry Potter</li>

        <li>Hermione Granger</li>

        <li>Ron Weasley</li>

    </ul>
</body>
```

## 将地图传递到模板中

`text/templates` Go 包为我们保持了良好的一致性。我们可以定义一个名为`wizards`的地图，把巫师的名字作为键，把他们魔杖的核心作为值。我们重复前面的步骤，并将映射传递给`ExecuteTemplate`方法调用。

然后，我们可以访问模板中的键值对，以获得最终输出。我们通过在当前数据对象`.`上再次使用`range`关键字来实现这一点，这为我们提供了变量对，然后我们显示这些变量对。

```
<body>
    <ul>
        {{range $key, $value := .}}
        <li>{{$key}} has a wand made of {{$value}}</li>
        {{end}}
    </ul>
</body>
```

运行上面的程序会输出…

```
$ go run *.go
<body>
    <ul>

        <li>Harry Potter has a wand made of Phoenix feather</li>

        <li>Hermione Granger has a wand made of Dragon heartstring</li>

        <li>Ron Weasley has a wand made of Unicorn hair</li>

    </ul>
</body>
```

## 将结构传递给模板

我最喜欢的 Go 属性之一是其自定义类型结构的简单性。这里我们定义了一个名为`wizard`的结构，它有两个属性`Name`和`WandCore`。然后，我们实例化一个名为`hp`的新`wizard`，像以前一样传递给我们的模板引用。

在我看来，自定义结构是模板中最容易格式化的。你所要做的就是引用你想要显示的结构属性，这样代码就变得可读性更好了！

```
<body>
    <ul>
        <li>{{.Name}} has a wand made of {{.WandCore}}</li>
    </ul>
</body>
```

运行程序给出了预期的输出..

```
$ go run *.go
<body>
    <ul>
        <li>Harry Potter has a wand made of Phoenix feather</li>
    </ul>
</body>
```

您可以利用 Go 中的模板来处理任何特定的情况，方法是使用`range`关键字遍历任何对象列表，使用`.`特殊字符引用单个数据对象的当前状态。通过这种方式，您可以循环遍历结构列表，以显示它们的属性或想到的任何其他组合！

# 将函数传递给模板

总结一下，也可以将函数传递给我们的模板。我们为什么要这么做？首先，你不希望在你的模板中做任何数据处理，那会导致高度耦合的代码。相反，在您处理了主 Go 代码逻辑中的数据之后，您可以调用模板中的函数来帮助*以更高的复杂度显示*数据。

为了将函数传递给我们的模板，`text/templates`包有一个名为`Funcs()`的方法，该方法需要一个名为`FuncMap`的数据类型，该数据类型包含字符串到函数的映射。我们定义了自己的`template.FuncMap{}`，称为`templateFuncMap`，它有两个函数`"upperCase"`和`"double"`，我们在第 11 行定义了这两个函数。

然后在第 22 行，我们链了几个值得理解的方法。首先，我们必须用`template.New("")`定义一个新模板，以便将我们的`templateFuncMap`传递给`Funcs()`方法调用。一旦我们有了包含函数的新模板，*然后*我们可以在各自的模板上调用`ParseFiles()`。如果我们试图马上调用`template.ParseFiles()`，我们会遇到一个先有鸡还是先有蛋的问题，我们无法获得一个定义了自定义函数的模板。

获得新的模板引用后，我们现在可以从模板中调用自定义函数:

```
<body>
    <ul>
        <li>{{upperCase .Name}}'s wand is {{double .WandCore}}</li>
    </ul>
</body>
```

运行上面的代码最终会给我们..

```
$ go run *.go
<body>
    <ul>
        <li>HARRY POTTER has a wand made of Phoenix featherPhoenix feather</li>
    </ul>
</body>
```

使用自定义函数，您可以添加更多动态行为来显示数据，就像真正的 Go web 开发人员一样。

我希望你喜欢阅读本教程，并且能够学习在 Go 中使用模板动态显示数据的强大功能。如果有任何不清楚的话题，或者你想要一篇新的文章，请在下面留下评论！感谢阅读。