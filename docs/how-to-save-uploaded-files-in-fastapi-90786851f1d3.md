# 如何在 FastAPI 中保存上传的文件

> 原文：<https://levelup.gitconnected.com/how-to-save-uploaded-files-in-fastapi-90786851f1d3>

接收文件并将其保存在本地的分步指南

![](img/7779d82f491eeb098e6a00171b5c7696.png)

作者图片

今天的主题是将用户上传到 FastAPI 服务器的图像或文本文件保存到本地磁盘中。如果你以前用过 Flask，你应该知道它自带了内置的`file.save`函数来保存文件。不幸的是，在撰写本文时，FastAPI 中还没有这样的特性。在本教程中，您将学习基于您自己的用例来实现这个功能。

# 单行

供您参考，接受通过`FormData`上传的图像或文件的简单 FastAPI 服务器的最低代码如下:

```
from fastapi import FastAPI, File, UploadFile

app = FastAPI()

@app.post("/image")
async def image(image: UploadFile = File(...)):
    return {"filename": image.filename}
```

## Python 多部分

为了接收上传的文件，你需要安装`python-multipart`。如果必须在虚拟环境中安装终端，请在终端中运行以下命令。

```
pip install python-multipart
```

## FastAPI 上传文件

`UploadFile`使用 Python `SpooledTemporaryFile`对象，这意味着只要文件大小在大小限制内，它就会被存储在内存中。否则，一旦超过大小限制，它将被存储在磁盘中。

由于它继承自`Starlette`，因此具有以下属性:

*   `filename` —文件的名称。它基于上传的原始文件名。
*   `content_type` —文件的内容类型。比如 JPEG 图像文件应该是`image/jpeg`。
*   `file` — Python 文件对象。

此外，`UploadFile`还附带了四个额外的`async`功能。通常，您应该在普通的`async`函数中使用 await 调用它们。在 FastAPI 中，`async`方法被设计成在线程池中运行文件方法，它将等待它们。因此，您可以不使用`await`语法来调用这些函数。

*   `write(data)` —将数据写入文件。接受字符串或字节。
*   `read(size)` —根据输入参数读取文件的 n 个字节或字符。接受整数。
*   `seek(offset)` —移动到文件中的字节或字符位置。使用`seek(0)`将转到文件的开头。
*   `close()` —关闭文件

## 保存文件

Flask 有一个`file.save`包装器功能，允许你将上传的文件保存在你的磁盘上。包装器基于 Python 标准库的一部分`shutils.copyfileobj()`函数。然而，在撰写本文时，FastAPI 还没有这样的实现。

您可以在 FastAPI 服务器内部轻松实现它。首先，你需要导入`shutil`模块。

```
import shutil
```

基本结构和代码如下所示。

```
with open("destination.png", "wb") as buffer:
    shutil.copyfileobj(image.file, buffer)
```

只需在 FastAPI 方法中调用它。看看下面的代码片段。

```
async def image(image: UploadFile = File(...)):
    with open("destination.png", "wb") as buffer:
        shutil.copyfileobj(image.file, buffer)

    return {"filename": image.filename}
```

重新运行您的 FastAPI 服务器，并提交一个表单，从您的前端代码中附加一个文件。您应该会看到一个基于您指定的路径生成的新文件。

# 多个文件

为了处理多个文件的上传，需要导入下面的语句

```
from typing import List
```

接下来，修改接受`UploadFile`的`List`的 FastAPI 方法。

```
async def image(images: List[UploadFile] = File(...)):
```

使用`for loop`简单地循环它，并在其中实现保存逻辑。

```
for image in images:
    with open(image.filename, "wb") as buffer:
        shutil.copyfileobj(image.file, buffer)
```

有关 FastAPI 的更多信息，强烈建议阅读以下文章:

*   [从烧瓶顺利迁移到 FastAPI】](https://medium.com/better-programming/migrate-from-flask-to-fastapi-smoothly-cc4c6c255397)
*   [FastAPI 中 4 个有用的高级特性](/4-useful-advanced-features-in-fastapi-f08e4db59637)
*   [FastAPI 中的元数据和附加响应](https://medium.com/better-programming/metadata-and-additional-responses-in-fastapi-ea90a321d477)

# 结论

让我们回顾一下今天所学的内容。

我们从一个简单的问题陈述开始，解释 FastAPI 中缺少保存上传文件的包装函数。

接下来，我们深入探讨了 UploadFile 背后的基本概念以及如何在 FastAPI 服务器中使用它。

我们还使用内置的 shutil.copyfileobj 方法实现了一个简单的文件保存功能。此外，我们还尝试扩展我们的服务器来处理多个文件上传。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [FastAPI 请求文件](https://fastapi.tiangolo.com/tutorial/request-files/)
2.  [保存上传文件 FastAPI Github 问题](https://github.com/tiangolo/fastapi/issues/426)