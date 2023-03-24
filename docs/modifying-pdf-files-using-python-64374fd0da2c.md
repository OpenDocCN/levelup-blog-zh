# 使用 Python 修改 PDF 文件

> 原文：<https://levelup.gitconnected.com/modifying-pdf-files-using-python-64374fd0da2c>

## 指南提取数据，连接，合并，加密和解密 PDF 文件

![](img/79018c9f2f974921260517f5722e5cc7.png)

维克多·塔拉舒克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

通过互联网共享信息的最常见的文档格式之一是 PDF 或可移植文档格式。了解如何使用 Python 修改 PDF 文件是很有用的。

为此，您需要在系统上安装一个 ***PyPDF2*** 包。如果没有，请在您的控制台上运行以下命令-

```
>> pip3 install PyPDF2
```

我们将通过简单的操作来开始。

# 使用 *PdfFileReader 类*提取数据

您需要一个来自接受 PDF 文件路径的 *PyPDF2* 的 *PdfFileReader* 类的实例。该实例将允许您使用访问 PDF 文件中的数据所需的所有必要方法和属性。

```
>> from PyPDF2 import PdfFileReader
>> pdf_reader = PdfFileReader("SamplePDFDocument.pdf")
```

## A.提取元数据

创建时嵌入的每个 PDF 文件都有关联的元数据。您可以使用 *documentInfo* 属性来获取它。

```
>> pdf_reader.documentInfo
{'/Author': 'Amit Randive', '/Creator': 'Microsoft® Word for Office 365', '/CreationDate': "D:20200708221150+05'30'", '/ModDate': "D:20200708221150+05'30'", '/Producer': 'Microsoft® Word for Office 365'}
```

输出可能看起来像一本字典，但它不是。您可以将每个项目作为一个属性来访问。

```
>> pdf_reader.documentInfo.producer
Microsoft® Word for Office 365
```

你也可以得到 PDF 文件的页数信息-

```
>> pdf_reader.getNumPages()
3
```

## B.提取文本数据

*PyPDF2* 包中的每个页面都由 *PageObject* 类表示。您可以使用此类的实例与 PDF 页面进行交互。这个实例可以使用 *PdfFileReader* 类的 *getPage()* 方法捕获。

*   要获得准确的页面对象，您需要将页面索引号传递给该方法。

```
>> pdf_page = pdf.getPage(0)
```

*   现在我们有了页面对象，是时候从中提取文本数据了。

```
>> pdf_page.extractText()
This is a sample pdf document generated to give a demo for PDF modification using python package PyPDF2.
```

*   如果你想一次遍历所有的页面，那么 *PdfFileReader* 有 *pages* 属性可以帮助你做到这一点

```
>>for page in pdf_reader.pages:
      print(page.extractText())
```

# 使用 PDF writer 类创建新的 PDF 文件

PdfFileWriter 类允许你创建新的 PDF 文件。这个类的实例就像空白的 PDF 文件。在继续保存之前，您需要添加空白页。

```
>> from PyPDF2 import PdfFileWriter
>> pdf_writer = PdfFileWriter()
>> pdf_writer.addBlankPage(width=72,height=72)
```

*   对于一个新的空白页，你需要指定宽度和高度，这决定了页面的尺寸，单位叫做**点**。一点等于 1/72 英寸，所以上面的代码向 pdf_writer 的*添加了一个一英寸见方的空白页。*
*   要将 *pdf_writer* 的内容写入 pdf 文件，以二进制写入模式将文件对象传递给 *pdf_writer.write()。*

```
>>> from pathlib import Path
>>> with Path("NewFile.pdf").open(mode="wb") as output_file:
        pdf_writer.write(output_file)
```

这将在您当前的工作目录中创建一个新的 PDF 文件。如果你打开它，你会看到一个只有一英寸见方的空白页面的文档。

> PdfFileWriter 有一个很大的问题，它的对象可以写入新的 PDF 文件，但是除了空白页之外，它们不能从头开始创建新的内容。为此，您可以使用另一个名为 **reportlab 的包。**

您可以添加多个页面，并使用从另一个 PDF 中提取的数据进行书写。使用*的 PdfFileReader* 类对象来读写它，使用*的 pdffilerwriter*类对象是如此简单。搞定了。

# 使用 PdfFileMerger 类连接和合并

让我们向前一步，看看我们如何连接和合并 pdf。为此， *PyPDF2* 为*提供了一个 PdfFileMerger* 类，它也能够编写 PDF 文件。但是它与 *PdfFileWriter* 类有什么不同呢？

两者的主要区别在于， *PdfFileWriter* 类只能将页面追加或连接到末尾，而 *PdfFileMerger* 可以在任何位置插入或合并页面。

```
>> from PyPDF2 import PdfFileMerger
>> pdf_merge = PdfFileMerger()
```

*PdfFileMerger* 实例最初为空。您需要添加一些页面来做其他事情。

## A.串联

*PdfFileMerger* 类允许使用 *append()* 方法将一个或多个 pdf 连接到现有文件。

