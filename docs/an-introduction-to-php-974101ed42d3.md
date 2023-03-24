# PHP 简介

> 原文：<https://levelup.gitconnected.com/an-introduction-to-php-974101ed42d3>

![](img/c0712537fab10a19578dcd33aff6c1cb.png)

PHP 是 web 开发中使用的一种流行的脚本语言。PHP 是一个缩写词，代表超文本预处理器。PHP 文件通常包含 HTML、CSS、JavaScript 和 PHP 代码，因此理解 HTML、CSS 和 JavaScript 对学习 PHP 很有帮助。PHP 用于创建动态网页，它可以从表单中收集数据，并在数据库中添加、修改或删除数据。一个 PHP 脚本在服务器中执行，一个 HTML 响应被发送到浏览器。

PHP 脚本可以写在 web 文档的任何地方。PHP 脚本标签编写如下。

```
<?php   ?>
```

让 PHP 和 HTML 相互交互的一个非常常见和重要的部分是 echo 命令。echo 命令允许将信息写入 HTML 文档。文本和 HTML 代码都可以在 PHP 标签中编写。

```
<?php
   echo("<h1>Hello World</h1>");
?>
```

上面的代码相当于直接在 HTML 代码的 h1 标记中编写“Hello World”。

# 变量

变量是一段有名字和值的信息。该名称从不改变，用于识别信息片段。变量的值可以改变。

声明变量的格式如下。

```
$variableName = variableValue
```

要使用变量，只需键入$后跟变量名。变量不是常量，可以用与上面相同的格式重写。

# 数据类型

PHP 有五种数据类型。第一个是字符串，它是纯文本。PHP 中使用了两种类型的数字。第一种是整数，是整数。整数可以是正数、负数或零。第二种类型的数字称为浮点数，是带小数的数字。PHP 中使用的另一种数据类型是布尔值，它可以是真或假。最后一个数据类型是 null，它用于没有值的情况。

# 使用字符串

## 评论

单行注释以#或//开头

```
# This is a comment.// So is this.
```

多行注释包含在/*和*/中。这种语法也可以用于在包含可执行代码的行中放置注释。

## 串联

串联意味着将多个字符串连接在一起。使用 PHP 时，有三种方法可以实现连接。

要将变量插入字符串，只需键入带有变量的字符串。不需要特殊的操作员。

```
$var = "variable"
“This string has a $var.” 
//This string has a variable.
```

多个字符串和变量可以使用。接线员。

```
$string1 = "Hello";
$string2 = $string1." "."World!"; 
//"Hello World!"
```

的。=运算符允许将一个字符串追加到另一个字符串。

```
 $string = "Hello ";
$string .= "World!";     
//"Hello World!"
```

## 字符串函数

PHP 内置了许多函数，可以用来轻松操作字符串。下面是一些最流行的字符串函数的基本指南。

strtolower:将文本转换成小写。

strtoupper:这将文本大写。

strlen:这代表字符串长度。它决定了字符串中有多少个字符。

str_replace:替换字符串的一部分。要替换字符串，请使用以下格式。

```
str_replace(“what you want to replace”, “what you want to replace this with”, $stringName)
```

substr:从字符串中提取子串。要提取子字符串，请键入以下内容。

```
substr($stringName, starting index, length(how many characters you want to grab))
```

索引:要在字符串的某个索引处查找字符，只需在变量名后的[ ]内键入索引。索引从 0 开始。

修改字符:要修改字符串中的字符，请键入变量，然后在变量名后的[ ]内键入索引，并将其设置为新的所需值。

# 使用数字

PHP 计算基本的加法、减法、乘法和除法。它还使用模数运算符计算表达式的余数。PHP 遵循正常的操作顺序。判断两个事物是否相等，使用===判断两个事物是否不相等，！==被使用。

PHP 也使用逻辑运算符，最常见的是“and”和“or”。例如，下面的第一行将确定两个条件是否都为真，第二行将确定其中一个是否为真。另一个常见的逻辑运算符是“not”。这些运算符的用法如下。

```
$x && $y
//x and y$x || $y
//x or y!x
//Not x
```

## 数字函数

PHP 还内置了许多函数，可以用来轻松处理数字。下面是一些最流行的数字函数的基本指南。

abs:这个函数决定一个表达式的绝对值。

```
abs(-5) = 5
```

pow:这个函数将一个数提升到另一个数的幂。例如，下面的函数是 2 的 3 次方。

```
pow(2,3)
```

sqrt:计算一个数的平方根。

马克斯:这决定了两个数字中哪一个更大。

min:这决定了两个数字中哪个更小。

round:这个函数将浮点数舍入到最接近的整数。

ciel:这将浮点数舍入到最接近的整数。

