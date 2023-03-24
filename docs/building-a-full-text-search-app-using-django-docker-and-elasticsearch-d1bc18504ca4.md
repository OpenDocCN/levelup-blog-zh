# 使用 Django、Docker 和 Elasticsearch 构建全文搜索应用

> 原文：<https://levelup.gitconnected.com/building-a-full-text-search-app-using-django-docker-and-elasticsearch-d1bc18504ca4>

![](img/42721c8384e9ce91a7814c88c54bddab.png)

在这篇文章中，我会给你一些关于 Elasticsearch 的简单信息，它的安装，以及一些使用的例子。

# 弹性搜索—基本概念

> Elasticsearch 是一个实时分布式开源全文搜索和分析引擎。它可以从 RESTful web 服务接口访问，并使用无模式 JSON (JavaScript 对象符号)文档来存储数据。它建立在 Java 编程语言之上，这使得 Elasticsearch 可以在不同的平台上运行。它使用户能够以非常高的速度探索大量的数据。

有一些弹性搜索的基础，一旦你把它们内化了，就能减少学习曲线的创伤。我总结了 4 个最重要的概念:

1.  **字段:**弹性搜索中数据的最小个体单位。每个字段都有定义的数据类型；核心数据类型(字符串、数字、日期、布尔值)或复杂数据类型(对象和嵌套)。
2.  **索引:**不同类型文档和文档属性的集合。它可以比作关系数据库世界中的一个数据库。
3.  **文档:**以特定方式在 JSON 格式中定义的字段集合。每个文档都属于一种类型，并驻留在索引中。在关系数据库的世界中，文档可以比作表中的一行。
4.  **映射:**共享同一索引中一组公共字段的文档集合。同样，它就像关系数据库世界中的一个模式。

⚠️:值得一提的是，Elasticsearch 不能用作数据库，它不是为此目的而建立的。因此，最好是在项目中将其作为 PostgreSQL、MySQL 或其他数据库旁边的附加服务。

# 使用 Django 的 Elasticsearch

