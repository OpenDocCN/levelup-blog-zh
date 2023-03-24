# StackOverflow 上 8 个投票最高的 Golang 问题和答案

> 原文：<https://levelup.gitconnected.com/8-highest-voted-golang-questions-and-answers-on-stackoverflow-9478e99bab4e>

## **Cherry** - **pick** 关于 Golang 编程语言栈溢出的投票最高的问题和答案

![](img/5e0983d3e34c4686b6a2b9706da42f92.png)

图片来自 [go.dev](https://go.dev/blog/gopher)

# 目录:

1.  [如何检查一张地图是否包含 Go 中的关键？](#be07)
2.  [如何高效串联字符串？](#904d)
3.  [如何将 int 值转换成字符串？](#0bce)
4.  [如何连接两个切片？](#dc40)
5.  [如何表示一个枚举？](#c7aa)
6.  [如何检查文件是否存在？](#a7ec)
7.  [如何获取对象的类型？](#682f)
8.  [如何将字符串转换成整数类型？](#d806)

# 1.如何检查一张地图是否包含 Go 中的关键？

**链接**:[https://stack overflow . com/questions/2050 391/how-to-check-if-a-map-contains-a-key-in-go](https://stackoverflow.com/questions/2050391/how-to-check-if-a-map-contains-a-key-in-go)

**回答:**

```
package main

import "fmt"

func main() {
	dict := map[string]int{"a": 8, "b": 9, "c": 10}

	if val, ok := dict["a"]; ok {
		// do something
		fmt.Printf("%v, %v\n", val, ok)     // 8, true
	}
}
```

**解释:**

*   `val`将接收来自地图的值“a”或“零值”。
*   `ok`将收到一个布尔值，如果“foo”出现在地图上，它将被设置为`true`
*   `if`Go 中的语句可以包括初始化语句和条件

# 2.如何有效地连接字符串

**链接**:[https://stack overflow . com/questions/1760757/how-to-efficient-concatenate-strings-in-go](https://stackoverflow.com/questions/1760757/how-to-efficiently-concatenate-strings-in-go)

**回答**:

```
package main

import (
	"fmt"
	"strings"
)

func main() {

	var sb strings.Builder

	for i := 0; i < 1000; i++ {
		sb.WriteString("a")
	}

	fmt.Println(sb.String())
} 
```

**解释:** `strings.Builder`是实现`io.Writer`接口和`io.WriterString`接口的类型。后者用于将字符串追加到缓冲区中。

# 3.如何将 int 值转换为字符串

**链接**:[https://stack overflow . com/questions/10105935/how-to-convert-an-int-value-to-string-in-go](https://stackoverflow.com/questions/10105935/how-to-convert-an-int-value-to-string-in-go)

**回答**:

```
package main

import (
	"fmt"
	"strconv"
)

func main() {

	// right way
	i := 66
	s := strconv.Itoa(i)
	fmt.Printf("%T, %v\n", s, s)      // string, 66

	// wrong way!
	t := string(i)
	fmt.Printf("%T, %v\n", t, t)      // string, B // wrong way2!
        p := []byte{66}       // (byte is an alias for uint8)
        fmt.Printf("%T, %v\n", p, p)   // []uint8, [66]}
```

**解释:**

*   `Itoa`是`strconv`包中的一个函数，将`int`转换为`string`。署名是:`func Itoa(i int) string`
*   Go 中的字符串存储为一片字节。所以当你使用类型转换把一个 int 转换成一个 string，你是把 int 转换成一个字节片。

如果你仍然有点困惑，请查看下面的帖子。

[](/demystifying-bytes-runes-and-strings-in-go-1f94df215615) [## 揭开 Go 中字节、符文和字符串的神秘面纱

### 当谈到字符串、字节和符文时，许多入门级 Golang 开发人员感到困惑。在这篇文章中，我想…

levelup.gitconnected.com](/demystifying-bytes-runes-and-strings-in-go-1f94df215615) 

# 4.如何连接两个切片？

**链接**:[https://stack overflow . com/questions/16248241/concatenate-two-slices-in-go](https://stackoverflow.com/questions/16248241/concatenate-two-slices-in-go)

**回答**:

```
package main

import "fmt"

func main() {

	// how to concatenate two slices of strings in go ?
	a := []int{1, 2, 3}
	b := []int{4, 5, 6}

	// right way
	c := append(a, b...)    // [1 2 3 4 5 6]
	fmt.Println(c)

}
```

**说明:** `append()`一个**变量**函数，spread 运算符…用于将一个切片的元素展开到一个变量列表中。

# 5.如何表示一个枚举？

**链接**:[https://stack overflow . com/questions/14426366/what-a-an-idiom-way-of-presentation-enums-in-go](https://stackoverflow.com/questions/14426366/what-is-an-idiomatic-way-of-representing-enums-in-go)

**回答**:

```
package main

import "fmt"

type Season int

const (
	Spring Season = iota + 1
	Summer
	Fall
	Winter
)

func (s Season) String() string {
	return [...]string{"Spring", 
           "Summer", 
           "Fall", 
           "Winter"}[s-1]
}

func (s Season) EnumIndex() int {
	return int(s)
}

func main() {
	var s Season = Fall
	fmt.Println(s)                 // Fall
	fmt.Println(s.String())        // Fall
	fmt.Println(s.EnumIndex())     // 3

} 
```

**解释:**

*   `iota`是希腊字母表中的字母。它是一个特殊的关键字，用来代表序列中的下一个数字。
*   可以理解为每次使用都递增的计数器。

[](/implementing-enums-in-golang-9537c433d6e2) [## 在 Golang 中实现枚举

### 枚举将相关的常数组合在一个类型中。枚举是一个具有广泛用途的强大功能。然而…

levelup.gitconnected.com](/implementing-enums-in-golang-9537c433d6e2) 

# 6.如何检查文件是否存在？

**链接**:[https://stack overflow . com/questions/12518876/how-to-check-if-a-file-exists-in-go](https://stackoverflow.com/questions/12518876/how-to-check-if-a-file-exists-in-go)

**回答**:

```
package main

import (
	"errors"
	"fmt"
	"os"
)

func main() {

	file, err := os.Open("data.json")
	if errors.Is(err, os.ErrNotExist) {
		fmt.Println("File not found")
		return
	}

	// do some work after opening the file
	defer file.Close()
} 
```

**解释:**

*   `os.Open`打开已命名的文件进行读取。如果成功，返回文件上的方法可用于读取；如果有错误，它将返回一个非零错误对象，返回的文件将是零。
*   `os.Is`是一个函数，如果错误等于传入的 error 对象，则返回 true。

# 7.如何获取对象的类型？

**链接**:[https://stack overflow . com/questions/2017 02 75/how-to-find-the-type-of-an-object-in-go](https://stackoverflow.com/questions/20170275/how-to-find-the-type-of-an-object-in-go)

**回答**:

```
package main

import (
	"fmt"
	"reflect"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	// ge the type of an object
	var x int
	var y float64
	var z string

	// Using string formatting
	fmt.Printf("%T\n", x) // int
	fmt.Printf("%T\n", y) // float64
	fmt.Printf("%T\n", z) // string

	// Use reflect package
	fmt.Println(reflect.TypeOf(x)) // int
	fmt.Println(reflect.TypeOf(y)) // float64
	fmt.Println(reflect.TypeOf(z)) // string

	// create a new object
	p1 := Person{
		Name: "John",
		Age:  30,
	}

	// get the type of the object
	fmt.Printf("%T\n", p1)          // main.Person
	fmt.Println(reflect.TypeOf(p1)) // main.Person
} 
```

**说明:**

至少有两种方法可以得到变量的类型:`reflect.TypeOf()`和用`%T`格式化的字符串

# 8.如何将字符串转换成整数类型？

**链接**:[https://stack overflow . com/questions/4278430/convert-string-to-integer-type-in-go](https://stackoverflow.com/questions/4278430/convert-string-to-integer-type-in-go)

**回答**:

```
package main

import (
	"fmt"
	"strconv"
)

func main() {
	s := "123"

	if i, err := strconv.Atoi(s); err == nil {
           fmt.Printf("i=%d,type: %T\n", i, i)  // i=123,type: int
	}

	if i, err := strconv.ParseInt(s, 10, 64); err == nil {
           fmt.Printf("i=%d,type: %T\n", i, i) // i=123,type: int64
	}
}
```

**解说:**

*   `strconv.Atoi`是`strconv`包中的一个函数，它将一个字符串转换成一个整数
*   `strconv.ParseInt`签名:`ParseInt(s string, base int, bitSize int) (i int64, err error)`。`ParseInt`解释给定基数(0，2 到 36)和位长(0 到 64)的字符串 s，并返回相应的值 I。

我希望你喜欢读这篇文章💚。如果你愿意支持我成为一名作家，可以考虑注册[成为一名媒体成员](https://jerryan.medium.com/membership)。你还可以无限制地访问媒体上的每个故事。