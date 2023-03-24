# FastAPI 中的依赖注入

> 原文：<https://levelup.gitconnected.com/dependency-injection-in-fastapi-111e3e7aad28>

## 尽量减少代码中的重复

![](img/fc707e749ea6c2e03b5af52894291524.png)

萨拉·巴克西在 [Unsplash](https://unsplash.com/s/photos/inject?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在本教程中，我将揭开 FastAPI 中依赖注入背后的概念。供您参考，FastAPI 现在自带了内置的依赖注入系统，允许您在构建 API 时更容易地集成组件。

依赖注入是编程中一个概念，其中一个对象接收它所依赖的其他对象。其他对象称为依赖关系。这最大限度地减少了代码重复，并使在您的系统上进行测试变得更加容易。

根据官方文档，依赖注入提供了以下能力

*   重用相同的共享逻辑
*   共享数据库连接
*   实施身份验证和安全功能
*   还有很多其他的东西

如果您是 FastAPI 的新手，强烈建议您在继续之前阅读以下文章:

*   [顺利从烧瓶迁移到 FastAPI】](https://medium.com/better-programming/migrate-from-flask-to-fastapi-smoothly-cc4c6c255397)
*   [FastAPI 中的元数据和附加响应](https://medium.com/better-programming/metadata-and-additional-responses-in-fastapi-ea90a321d477)
*   [FastAPI 中 4 个有用的高级特性](/4-useful-advanced-features-in-fastapi-f08e4db59637)
*   [如何在 FastAPI 中保存上传的文件](/how-to-save-uploaded-files-in-fastapi-90786851f1d3)
*   [如何用 Bash 脚本重启 FastAPI](/how-to-restart-fastapi-server-with-bash-script-f05a5bfcec5c)
*   [FastAPI:如何批量处理传入请求](/fastapi-how-to-process-incoming-requests-in-batches-b384a1406ec)

让我们进入下一节，开始深入研究依赖注入的基本用法。

# 没有依赖注入的简单服务器

假设您有一个 FastAPI 服务器，它包含

*   用户的路线
*   管理员的路线

两个路由操作都接受相同的查询参数，如下所示:

*   身份证明（identification）
*   名字
*   年龄

代码应该是这样的

这种实现的一个主要缺点是，当出现新的变化(比如添加新的查询参数)时，您需要修改这两个路由操作。这不是可扩展的，需要不断的修改。

# 具有依赖注入的简单服务器

使用依赖注入，您可以简单地将它包装成一个函数，当从路由操作中调用它时，该函数返回相应的对象。首先，您需要添加`Depends`导入语句

```
from fastapi import Depends
```

接下来，声明一个接受相应参数的新函数:

*   身份证明（identification）
*   名字
*   年龄

```
async def common_parameters(id: str, name: str, age: int):
    return {"id": id, "name": name, "age": age}
```

由于返回的结果是一个字典，只需修改您的路径如下:

```
@app.get("/user/")
async def user(commons: dict = Depends(common_parameters)):
    return commons
```

你可以在下面的[要点](https://gist.github.com/wfng92/216952d7e1b9b2f6469431957a43d996)找到完整的代码。

每当有新请求时，FastAPI 将执行以下操作:

*   使用相应的参数调用依赖函数
*   从依赖函数返回结果
*   将结果分配给路线操作

事实上，你可以退回任何你喜欢的东西。通过这样做，只要查询参数有变化，就只需要修改依赖函数。因此，这种实现更加方便且可伸缩。您甚至可以在依赖函数中创建子依赖项。

# 作为依赖项的类

供您参考，您甚至可以使用类作为输入依赖项，而不是函数。只要输入依赖项是可调用的，它就会工作。与返回一个经常是晦涩难懂的字典相比，这为编辑提供了更好的支持和参考。

创建一个新类，它接受相同的输入参数，并将它们赋给相应的变量。

```
class CommonQueryParams:
    def __init__(self, id: str, name: str, age: int):
        self.id = id
        self.name = name
        self.age = age
```

之后，只需修改查询参数

```
commons: dict = Depends(common_parameters)
```

到下面

```
commons: CommonQueryParams = Depends(CommonQueryParams)
```

新的更新代码应该如下所示:

无论是否使用依赖注入，结果和输出都是一样的。然而，使用依赖注入来编码和维护要容易得多。

# 装饰者中的依赖注入

上面给出的所有例子都是基于路由操作函数中的依赖注入。事实上，您也可以在 decorators 内部使用它。主要区别在于，装饰器对于不返回值的依赖项很有用。

例如，注入一个依赖函数，根据 id 验证用户是否是管理员。在这种情况下，您可以直接返回一个`HTTPException`,而不需要将任何值传递回路由操作。记住在 Python 文件的顶部添加下面的导入声明，以便使用`HTTPException`。

```
from fastapi import Depends, FastAPI, HTTPException
```

让我们用以前的类作为依赖关系的例子。在文件中添加以下函数。

*   `verify_age` —检查`age`是否至少 18 岁及以上。
*   `verify_age` —验证`id`是否属于管理员。

按如下方式修改路线操作。我将返回一个 JSON 响应，因为依赖项不会返回任何响应。

```
@app.get("/user/", dependencies=[Depends(verify_age)])
async def user():
    return {"message": "Successfully accesed user page"}
```

您可以指定附加的`dependencies`参数，该参数接受一个`Depends`列表。这意味着您可以将多个依赖项叠加到单个路由操作中。例如，您可以使用下面的代码来检查输入是否是 admin 并且至少 18 岁。

```
dependencies=[Depends(verify_admin), Depends(verify_age)]
```

完整的代码如下:

# 试验

在命令行中运行以下命令进行测试。相应地修改名称。我在这个教程中使用的是`myapp.py`。

```
uvicorn myapp:app
```

## 管理员，年龄至少 18 岁

在您的浏览器中浏览以下网址。

```
http://localhost:8000/admin/?id=ID0001&name=wfng&age=18
```

您应该得到以下输出

```
{"message":"Successfully accessed admin page"}
```

## 管理员，年龄小于 18 岁

我们用 15 代替`age`试试吧。

```
http://localhost:8000/admin/?id=ID0001&name=wfng&age=15
```

您的浏览器将输出以下错误消息。

```
{"detail":"Requires adult supervision"}
```

## 不是管理员

使用以下 URL 试用非管理员

```
http://localhost:8000/admin/?id=ID0002&name=wfng&age=15
```

无论输入年龄是否至少为 18 岁，都会得到以下结果。这仅仅是因为依赖关系是按顺序调用的。

```
{"detail":"Requires admin access"}
```

# 结论

让我们回顾一下今天所学的内容。

我们首先简要解释了依赖注入的概念及其在 FastAPI 中的工作原理。

然后，我们继续向简单的 FastAPI 服务器添加依赖注入。

我们通过将依赖函数更改为 Python 类来进一步改进它，因为只要相应的依赖项是可调用的，它就会工作。

最后，我们深入研究并直接在 decorators 中实现了依赖关系，这对于不返回任何形式响应的依赖关系来说是理想的。

感谢您阅读这篇文章！希望在下一篇文章中再见到你！

# 参考

1.  [FastAPI —依赖关系:第一步](https://fastapi.tiangolo.com/tutorial/dependencies/)
2.  [FastAPI —作为依赖关系的类](https://fastapi.tiangolo.com/tutorial/dependencies/classes-as-dependencies/)
3.  [FastAPI —路径操作装饰器中的依赖关系](https://fastapi.tiangolo.com/tutorial/dependencies/dependencies-in-path-operation-decorators/)