很多教程都在使用 [Django-Haystack](https://django-haystack.readthedocs.io/) ，这个在 Django 社区使用非常广泛的工具，作为一个模块化的搜索来插入 ElasticSearch(或者其他任何搜索引擎比如 Solr，Whoosh，Xapian 等。)，因为它的最小配置和查询语法类似于 Django 的 ORM。

我最近在一个项目中使用了它和 Solr，我对它的简单实现印象深刻，我喜欢它，但我不会在本文中使用它，我认为 Elasticsearch 本身很容易使用。

我将使用 Docker 来运行 Elasticsearch。

下面是源代码，这样您就可以清楚地看到发生了什么:

[](https://github.com/aymaneMx/django-elasticsearch) [## aymaneMx/django-elasticsearch

### 用 Django 测试 Elasticsearch 的简单项目，基于 docker 构建。

github.com](https://github.com/aymaneMx/django-elasticsearch) ![](img/9838c5ec3b524827c1eac92c4cecfd0a.png)

# 弹性搜索实例

在项目目录中编辑`docker-compose.yml`以添加一个 ES 服务:

```
es:
    image: elasticsearch:7.8.1
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"
```

然后将`es`添加到 Django app service 所依赖的服务中，将`ELASTICSEARCH_DSL_HOSTS=es:9200`添加到`docker-compose.env`:

```
web:
    build: .
    command: python /code/manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - 8000:8000
    env_file:
      - docker-compose.env
    depends_on:
      - db
      - es    <--- here
```

现在运行`docker-compose up -d --build`

您可以通过 curl 检查它是否正常工作:

```
curl -X GET localhost:9200/_cluster/health
```

# 设置弹性搜索

让我们安装 Django Elasticsearch DSL。用你喜欢的 Python 包管理器从 PyPI 安装 app，我用 pipenv。

```
pipenv install django-elasticsearch-dsl
```

与大多数 Django 应用程序一样，您应该将`django_elasticsearch_dsl`添加到设置文件中的 INSTALLED_APPS:

```
INSTALLED_APPS = [
    ...
    'django_elasticsearch_dsl',
    ...
]
```

然后，您必须在 Django 设置中定义 ELASTICSEARCH_DSL。

```
# Elasticsearch
ELASTICSEARCH_DSL = {
    'default': {
        'hosts': os.getenv("ELASTICSEARCH_DSL_HOSTS", 'localhost:9200')
    },
}
```

# 将数据索引到弹性搜索中

让我们考虑以下模型:

```
class Post(models.Model):
    title = models.CharField(max_length=128)
    content = models.CharField(max_length=5000)
    created_at = models.DateTimeField(default=timezone.now)
    likes = models.PositiveIntegerField(default=0)
    slug = models.SlugField(max_length=128, db_index=True, null=True)
    draft = models.BooleanField(default=True) user = models.ForeignKey(
        User, 
        related_name='posts', 
        on_delete=models.CASCADE
    ) def __str__(self):
        return self.title class Meta:
        app_label = 'posts'
```

然后我们应该运行迁移:

```
$ docker-compose run web python manage.py makemigrations
$ docker-compose run web python manage.py migrate
```

现在让我们定义 ElasticSearch 索引，这需要在您的应用程序目录中的`[documents.py](http://documents.py/)`中定义文档类。

```
from django.contrib.auth import get_user_model
from django_elasticsearch_dsl import Document, fields
from django_elasticsearch_dsl.registries import registry
from .models import Post, ReplyUser = get_user_model()@registry.register_document
class PostDocument(Document):
    user = fields.ObjectField(properties={
        'id': fields.IntegerField(),
        'username': fields.TextField(),
    })class Index:
        name = 'posts'
        settings = {'number_of_shards': 1,
                    'number_of_replicas': 0}class Django:
        model = Postfields = [
            'title',
            'content',
            'created_at',
            'likes',
            'draft',
            'slug',
        ]def get_queryset(self):
        return super(PostDocument, self).get_queryset().select_related(
            'user'
        )def get_instances_from_related(self, related_instance):
        if isinstance(related_instance, User):
            return related_instance.posts.all()
        elif isinstance(related_instance, Reply):
            return related_instance.post
```

# 用法示例

为了用一些内容填充数据库，我为此发出了一个命令，只需运行:

```
docker-compose run web python manage.py load_posts 20
```

现在，让我们进入交互式 Python shell ( `docker-compose run web python manage.py shell`)并尝试使用 ElasticSearch 查询:

```
>>> from posts.documents import PostDocument
>>> posts = PostDocument.search()
>>> for hit in posts:
...     print(hit.title)
... 
Design half three bar quickly material center.
Author true left. Position entire someone study be.
School draw individual sell produce brother.
Truth drug compare TV modern.
Expert apply baby reveal team along.
Beautiful for suddenly half.
Plant argue enough less order receive sing.
Store economy offer decision industry.
Beat chair affect assume score occur include laugh.
Language poor cell fish worry ready industry use.
>>>
```

接下来，我收集了一些你可能需要的例子:

```
search = PostDocument.search()# Filter by single field equal to a value
search = search.query('match', draft=False)# Filter by single field containing a value
search = search.filter('match_phrase', title="value")# Add the query to the Search object
from elasticsearch_dsl import Q
q = Q("multi_match", query='python django', fields=['title', 'content'])
search = search.query(q)# Query combination
or_q = Q("match", title='python') | Q("match", title='django')
and_q = Q("match", title='python') & Q("match", title='django')
search = search.query(or_q)# Exclude items from your query
search = search.exclude('match', draft=True)# Filter documents that contain terms within a provided range.
# eg: the posts created for the past day
search = search.filter('range', created_at={"gte": "now-1d"})# Ordering
# prefixed by the - sign to specify a descending order.
search = search.sort('-likes', 'created_at')
```

给你一个快速测验😄，您可以在评论中提交您的答案👇

> 如何获取过去一周内发布的标题/内容中包含“使用”一词的帖子？

# 就是这样！

老实说，这太长了。如果你有足够的耐心读完这篇文章，并且觉得它很有趣，那么请分享它。

> 本文最初发表于[https://obytes.com/](https://www.obytes.com/blog/building-a-full-text-search-app-using-django-docker-and-elasticsearch)