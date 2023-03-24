# Django Rest 框架视图集

> 原文：<https://levelup.gitconnected.com/django-rest-framework-viewsets-2f3283dda518>

Django 中的视图集和路由器

![](img/9c9a78d7c171e9dc3b3d2dc94fb3b86f.png)

照片由 [kimi lee](https://unsplash.com/@kimileee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Django REST framework 是一个强大而灵活的工具包，可以轻松构建 Web APIs。它提供了强大的功能，例如:

*   可浏览的 API
*   证明
*   序列化程序
*   定制功能

在本教程中，我们将构建一个利用视图集和路由器的 Django 应用程序。

## 项目设置

创建一个目录并放入 cd。

```
mkdir DRF
cd DRF
```

创建一个虚拟环境。

```
python3 -m venv env
```

激活虚拟环境并安装 Django 和 Django rest 框架

```
source env/binactivate
python3 -m pip install django djangorestframework
```

创建一个名为 django_viewsets 的新 Django 项目。在项目目录中，创建一个名为 books 的应用程序

```
django-admin startproject django_viewsets
cd django_viewsetsdjango-admin startapp books
```

将 app books 和 rest_framework 添加到 settings.py 文件的 INSTALLED_APPS 列表中。

```
INSTALLED_APPS = [ 'books','rest_framework',]
```

## 模型

为具有 3 个字段的数据库创建一个 Book 对象，即:

*   标题
*   作者
*   数据 _ 已发布

因为序列化程序将模型转换成 json lets，所以在 books app 目录中创建一个 serializers.py 文件。序列化程序将采用与模型相同的名称。
创建一个 Bookserializer 类来序列化模型。序列化程序类也将继承模型的属性。

```
from rest_framework import serializersfrom .models import Bookclass BookSerializer(serializers.ModelSerializer): class Meta: model = Book fields = ['title','author','date_published']
```

## Django 管理站点

Django 管理站点将允许我们添加一些测试数据。在`admin.py`中，创建一个带有标题、作者和出版日期字段的`BookAdmin`对象

## 视图集

来自 Django rest 框架文档:

> 一个`ViewSet`类仅仅是**一种基于类的视图，它不提供任何方法处理程序**如`.get()`或`.post()`，而是提供动作如`.list()`和`.create()`。

有四种不同类型的视图集，即，

*   视图集
*   GenericViewSet
*   模型视图集
*   ReadOnlyModelViewSet

例如，这个视图集可以执行所有的 CRUD 操作。

动作处理程序对应于不同的 HTTP 方法:

*   列表-获取
*   创建-发布
*   检索-获取特定的项目
*   更新-发布特定项目
*   partial_update-修补特定项目
*   销毁—删除

**视图集类**
视图集类继承自 APIView，默认不包含任何动作。例如

**GenericViewSet 类**
generic viewset 类继承自 generic piview，因此将继承 generic piview 的所有属性，如 get_object、get_queryset 方法等
默认情况下不包含任何操作。

**模型视图集类**
视图集类继承自 GenericAPIView，包含动作即。list()，。检索()，。create()，。更新()和。销毁()。
它还将 serializer 类和 queryset 作为属性

**ReadOnlyModelViewSet 类**
ReadOnlyModelViewSet 顾名思义只提供只读动作(。列表()和。检索()。).
它继承自 GenericAPIView，并将至少接受一个 queryset 和一个 serializer 类作为属性

## 视图

让我们创建一个 BookViewset，它将从 ModelViewset 继承并具有以下属性

*   序列化程序类
*   一个查询集

## 路由器

与我们必须自己设计 url 的视图类不同，Router 类负责将资源连接到视图中，我们需要做的就是用一个路由器注册视图集。

```
from django.urls import  path,includefrom rest_framework import routersfrom .views import BookViewSetrouter = routers.DefaultRouter()router.register(r'books', BookViewSet)urlpatterns = [ path('', include(router.urls)),]
```

## 结论

使用视图集可以最大限度地减少您需要编写的代码量，还可以让您专注于 API 的交互和表示。

享受在媒体上阅读的乐趣，[创建一个帐户](https://essyking.medium.com/membership)以获得全部访问权限。如果你喜欢读这篇文章，你可能会喜欢？

[https://python . plain English . io/an-introduction-to-django-rest-framework-class-based-views-AC 2304285 d29](https://python.plainenglish.io/an-introduction-to-django-rest-framework-class-based-views-ac2304285d29?source=your_stories_page----------------------------------------)