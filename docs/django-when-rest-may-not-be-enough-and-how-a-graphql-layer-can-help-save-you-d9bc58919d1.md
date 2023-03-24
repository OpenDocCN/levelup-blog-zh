# Django:什么时候 REST 可能还不够，GraphQL 层如何帮助你

> 原文：<https://levelup.gitconnected.com/django-when-rest-may-not-be-enough-and-how-a-graphql-layer-can-help-save-you-d9bc58919d1>

## **简介**

在本文中，我们将介绍一个成熟的基于 REST 的 API 的维护者面临的常见问题，这个 API 是使用 Django REST 框架构建的。然后，我们将继续演示 [GraphQL](https://graphql.org/) 如何为这个问题提供解决方案，以及如何通过使用 [GraphWrap 库](https://github.com/PaulGilmartin/graph_wrap)向 Django 项目添加两行代码来应用这个解决方案。

## 该设置

页（page 的缩写）核桃公司是一家历史悠久的出版公司。他们有一个成熟的基于 REST 的 API，是使用 Django REST 框架(DRF)构建的。这个 API 允许消费者获取关于`books`或`authors`的信息。这个 API 有三个主要消费者:核桃树公司网站、核桃树公司 Android/OS 应用和外部客户端。

遵循典型的 REST 范例，API 消费者可以通过“列表”`/author/`端点找到他们有权查看的所有作者的详细信息，并可以通过“细节”`/author/id/`端点查看特定作者的详细信息。类似的终点被暴露给`book`。在幕后，这些端点由以下 DRF 系列产品提供服务:

```
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
       model = Author
       fields = ['name', 'active', 'profile'] class BookSerializer(serializers.ModelSerializer):
    author = serializers.HyperlinkedRelatedField(
          view_name='author-detail', read_only=True)
    class Meta:
        model = Book
        fields = ['author', 'title', 'page_count']
```

## 问题是

该公司网站包括一个网页，用户可以浏览现有的书籍。当一本书被点击时，我们会重定向到一个页面，这个页面会提供关于这本书及其作者的更多信息。为了获取服务于该页面的数据，web 客户端调用，例如`/book/1/`，它将给出如下所示的响应:

```
{
  'author': '/author/1/',
  'title': 'Lovely Gardens',
  'page_count': 200,
}
```

正如我们所看到的，web 客户端在这里没有获得太多关于图书作者的信息。为了获得这些数据，他们需要第二次调用`/author/1/`。这就是众所周知的 [n+1 问题](https://itnext.io/what-is-the-n-1-problem-in-graphql-dd4921cb3c1a)。会导致网站效率低下，前端架构过于复杂；这是我们想要避免的！那么我们该如何解决这个问题呢？

## 尝试在 DRF 本土解决问题

满足我们的 web 客户端的显而易见的解决方案是在我们的`/book`端点上简单地公开更多的作者字段。我们将向`BookSerializer`添加一个`author_full`字段，如下所示:

```
class BookSerializer(serializers.ModelSerializer):
    author = serializers.HyperlinkedRelatedField(
          view_name='author-detail', read_only=True)
    author_full = AuthorSerializer(source='author_full') class Meta:
        model = Book
        fields = ['author', 'author_full', 'title', 'page_count']
```

现在，当我们的 web 客户端请求`/book/1/`时，它们也会获得作者的完整表示:

```
{
  'author': '/author/1/',
  'title': 'Lovely Gardens',
  'page_count': 200,
  'author_full': {
      'name': 'Emilie B.',
      'active': True,
      'profile': '/profile/1/',
   }
}
```

我们的 web 客户端现在可以使用一个 GET 请求来提供“图书详情”页面。大家都很开心！

第二天，我们的应用程序客户端找到我们，抱怨对`/book/1/`的调用变得更慢，并且返回了应用程序不需要的信息。现在怎么办？

在试图满足我们的一个 API 客户时，我们无意中为我们的其他客户引入了一类全新的问题。不仅如此，通过在`/book`端点上公开作者字段，我们在 API 架构中引入了不必要的耦合，我们都知道这会导致什么样的问题。

开始有这种感觉，除非我们开始为每个客户端构建一个 API *(当然我们不希望这样)，否则我们会有点停滞不前。*

## GraphQL 如何帮助我们

输入 GraphQL: GraphQL 被设计成让客户机决定它从服务器接收什么信息，而不是相反。虽然有许多很棒的[包](https://docs.graphene-python.org/projects/django/en/latest/)可以从头开始创建 Python GraphQL API，但是迁移一个成熟的生产 REST API 来使用其中一个框架并不那么简单。不仅如此，可能是我们真的爱上了使用 Django REST 框架进行开发，不想仅仅为了解决这个问题而换一个新的库。

## 通过 GraphWrap 应用 GraphQL 层

[GraphWrap](https://github.com/PaulGilmartin/graph_wrap) 是一个 python 库，只需在 Django 项目中添加两行代码，就可以用 [GraphQL](https://graphql.org/learn/) 接口扩展现有的 [Django Rest 框架](https://www.django-rest-framework.org/) API。这是通过利用 [Graphene-Django](https://docs.graphene-python.org/projects/django/en/latest/) 在运行时为 API 中的每个 Django REST 视图动态构建 GraphQL ObjectType 来实现的。然后将这些对象类型粘合在一起，形成一个 GraphQL 模式，它与现有的 REST API 具有相同的“形状”。请注意，GraphWrap 并不是为了构建一个 GraphQL 模式来替换您现有的 REST API，而是扩展它来提供一个额外的[完全兼容的](http://spec.graphql.org/June2018/#sec-Root-Operation-Types) GraphQL-queryable 接口。

核桃公司 API 开发团队决定将 GraphWrap 添加到他们的项目中。通过安装后

```
pip install graph_wrap
```

团队通过将`graphql_view`添加到他们的`urlpatterns`中，在他们的 API 上公开新的`/graphql`端点:

```
from rest_framework import routers

from graph_wrap.django_rest_framework.graphql_view import graphql_view  

urlpatterns = [
     ...,
     path(r'/graphql/', view=graphql_view), 
]
```

有了这个新的`/graphql`端点，我们现在可以停止过度暴露`author`字段，而是像我们最初做的那样简单地将`author`暴露为一个 URL:

```
class BookSerializer(serializers.ModelSerializer):
    author = serializers.HyperlinkedRelatedField(
          view_name='author-detail', read_only=True)
    class Meta:
        model = Book
        fields = ['author', 'title', 'page_count']
```

这让我们的应用程序客户端感到高兴，他们不关心嵌套的作者字段。我们的 web 客户端对检索嵌套的作者字段感兴趣，然后可以通过查询新的`/graphql`端点来这样做:

```
query {
      book(id: 1) {
          title
          author {
              name
              active
          }
      }
  }
```

这将给出如下所示的响应:

```
'data': {
      'title': 'Lovely Gardens',
      'author': {
          'name': 'Emilie B.',
          'active': True, } }
```

这个`/graphql`端点允许每个客户自己决定*他们想要从 book 端点得到什么信息。我们的 web 客户端和应用程序客户端现在都很高兴！*

新的`/graphql` 端点对后端团队最有吸引力的特性(当然除了让前端满意之外！)的一个优点是，由于 GraphWrap 的以下特性，它几乎不需要额外的维护:

*   GraphQL 层构建的动态特性意味着您可以继续开发现有的基于 REST 的 API，并且知道 GraphQL 模式将自动保持最新。
*   由于 GraphQL 层使用的是底层的 REST API，所以您可以确信，在您的 REST 视图和相应的 GraphQL 类型之间，序列化、身份验证、授权和过滤等重要的事情是一致的。

*关于 GraphWrap 的更多信息，请参见 https://github.com/PaulGilmartin/graph_wrap*[T3](https://github.com/PaulGilmartin/graph_wrap)