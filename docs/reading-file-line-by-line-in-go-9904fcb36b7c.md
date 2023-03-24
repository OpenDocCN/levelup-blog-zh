# 在 Go 中逐行读取文件

> 原文：<https://levelup.gitconnected.com/reading-file-line-by-line-in-go-9904fcb36b7c>

![](img/be25cb14b80faacd7ea426198b2d9ead.png)

由 [Madalyn Cox](https://unsplash.com/@madalyncox?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在 go 中读取文件的一种流行方式是使用 Go 标准包“bufio”。

代码片段:

```
package main

import (
    "bufio"
    "fmt"
    "log"
    "os"
)

func main() {
    file, err := os.Open("file.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer file.Close()

    scanner := bufio.NewScanner(file)
    for scanner.Scan() {
        fmt.Println(scanner.Text())
    }

    if err := scanner.Err(); err != nil {
        log.Fatal(err)
    }
}
```

在这个示例代码中，我们首先尝试使用“OS”包打开位于当前目录中的文件“file.txt”

然后传递这个 io 类型的文件。Reader 进入一个名为“NewScanner”的函数，在“bufio”一个标准的库包中，返回一个“Scanner”

扫描器有一个名为的方法，该方法使扫描器前进到下一个标记，然后可以通过 Bytes 或 Text 方法使用该标记。当扫描停止时，它返回 false，无论是由于到达输入的结尾还是由于出错。Scan 返回 false 后，Err 方法将返回扫描期间发生的任何错误，除非是 io。EOF，Err 将返回 nil。

这里我们使用“fmt”打印文件的每一行。Println "

> **警告:**

当行数超过 65536 个字符时，扫描仪将抛出错误。如果长度有可能超过 65536 个字符，可以使用`[Buffer()](https://pkg.go.dev/bufio#Scanner.Buffer)`方法增加扫描仪的容量:

```
package main

import (
    "bufio"
    "fmt"
    "log"
    "os"
)

func main() {
    file, err := os.Open("file.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer file.Close()

    scanner := bufio.NewScanner(file)
    const maxCap int = lengthOfTheLongestLine
    buf := make([]byte, maxCapacity)
    scanner.Buffer(buf, maxCapacity)
    for scanner.Scan() {
        fmt.Println(scanner.Text())
    }

    if err := scanner.Err(); err != nil {
        log.Fatal(err)
    }
}
```