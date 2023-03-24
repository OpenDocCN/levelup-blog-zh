# Base64 编码简介

> 原文：<https://levelup.gitconnected.com/an-introduction-to-base64-encoding-716cdccc58ce>

![](img/06dda47646ae28beca2d218ea5b60117.png)

作者通过 [Flickr](https://www.flickr.com/photos/cmmorrow/10788726084/) 拍摄的照片

如果您从事 web 开发、数据工程或网络安全，您可能听说过 Base64。如果你看过 Base64 文本的例子，它看起来就像胡言乱语。你可能会问自己“这是什么？”，更重要的是“这是干什么用的？”谷歌搜索答案给出了涉及 radix-64 和 RFC 4648 的高度技术性的解释。如果你在寻找一个简单的解释，你来对地方了。

# 什么是 Base64？

Base64 是将任意二进制数据表示为文本的常用且可靠的方法。这个名字来源于这样一个事实:Base64 使用 64 个可能的值来表示二进制数据。这意味着用 6 位来表示一个 Base64 字符(2⁶ = 64)。这些值由大小写字母、数字 0 到 9 以及“+”和“/”符号组成。但是我们如何将位转换成 Base64 呢？要理解这是如何做到的，需要对二进制数据是如何表示的进行一些解释。

举个例子，我们以单词“one”为例，看看它是如何用 Base64 表示的。然而，在此之前，我们先讨论一下 ASCII。 [ASCII](https://en.wikipedia.org/wiki/ASCII) 是一种在计算机系统中表示文本数据的编码方案。ASCII 用八位表示字符(总共 2⁸ = 256 个可能值)，因此 ASCII 编码的字“一”表示为:

```
 "o"        "n"        "e"
01101111   01101110   01100101
```

由 [ASCII 表](https://www.rapidtables.com/code/text/ascii-table.html)定义每个字符到其八位二进制表示的翻译。根据 Base64 [查找表](https://en.wikipedia.org/wiki/Base64#Base64_table)中的值，Base64 用六位而不是八位对二进制数据进行编码。通过将“一”的 ASCII 二进制表示拆分成六个位块，并使用 Base64 查找表，我们可以将 ASCII 字“一”的 Base64 表示获得为“b25l”。

```
 "o"        "n"        "e"
 01101111   01101110   01100101
 011011  110110  111001  100101
   "b"     "2"     "5"     "l"
```

# Base64 是做什么用的？

您可能希望将二进制数据编码为 Base64 有几个原因，有些好，有些不太好。最常见的情况是通过 HTTP 发送二进制数据。为了防止数据损坏，您可以对图像、音频和其他二进制文件格式进行 Base64 编码，以便将数据作为 HTTP GET 请求或 HTML 表单的一部分发送。一个例子就是把你的简历以 PDF 格式上传到雇主的网站上。您也可以对计算机代码进行 Base64 编码，以便通过 HTTP 进行传输。MermaidJS 这样做是为了共享流程图和图表。

Base64 的另一个成熟用例是在电子邮件附件中。附加到电子邮件的文件在通过 SMTP 邮件协议与电子邮件一起传输之前，会在后台进行 Base64 编码。

Base64 也用于混淆数据，或者掩盖数据，使其不可读。这通常是为了通过 HTTP 传输半机密信息，如唯一标识符或 JSON 有效载荷。然而，不良行为者通常采用 Base64 编码来隐藏反病毒软件的 Excel 宏和脚本中的恶意软件或恶意 URL。因此，您应该始终对运行您下载或通过电子邮件发送给您的、来源不可靠的脚本和宏持怀疑态度。

# Base64 和加密一样吗？

简而言之，不能。从表面上看，Base64 编码似乎可以用来加密数据，然而，Base64 编码只是将数据转换成文本格式。Base64 解码不使用密码，在这个过程中没有使用随机性。因此，任何 Base64 编码的数据都很容易被其他人解码。

因此，Base64 编码不能替代安全性。如果您需要确保传输的数据不会被不可信的一方读取，则需要对数据进行加密。如果您只想确保传输的数据不被不可信的一方篡改，您将需要对数据进行加密签名。这两个主题都超出了本次讨论的范围，但是我完全鼓励您学习更多关于[加密数据](https://searchsecurity.techtarget.com/definition/encryption)的知识。

# 如何对数据进行 Base64 编码和解码？

如果你需要对文本或文件内容进行 Base64 编码作为一次性用例，你可以访问 www.base64encode.org 的[，它是免费且易于使用的。还有解码 Base64 的姐妹站点【www.base64decode.org](https://www.base64encode.org/)。

如果您需要定期对数据进行 Base64 编码和解码，几种编程语言都内置了对 Base64 的支持。在 Go 中，你可以使用下面的代码对一个字符串进行 Base64 编码:

```
package mainimport (
    "encoding/base64"
    "fmt"
)func main() {
    msg := "Hello world!"
    encoded := base64.StdEncoding.EncodeToString([]byte(msg)
    fmt.Println(encoded)
}
```

在 Python 中，等效代码可用于对字符串进行 Base64 编码:

```
import base64msg = "Hello world!"
encoded = base64.b64encode(bytes(msg, encoding='utf-8'))
print(encoded.decode('utf-8'))
```

两组代码实现了相同的目标——它们接受字符串“Hello world！”as `msg`和 Base64 使用`utf-8`编码对其进行编码(`utf-8`在 Go where 中被隐式使用，因为它在 Python 中被显式定义)。两个 Base64 编码函数都处理二进制数据——Go 中的字节数组和 Python 中的字节对象。由于`msg`是一个字符串，它被转换成适当的类型并保存到变量`encoded`。`encoded`打印结果为“SGVsbG8gd29ybGQh”。

从 Base64 解码文本非常相似:

```
package mainimport (
    "encoding/base64"
    "fmt"
)func main() {
    msg := "SGVsbG8gd29ybGQh"
    decoded, err := base64.StdEncoding.DecodeString(msg)
    if err != nil {
        fmt.Println("error:", err)
        return
    }
    fmt.Printf("%q\n", decoded)
}
```

在 Python 中，等效代码可用于对字符串进行 Base64 解码:

```
import base64msg = bytes("SGVsbG8gd29ybGQh", encoding='utf-8')
decoded = base64.b64decode(msg)
print(decoded.decode('utf-8'))
```

两组代码都采用字符串“SGVsbG8gd29ybGQh”并对其进行 Base64 解码。Base64 对二进制数据进行操作，因此即使 Go `DecodeString`函数接受一个字符串，它也将`decoded`作为一个字节数组返回——这就是用于打印`decoded`的`%q`格式化动词。Python 代码同样需要通过使用`decode()`方法将`decoded`从字节对象转换为字符串对象。

# 结论

我们学习了 Base64 是什么，它的用途，以及如何在 Go 和 Python 中实现 Base64 编码器和解码器。然而，这仅仅是对 Base64 的皮毛，还有很多有待发现。希望您现在对如何将 Base64 整合到您的下一个项目中有一个更好的想法。黑客快乐！