# 5 个隐藏的 C#特性，它们将真正提高你的工作效率

> 原文：<https://levelup.gitconnected.com/5-hidden-c-features-that-will-actually-boost-your-productivity-d192f7d66b27>

![](img/568a487f5458eda77e62e75b1ca79028.png)

如果你想学习一门新的语言，从基础开始。熟悉代码语法，代码文法，如何写语句(if，for，switch，…)，类，接口。

但是每一种编程语言都有自己的一套“隐藏”特性，小的快捷方式和酷的操作符，可以节省你的时间和代码行。您将写出更好、更简洁、可读性更强的代码。

在本文中，我将向您展示 C#的 5 个您可能不知道的特性。所以我们不要再浪费时间了，从第一点开始。

## 1)行内"`using"`语句

![](img/63700eec4c0aea6b88d35ea0bce5f172.png)

如果您不熟悉`using`语句，它定义了一个作用域，在该作用域的末尾将释放一个对象。这在安全执行 I/O 操作时特别有用，例如:

```
using (var connection = new SqlConnection(connectionString)
{
	connection.Open();
	[....]
}//Here, the connection is already disposed for you
```

C# 8.0 引入了一种新的编写`using`语句的方式。基本上，不用打开一个块，只需在稍后要处理的对象之前使用相同的关键字`using`。

```
using var connection = new SqlConnection(connectionString)
connection.Open();

[....]
```

不再有括号和缩进；代码现在可读性更好了。内联`using`的生存期将延长到声明它的范围的末尾。例如，如果您在函数中使用连接，当函数终止时，它将被释放。上面的示例代码与此相同:

```
var connection = new SqlConnection(connectionString)
connection.Open();

[....]connection.Close();
return;
```

## 2)速记“开关”语句

![](img/d16efc22c42b6098bc245fc79f634c5e.png)

我想你们都熟悉 switch 语句。您可以使用它来编写复杂的条件和分支操作

```
switch (light.Status)
{
	case ON:
		text = "Light is on";
		break;
	case OFF:
		text = "Light is off";
		break;
	default:
		text = "Unknown status";
		break;
}
```

但是在大多数情况下，您将使用 switch 语句将一个值映射到另一个值。这就是为什么从 C# 8.0 开始，您可以使用`switch expression`来计算候选表达式列表中的单个表达式。下面是同样的例子，用`switch expression`

```
text = light.Status switch
{
	ON => "Light is on",
	OFF => "Light is off",
	_ => "Unknown status"
}
```

你刚刚写了同样的东西，但是用了一半的行，并且用了一种更易读的方式。

## 3)零凝聚算子

![](img/174496220657e80eb8acead9593c50be.png)

当处理可空类型时，在对变量做任何事情之前总是检查变量是否为空是很重要的。我相信大多数人，包括我自己，都依赖 if 语句来实现这个目标:

```
variable = MightReturnNull();if(variable == null)
{
    variable = "default";
}
```

现在有一个更好的方法，如果不是`null`，那么`??`操作符返回其左边操作数的值；否则，它计算右边的操作数并返回结果。让我们用行动来展示这一点:

```
var variable = MightReturnNull() ?? "default";
```

简单对吗？同样的事情，只有一行代码。

甚至还有另一种操作符可以处理可空类型，即空合并赋值操作符。从 C# 8.0 开始，您可以使用`??=`操作符来替换表单的代码

```
if (variable is null)
{
    variable = expression;
}
```

与以下内容

```
variable ??= expression;
```

## 4)速记对象初始化

![](img/ab8653fb32647162408df73355ddaee2.png)

在 C#中，有两种标准的方法来声明变量。您可以编写值的类型，或者让编译器根据正确的赋值自动分配正确的类型。

```
Car myCar = new Car();
var myCar = new Car();
```

但是还有第三种声明变量的方法，使用简短的`new`关键字

```
Car myCar = new();
```

## 5)文件范围的命名空间

![](img/3c2abbf81139f58809498acdb7536c3d.png)

这是我最喜欢的，因为它减少了整个文件的代码缩进。我仍然很惊讶为什么微软之前没有添加这个功能，因为许多语言从一开始就有这个功能(比如 Java)。使用文件范围的命名空间，您可以替换以下形式的代码

```
namespace MyNamespace
{
    class MyClass
    {public void MyMethod()
        {

        }}
}
```

与以下内容

```
namespace MyNameSpace;class MyClass
{
    public void MyMethod()
    {

    }
}
```

这样更好，你不需要缩进大部分代码；更清晰，仪式感更少。

## 结论

在本文中，我介绍了 C#最隐藏的五大特性。有了这些小技巧，C#变得更加有用了。代码现在更加清晰、易读和简洁。尽管微软在实现新功能(比如文件范围的名称空间)方面非常缓慢，但我们现在仍然可以从中受益。

你已经知道所有这些特征了吗？请在评论中告诉我。