```
>> list_of_pdfs=['Sample1.pdf','Sample2.pdf', 'Sample3.pdf']
>> for path in list_of_pdfs:
       pdf_merge.append(path)
```

现在，*list _ of _ pdf*中的所有 pdf 被连接到 *pdf_merge* 对象中。您需要将它写入输出文件。以二进制写模式打开一个新文件，然后将 file 对象传递给 *pdf_merge.write()* 方法。

```
>> with Path("concatenated.pdf").open(mode="wb") as output_file:
        pdf_merge.write(output_file)
```

在你当前的工作目录中，你会有一个 concatenated.pdf 文件。

## B.合并

使用 *PdfFileMerger* 类的 *merge()* 方法合并或插入另一个 PDF 到现有文件。该方法需要您想要插入另一个 PDF 的页面的索引号和包含其路径的字符串。

*   创建一个 PdfFileMerger 类对象，它只是一个空对象。您需要添加一个想要插入或合并另一个 PDF 的 PDF 文件。

```
>>> pdf_merge = PdfFileMerger()
>>> pdf_merge.append("Sample1.pdf")
```

*   对 *pdf_merge* 对象调用 *merge* ()方法，并传递另一个 pdf 的索引和路径。

```
>>> pdf_merge.merge(2,"Sample3.pdf")
```

*Sample3.pdf*中的每一页都被插入到索引 2 处的页面之前。当前索引为 2 的页面将被 Sample3.pdf*的页面数量所移动。*

*   将合并的 PDF 写入输出文件:

```
>>> with Path("Merged.pdf").open(mode="wb") as output_file:
         pdf_merger.write(output_file)
```

连接和合并 pdf 是非常有用和常见的场景。自动化可以节省大量的时间和人力。

# PDF 文件的加密和解密

加密的 PDF 文件在互联网上共享机密信息非常流行。 *PyPDF2* 不仅允许您使用受密码保护的文件，还允许您为 PDF 文件添加密码保护。

## A.加密

*PdfFileWriter* 类的 *encrypt()* 方法用于给 PDF 文件添加密码保护。该方法允许设置两种类型的密码-

1.  **用户密码** -允许 PDF 以只读模式打开，可以使用 *user_pwd* 参数设置。
2.  **所有者密码** -允许 PDF 不受任何限制地打开。您可以阅读甚至编辑打开的文档。可以使用 *owner_pwd* 参数设置该密码

让我们为*SecretDocument.pdf-*添加密码保护

*   打开文档。

```
>> pdf_reader = PdfFileReader("SecretDocument.pdf")
```

*   由于 *encrypt()* 方法属于 *PdfFileWriter* 类，我们需要它的实例，它最初是空的。您可以使用*appendPagesFromReader()*方法将页面从 pdf_reader 直接添加到这个新创建的实例中

```
>>> pdf_writer = PdfFileWriter()
>>> pdf_writer.appendPagesFromReader(pdf_reader)
```

*   是时候添加密码了

```
>>> pdf_writer.encrypt(user_pwd="M@yTheF0rceBeWithU")
```

注意，由于我没有给 *owner_pwd* ，默认值将设置为与 *user_pwd 相同。*

*   将加密文件写入输出文件-

```
>>> with Path("SecretDocument.pdf").open(mode="wb") as output_file:
         pdf_writer.write(output_file)
```

现在您有了密码保护的 PDF 文件。无论何时打开它，系统都会提示您输入密码来访问它。

## B.[通信]解密

*PdfFileReader* 类提供了 *decrypt()* 方法对 PDF 文件进行解密。

*decrypt()* 方法接受*一个可以用来解密的密码*参数。打开 PDF 时您拥有的权限取决于您传递给*密码*参数的参数。

我们将使用相同的 PDF，我们刚刚加密的密码，即*SecretDocument.pdf*。

*   创建 *PdfFileReader* 实例-

```
>>> pdf_reader = PdfFileReader("SecretDocument.pdf")
```

*   如果你试图在不提供密码的情况下访问任何页面或提取数据，看看会发生什么

```
>>> pdf_reader.getPage(0)Traceback (most recent call last):
File "..\Python\Python38\lib\site-packages\PyPDF2\pdf.py", line 1150, in getNumPages
    raise utils.PdfReadError("File has not been decrypted")
PyPDF2.utils.PdfReadError: File has not been decrypted
```

一个**pypdf 2 . utils . pdfreadererror**异常会直接抛出，告诉你“**文件还没有被解密**”。**

**在我们解密我们的文档之前，您应该知道由 *decrypt()* 方法返回的解密状态码。**

*   **0-密码不正确。**
*   **1-用户密码匹配。**
*   **2-所有者密码匹配。**

```
**>>>pdf_reader.decrypt(password="M@yTheF0rceBeWithU")
1**
```

**现在你可以自由地访问你解密的 PDF 文件的内容了:-)**

**这并不是 PyPDF2 功能的终结。还有更多！它使您能够执行复杂的操作，如旋转和裁剪 PDF 文件的页面。**

**如果你到达这里并理解了这篇文章中的一切，那么恭喜你！！您已经准备好开始自动化 PDF 文件修改任务。**

****干杯！！****