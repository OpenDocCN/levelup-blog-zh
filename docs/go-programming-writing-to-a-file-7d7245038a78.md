# 转到编程|写入文件

> 原文：<https://levelup.gitconnected.com/go-programming-writing-to-a-file-7d7245038a78>

![](img/00ff17ee4a67480487e0375802883cd4.png)

图片来自 [go.dev](https://go.dev/blog/gopher)

Golang 是一种用于系统编程的通用编程语言。它内置了 os 和 io 之类的包，提供文件创建、打开、读取和写入功能来处理文件管理操作。

在本教程中，我们将学习一些将数据写入磁盘文件的常用方法。

# ☄☄ func (*File) WriteString ☄☄

最简单的文件操作之一是将字符串写入文件。它有两个步骤:首先，创建一个文件；其次，将字符串写入文件。

请参见下面的代码示例。

```
 package main

import (
	"fmt"
	"os"
)

func main() {
	s := "Some data string to write"

	// create a file for writing
	f, err := **os.Create**("test.txt")
	if err != nil {
		fmt.Println(err)
	}
	defer f.Close()

	n, err := **f.WriteString**(s)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Wrote %d bytes\n", n)

}
```

*   在上面的代码中，我们首先使用`os.Create`方法创建一个名为 *test.txt* 的文件。返回一个文件对象，我们可以用它来写入文件。
*   接下来，我们调用 file 对象的`WriteString`函数来写入文件。它返回写入的字节数和遇到的任何错误。

# ☄☄func(*文件)写☄☄

将字节写入文件与将字符串写入文件非常相似。

唯一的区别是字节是以字节序列的形式写入文件的，而不是以字符串的形式。并且它使用了 `Write()` 方法，而不是文件类型的`WriteString()`方法。

```
package main

import (
	"fmt"
	"os"
)

func main() {

	b := []byte("Some data string to write")

	// open file for writing
	f, err := **os.Create**("test.txt")
	if err != nil {
		fmt.Println(err)
	}
	defer f.Close()

	n, err := **f**.**Write**(b)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Wrote %d bytes\n", n)

}
```

# ☄☄ func os。WriteFile☄☄

见语法。

```
**func** **WriteFile**(filename **string**, data []**byte**, perm os.FileMode) **error**
```

它需要三个参数。

1.  *文件名*/文件路径
2.  *数据*片字节
3.  文件*许可*

`WriteFile()` 将数据写入文件名命名的文件。如果文件不存在，WriteFile 使用权限 *perm* 创建它；否则，`WriteFile`会在写入前将其截断。

```
package main

import (
	"fmt"
	"os"
)

func main() {
	bSlice := []byte("Some date to write")

	// Write to file
	fileName := "output.txt"
	err := **os.WriteFile**(fileName, bSlice, 0644)
	if err != nil {
		fmt.Println(err)
		return
	}
} 
```

# ☄☄fmt.Fprintln(f，v)☄☄

我们使用`fmt.Fprintln()`函数将字符串逐行写入文件对象。

请参见以下语法。

```
func fmt.Fprintln(w io.Writer, a …interface{})
```

`Fprintln`使用其操作数的默认格式进行格式化，并写入 *w* 。操作数之间总是添加空格，并追加一个换行符。

它返回写入的字节数和遇到的任何写入错误。

```
 package main

import (
     "fmt"
     "os"
)

func main() {

	// open file for writing
	f, err := os.Create("test.txt")
	if err != nil {
		fmt.Println(err)
	}
	defer f.Close()

	d := []string{
            "Game of Thrones", 
            "The Walking Dead", 
             "The Big Bang Theory"} for _, v := range d {
	      **fmt.Fprintln**(f, v)
	}

} 
```

如果你喜欢这篇文章，请鼓掌，让其他人也能看到。💚