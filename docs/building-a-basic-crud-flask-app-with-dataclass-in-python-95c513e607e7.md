# 用 Python 中的 Dataclass 构建一个基本的 CRUD Flask 应用程序

> 原文：<https://levelup.gitconnected.com/building-a-basic-crud-flask-app-with-dataclass-in-python-95c513e607e7>

Python 3.7 中一个令人兴奋的特性是**数据类**。我最近开始在几个 web 后端项目中使用这个模块，我很喜欢它。

在这篇文章中，我们将构建一个基本的 CRUD flask 应用程序，作为 dataclass 的一个例子。

![](img/b5256b17a20652ee7cba8d45dbc3c512.png)

照片由[通风视图](https://unsplash.com/@ventiviews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 要求

*   [烧瓶](https://github.com/pallets/flask)(流行的 web 框架)
*   [棉花糖-数据类](https://github.com/lovasoa/marshmallow_dataclass)(数据类的字段验证)
*   [pymongo](https://github.com/mongodb/mongo-python-driver) (存储数据)
*   [dataclasses-JSON](https://github.com/lidatong/dataclasses-json) :(轻松地将 dataclasses 序列化到 JSON 和从 JSON 序列化)

# 模型

我们首先定义一个类来表示一个 book 对象。它具有以下字段和相应的类型。

*   id: int
*   标题:str
*   作者:str
*   类型:str
*   年份:整数
*   描述:str
*   图像:str
*   汇率:浮动

下面是一个示例表示。

```
{
    "id": 1,
    "title": "The Great Gatsby",
    "author": "F. Scott Fitzgerald",
    "year": 1925,
    "genre": "Action",
    "description": "The Great Gatsby is a 1925 novel ...",
   "image": "https://images-na.ssl...Kb%2BQmL.jpg",
   "rating": 4.5
}
```

使用 dataclass 定义这个对象非常容易。下面是最简单的代码。

接下来，让我们使用两个包添加一些很酷的东西: ***棉花糖-数据类*** 和**数据类-JSON** 。请记住，数据类不需要类型注释；它只是作为一个类型提示。

*   字段`title`验证是为了确保标题不为空，并且是满足特定条件的字符串
*   字段`created_at` 有一个默认的日期时间值，可以从时间戳自动反序列化。
*   字段`id`为每本书生成一个唯一的标识符。

不包括其他数据类字段。例如，当我们像下面这样创建一个 dataclass 实例时，字段`x`被忽略而没有错误。

```
>> b = BookModel(id=1,
              author="John Doe",
              title="Book Title",
              year=2020,
              genre=1,
              description="Description",
              image="image.jpg",
              rating=5.0,
              **x="y"**)

>> print(b)
BookModel(id=1, author='John Doe', title='Book Title', year=2020, genre=1, description='Description', image='image.jpg', rating=5.0, created_at=**datetime.datetime(2022, 3, 17, 15, 59, 20, 757186**))
```

对于数据模型部分来说，这已经足够了。是时候开始我们的 CRUD 旅程了。

# 数据库

我们使用`mongodb`来存储我们的图书数据。

# 创造

这个 API 通过使用请求中发送的有效负载创建一本新书，并将其存储在数据库中

*   我们使用 validate 方法来防止用户发送无效的负载
*   有效负载中不允许有字段`id`，因为我们不想让用户在创建新书时分配一个`id`。那是后端的工作。

# 恢复

# 更新

Update API 类似于 create API，但是我们需要在更新它之前检查这本书是否存在。

# 删除

# 目录

这里是这个应用程序的完整代码，如果你想在本地运行它。

[](https://github.com/jerryan999/dataclass-crud-demo) [## GitHub—jerryan 999/data class-crud-demo

github.com。](https://github.com/jerryan999/dataclass-crud-demo) 

# 资源:

1.  [Python 3.7+](https://realpython.com/python-data-classes/)中的数据类
2.  [你应该开始使用 Python 数据类的 9 个理由](https://towardsdatascience.com/9-reasons-why-you-should-start-using-python-dataclasses-98271adadc66)
3.  [使用棉花糖简化 API 中的参数验证](https://medium.com/bitproject/recently-i-created-a-restful-api-with-flask-where-my-models-had-many-parameters-75da1db870b7)
4.  [marshmallow vs . pydantic——Python 的两个最好的数据序列化和验证库](https://www.augmentedmind.de/2020/10/25/marshmallow-vs-pydantic-python/)
5.  [https://stack overflow . com/questions/47955263/什么是数据类以及它们与普通类有何不同](https://stackoverflow.com/questions/47955263/what-are-data-classes-and-how-are-they-different-from-common-classes)