floor:与 ciel 函数相反，它将浮点数向下舍入到最接近的整数。

# PHP 中的数组

数组可以保存多条信息。将数组视为列表可能会有所帮助。数组的格式如下。

```
$array = array(“one”, 2, “three”, 4, true);
```

数组中的元素可以使用它们的索引来访问。数组索引从 0 开始。

```
$array[0] //“one”
```

这种索引方法可用于改变数组中的特定元素。数组中的第三个元素可以使用下面的方法重新分配。

```
$array[2] = 3//array(“one”, 2, 3, 4, true)
```

同样的方法也可以用于向数组中添加元素。

```
$array[5] = “puppies”//array(“one”, 2, 3, 4, true, “puppies”)
```

## 数组方法

PHP 有许多内置函数，可以用来确定数组的信息和操作数组。下面是一些最流行的 PHP 数组函数的基本指南。

通过将数组或数组变量放在括号内，可以使用以下所有数组方法。

count:这个方法决定一个数组中有多少个元素。

```
$array = array(“one dog”, “two dogs”, “red dog”, “blue dog”)count($array)
//4
```

sort():这个方法按照升序对数组进行排序。

rsort():与排序相反，这个方法按降序对数组进行排序。

# 关联数组

关联数组允许信息存储在键值对中。一个键值对表示如下。

```
“key” => ”value”
```

如果我们想做一个狗和它们的品种的关联数组，它看起来会像这样。

```
$dogs = array(“Baby” => ”poodle”, “Elvis” => ”yorkie”, “Shelley” => ”golden retriever”)
```

在普通数组中，使用元素的索引来访问元素。使用关联数组时，通过键访问值。例如，如果我们想检查我们的一只狗的品种，我们可以用下面的方法。

```
$dogs[“Baby”]//”poodle”
```

确保关联数组中的所有键都是唯一的是很重要的。否则，很难正确访问信息。

可以对普通数组做的许多事情也可以对关联数组做。可以用类似的方式更新值。唯一的区别是使用了一个键而不是一个索引。还有一些数组方法可以用于关联数组。

```
$dogs[“Shelley”] = “golden lab”//array(“Baby” => ”poodle”, “Elvis” => ”yorkie”, “Shelley” => ”golden lab”)count($dogs)
//3
```

和普通数组一样，PHP 也内置了关联数组的方法。以下是这些方法中最常见的一些。

asort():这个方法按照值对关联数组进行升序排序。

ksort():这个方法按照键对关联数组进行升序排序。

arsort():这个方法按照值对关联数组进行降序排序。

krsort():这个方法按照键对关联数组进行降序排序。

# 功能

函数是制作可重用代码块的一个好方法。在 PHP 中，函数采用以下形式。

```
function functionName(parameters){
   code to be executed
}
```

当调用一个函数时，只需在函数名后加一对括号。要使函数返回特定值，请用 return 语句结束函数。return 语句出现在函数的最末尾很重要，因为 return 语句会导致函数停止执行。这意味着 return 语句之后的任何代码都不会被执行。

# 包括 HTML

正如 PHP 文件可以在 HTML 文件中使用一样，HTML 也可以包含在 PHP 文件中。这是使用下面的格式完成的。

```
<?php  include “fileName.html”  ?>
```

一个文件中的 PHP 也可以包含在使用相同格式的另一个 PHP 文件中。值得注意的是，变量、函数和其他代码并不局限于任何一个文件。

一个变量可以在一个文件中使用，也可以在另一个文件中赋值。一个函数可以在一个文件中定义，在另一个文件中调用。只要文件包含在彼此之中，任何一点代码或信息都可以使用，不管它来自哪个文件。

能够将 PHP 代码插入到 HTML 文档中，将 HTML 文档插入到 PHP 代码中，以及将 PHP 代码插入到其他 PHP 文件中，这使得网站能够以更加模块化的方式创建。它允许开发人员创建各种可重用的组件，并在需要时简单地使用这些组件。

# 关于 PHP 的更多信息

要了解 PHP 中的面向对象编程，请查看下面的链接。

[](https://medium.com/@arieljakubowski/php-classes-and-objects-bde14a99139a) [## PHP 类和对象

### 用 PHP 编写面向对象代码的简单指南。

medium.com](https://medium.com/@arieljakubowski/php-classes-and-objects-bde14a99139a) 

要了解如何在 PHP 中使用表单，请查看下面的链接。

[](https://medium.com/@arieljakubowski/a-basic-guide-to-php-forms-7461e5217055) [## PHP 表单基础指南

### 这是一个使用 PHP 和 HTML 创建和集成表单以及使用 PHP 检索信息的简单指南…

medium.com](https://medium.com/@arieljakubowski/a-basic-guide-to-php-forms-7461e5217055)