# 在 Golang 中使用正则表达式指南

> 原文：<https://levelup.gitconnected.com/guide-to-using-regular-expressions-in-golang-18a821ba1c16>

![](img/0b8797c6ba32859e87c147a16b26a639.png)

由 [Sandrachile 拍摄的照片。](https://unsplash.com/@sandrachile?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/alphabet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

正则表达式是定义和使用字符串搜索模式以及在软件中构建查找/替换功能的一种广泛使用的方式。然而，处理它们有时会因为数百个库和框架造成的混乱而感到沮丧，每个库和框架使用稍微不同的方法来处理正则表达式。

然而在 Golang 中，内置的`regexp`包包含了正则表达式的所有用例，非常健壮，使用起来非常直观。

*让我们来看看* `*regexp*` *包的各种功能。*

## 检查字符串中的模式匹配

使用`MatchString`方法在字符串中寻找模式。第一个参数是要搜索的正则表达式，第二个参数是输入字符串。

```
found, err := regexp.MatchString("g([a-z]+)ng", "golang")
fmt.Printf("found=%v, err=%v", found, err)
```

这为我们提供了以下输出:

```
found=true, err=<nil>
```

它返回一个布尔值，表明模式的存在，如果正则表达式编译失败，则返回一个错误值。

将无效的正则表达式传递给`MatchString`方法会产生错误。

```
found, err := regexp.MatchString("g(-z]+ng  wrong regex", "golang")
fmt.Printf("found=%v, err=%v", found, err)
```

这给了我们:

```
found=false, err=error parsing regexp: missing closing ): `g(-z]+ng  wrong regex`
```

## 重用正则表达式

使用内置的`Compile`函数将正则表达式存储在一个变量中。

```
myRegex , err := regexp.Compile("g([a-z]+)ng")
```

`Compile`返回一个指向已创建的`RegExp`对象的指针，一个错误表示 regex 中的一个编译错误。

`myRegex`变量现在可以在任何操作中重复使用。例如，我们可以在`myRegex`实例上使用`MatchString`函数。

```
found := myRegex.MatchString("golang")
fmt.Printf("found=%v", found)
```

这给了我们:

```
found=true
```

另一种选择是函数`MustCompile`。这仅返回一个参数并丢弃错误返回。如果提供的字符串没有编译成正则表达式，`MustCompile`函数会使应用程序死机。

```
reg := regexp.MustCompile("g([a-z]+)ng")
fmt.Println(reg.MatchString("golang"))
```

以上工作与`Compile`类似。然而，像这样给`MustCompile`提供一个无效的正则表达式字符串，会产生混乱。

```
reg := regexp.MustCompile("g([az]+ng") //Invalid regex
fmt.Println(reg.MatchString("golang"))
```

这会产生:

```
regexp.MustCompile(0x10f5b80, 0x9, 0x119f100)
        /usr/local/Cellar/go/1.13.5/libexec/src/regexp/regexp.go:311 +0x152
```

## 使用正则表达式进行单元素搜索

使用`FindString`功能找到给定模式的第一个或最左边的匹配。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
found := myRegex.FindString("The best language is golang")
fmt.Printf("found=%s", found)
```

输出:

```
found=golang
```

如果不匹配，结果是一个空字符串。

要获得第一个匹配的子字符串的索引，使用`FindStringIndex`方法。它返回两个元素的整数切片，定义正则表达式最左边匹配的位置。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
found := myRegex.FindStringIndex("We all like golang because it is cool")
fmt.Printf("start=%d, end=%d", found[0], found[1])
```

输出:

```
start=12, end=18
```

如果不匹配，则返回一个空切片。

`FindStringSubmatch`将提供给定模式的最左边的匹配，以及该匹配中的子匹配。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
found := myRegex.FindStringSubmatch("We all like golang because it is cool")
fmt.Printf("found  =%v", found)
```

输出:

```
found  =[golang ola]
```

它提供了一段字符串，在本例中有两个元素。第一个元素用于匹配`g([a-z]+)ng`，第二个元素用于内部`([a-z]+)`。

与`FindString`和`FindStringIndex`类似，`FindStringSubmatch`也有相应的`FindStringSubmatchIndex`。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
found := myRegex.FindStringSubmatchIndex("We all like golang because it is cool")
fmt.Printf("found  =%v", found)
```

输出:

```
found  =[12 18 13 16]
```

## 使用正则表达式进行多元素搜索

使用`FindAllString`在给定的字符串中查找模式的所有匹配。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
found := myRegex.FindAllString("Can golang be called godlang?", -1)
fmt.Printf("found  =%v", found)
```

第二个参数是指定所需的匹配计数。`-1`表示指定无计数。

输出:

```
found  =[golang godlang]
```

如下所示，`FindString`中讨论的所有变体在`FindAllString`中也可用。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
found1 := myRegex.FindAllString("Can golang be called godlang?", -1)
fmt.Printf("found1  =%v \n", found1)
found2 := myRegex.FindAllStringIndex("Can golang be called godlang?", -1)
fmt.Printf("found2  =%v \n", found2)
found3 := myRegex.FindAllStringSubmatch("Can golang be called godlang?", -1)
fmt.Printf("found3  =%v \n", found3)
found4 := myRegex.FindAllStringSubmatchIndex("Can golang be called godlang?", -1)
fmt.Printf("found4  =%v \n", found4)
```

相应的输出:

```
found1  =[golang godlang] 
found2  =[[4 10] [21 28]] 
found3  =[[golang ola] [godlang odla]] 
found4  =[[4 10 5 8] [21 28 22 26]]
```

## 使用正则表达式进行修改

使用`ReplaceAllString`函数用不同的子字符串替换正则表达式的所有匹配项。

```
myRegex, _ := regexp.Compile("g([a-z]+)ng")
altered := myRegex.ReplaceAllString("Can golang be called godlang?", "python")
fmt.Println(altered)
```

输出:

```
Can python be called python?
```

如您所见，所有正则表达式匹配都被字符串`python`替换了。

我希望本指南能作为一个简单的入门，让你轻松地使用 Golang 中的正则表达式。