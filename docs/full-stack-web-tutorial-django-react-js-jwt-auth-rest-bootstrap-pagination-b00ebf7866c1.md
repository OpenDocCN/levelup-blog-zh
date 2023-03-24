# 全栈网络教程:Django，React JS，JWT Auth，REST，Bootstrap，分页

> 原文：<https://levelup.gitconnected.com/full-stack-web-tutorial-django-react-js-jwt-auth-rest-bootstrap-pagination-b00ebf7866c1>

实践中学习

![](img/ae7754995851a24a34508beae70660ea.png)

我们将使用 Django REST 为后端编写一个销售订单管理系统，React JS 为前端。我们将添加 JWT 认证，并将测试覆盖后端。

我们还会使用 React JS 的 CDN 版本，而不是“create-react-app”。这将简化开发，因为我们不需要运行两个服务器:一个用于 Django，一个用于 React。

当然，我们将在 React 中使用功能组件。

额外奖励:精美动画温馨提示:)

要点:GitHub 上完整的项目源代码

内容:

1.  创建 Python Django 项目
2.  定义模型
3.  添加身份验证(带测试)
4.  为销售订单 API 添加测试
5.  实现 API: GET 和 DELETE
6.  实现 API: POST 和 PUT
7.  用 React JS 初始化前端
8.  导入引导和 Sweet 警报
9.  从前端验证
10.  显示订单表(GET)
11.  实施“添加新订单”
12.  实施“编辑订单”
13.  实现“删除订单”
14.  GitHub 知识库
15.  结论

# 创建 Python Django 项目

我假设你已经安装了 Python3。

我们将需要这些库来启动:

```
pip install django
pip install djangorestframework
pip install markdown
pip install django-filter
pip install djangorestframework-simplejwt
```

创建项目和应用程序:

```
django-admin startproject sales
cd sales
python manage.py startapp app
```

这将为您的项目文件做好准备。对 settings.py 进行以下更改:

将您的应用程序添加到 INSTALLED_APPS:

```
INSTALLED_APPS = [
    **'django.contrib.admin'**,
    **'django.contrib.auth'**,
    **'django.contrib.contenttypes'**,
    **'django.contrib.sessions'**,
    **'django.contrib.messages'**,
    **'django.contrib.staticfiles'**,
    **'rest_framework'**,
    **'app'**,
]
```

更改允许的主机。我知道这是个糟糕的建议。

```
ALLOWED_HOSTS = [**"*"**]
```

将“模板”添加到模板:

```
TEMPLATES = [
   {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [**'templates'**],
...
```

添加一些身份验证设置:

```
REST_FRAMEWORK = {
    **'DEFAULT_AUTHENTICATION_CLASSES'**: (
        **'rest_framework.authentication.BasicAuthentication'**,
        **'rest_framework_simplejwt.authentication.JWTAuthentication'**,
    )
}
SIMPLE_JWT = {
    **'ACCESS_TOKEN_LIFETIME'**: timedelta(days=7),
}
```

静力学的一些标准材料:

```
STATIC_URL = **'/static/'** STATIC_ROOT = os.path.join(BASE_DIR, **'static/'**)
```

迁移数据库:

```
python manage.py migrate
```

并确保您的应用程序能够正确运行:

```
python manage.py runserver
```

# 定义模型

我们将只使用一张桌子。所以这部分很简单。

编辑 *app/models.py* 文件并添加该模型:

```
**class** Order(models.Model):
    date = models.DateField(blank=**False**)
    item = models.CharField(max_length=100, blank=**False**)
    price = models.DecimalField(decimal_places=2, max_digits=10, default=0)
    quantity = models.DecimalField(decimal_places=2, max_digits=10, default=0)
    amount = models.DecimalField(decimal_places=2, max_digits=10, default=0)

    **def** __str__(self):
        **return f"{**self.date**}: {**self.item**}"

    class** Meta:
        ordering = [**"-id"**]
```

我们想要编辑表格中的日期、项目、价格和数量。金额将自动计算。而且我们希望新记录出现在顶部，所以我们在这里添加了相应的“排序”。

# 添加身份验证(带测试)

我们将使用 JWT 进行身份验证。如果你想写 API，这是最好的方法。你可以在这里阅读更多关于 JWT [的信息。](https://jwt.io/)

对于姜戈，有一个图书馆。我们已经实现了一半:我们已经用 pip 安装了这个库，并在 settings.py 中设置了它。

现在，为了让它工作，只需将它添加到 urls.py:

```
...
**from** sales **import** settings
**from** django.conf.urls.static **import** static

urlpatterns = [
    path(**'admin/'**, admin.site.urls),
    path(**'api/token/'**, TokenObtainPairView.as_view(), name=**'token_obtain_pair'**),
    path(**'login'**, views.login_view),
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

现在，您可以尝试使用登录和密码向“localhost:8000/api/token”发送 POST 请求来获取令牌。但在此之前，您需要至少创建一个超级用户:

```
python manage.py createsuperuser
```

**可选。**您可以像这样在 curl 中测试它(在单独的终端/cmd 中运行服务器):

```
curl -X POST -H 'Content-Type: Application/JSON' --data '{"username":"yourusername", "password":"yourpassword"}' localhost:8000/api/token/
```

您应该会看到这样的响应:

```
{"refresh":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTYxODI0MTQ1MiwianRpIjoiZTIzYTQwOTYwMGRiNDdjZjlmYTIxZGZlZDgyMDczNzUiLCJ1c2VyX2lkIjoxfQ.hi2soO9TU0Cbqrl9_lE3cixO9jbuO9LtB1J-7ax_JFQ","access":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjE4NzU5ODUyLCJqdGkiOiJkMjk4ZmRmNmNmZDc0MWYyYTZmYzgzYjBhZmNjMzRjNSIsInVzZXJfaWQiOjF9.zXnFqbawUUNTVjOsYUtNiJDtYF-axDHKBZ2phdUAU7Q"}
```

因此，我们得到“刷新”和“访问”令牌作为响应。为简单起见，我们将只使用“access”令牌。

现在，让我们用姜戈来测试一下。在“sales”文件夹中，创建一个名为“tests_jwt.py”的文件。文件名以“tests”开头很重要。

```
**from** django.test **import** TestCase
**from** django.contrib.auth.models **import** User
**import** json

test_users = [
    {**"username"**: **"testuser1"**, **"password"**: **"testpassword1"**},
    {**"username"**: **"testuser2"**, **"password"**: **"testpassword2"**},
]

**class** LoginTest(TestCase):
    **def** setUp(self):
        **for** user **in** test_users:
            new_user = User.objects.create(username=user[**"username"**])
            new_user.set_password(user[**"password"**])
            new_user.save()

    **def** test_login(self):
        USER1 = test_users[0]
        res = self.client.post(**'/api/token/'**,
                               data=json.dumps({
                                   **'username'**: USER1[**"username"**],
                                   **'password'**: USER1[**"password"**],
                               }),
                               content_type=**'application/json'**,
                               )
        result = json.loads(res.content)
        self.assertTrue(**"access" in** result)
```

这里，我们创建一个新用户，从相应的 URL 获取令牌，并测试响应中是否包含“access”。

要运行测试，只需输入:

```
python manage.py test
```

# 为销售订单 API 添加测试

我们还没有构建 API，但是我们首先运行测试。为什么？

比方说，您已经开始实现 API，并且想要测试它。你会怎么做？使用 curl？好吧。但是您可能不希望每次更改代码中的某些内容时都运行大量的 curls。东西坏了怎么办？你永远不会知道这一点。

这就是为什么对于 API 来说，遵循 TDD(测试驱动开发)方法是好的。

在“app”文件夹中，创建一个名为“tests_orders.py”的文件。本章中的所有内容都应该添加到这个文件中。

我们从设置开始:

```
**from** django.test **import** TestCase
**from** django.contrib.auth.models **import** User
**import** json

test_user = {**"username"**: **"testuser"**, **"password"**: **"testpassword"**}

**class** OrdersTest(TestCase):
    **def** setUp(self):
        new_user = User.objects.create(
            username=test_user[**"username"**])
        new_user.set_password(test_user[**"password"**])
        new_user.save()

    **def** get_token(self):
        res = self.client.post(**'/api/token/'**,
           data=json.dumps({
               **'username'**: test_user[**"username"**],
               **'password'**: test_user[**"password"**],
           }),
           content_type=**'application/json'**,
           )
        result = json.loads(res.content)
        self.assertTrue(**"access" in** result)
        **return** result[**"access"**]
```

在运行这个类中的每个测试之前，清理数据库并调用这个“setUp”方法。不用担心，测试是在一个单独的数据库中运行的，所以你的数据不会受到影响。如果您使用的不是 SQLite，请确保您的用户有足够的权限创建数据库。

我们还需要获取每个测试的令牌，因此我们编写了名为“get_token”的帮助器方法。

测试 1。确保未经授权的用户不能发布新数据。响应代码应该是 401(已验证)

```
**def** test_add_orders_forbidden(self):
    res = self.client.post(**'/api/orders/'**,
       data=json.dumps({
           **'date'**: **"2020-01-01"**,
           **'item'**: **"Hard Drive"**,
           **'price'**: 100,
           **'quantity'**: 10,
       }),
       content_type=**'application/json'**,
       )
    self.assertEquals(res.status_code, 401)
    res = self.client.post(**'/api/orders/'**,
       data=json.dumps({
           **'date'**: **"2020-01-01"**,
           **'item'**: **"Hard Drive"**,
           **'price'**: 100,
           **'quantity'**: 10,
       }),
       content_type=**'application/json'**,
       HTTP_AUTHORIZATION=**f'Bearer WRONG TOKEN'** )
    self.assertEquals(res.status_code, 401)
```

测试 2。授权用户可以发帖。我们检查响应是否正确。响应代码应该是 201(已创建)。

```
**def** test_add_orders_ok(self):
    token = self.get_token()
    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **"Hard Drive"**,
                               **'price'**: 100,
                               **'quantity'**: 10,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 201)
    result = json.loads(res.content)[**"data"**]
    self.assertEquals(result[**"date"**], **'2020-01-01'**)
    self.assertEquals(result[**"item"**], **'Hard Drive'**)
    self.assertEquals(result[**"price"**], 100)
    self.assertEquals(result[**"quantity"**], 10)
```

测试 3。我们不应该发布“错误”的数据。在这种情况下，响应代码应该是 400(错误的请求)

```
**def** test_add_orders_wrong_data(self):
    token = self.get_token()
    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **"Hard Drive"**,
                               **'price'**: -1,
                               **'quantity'**: 10,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 400)

    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **"Hard Drive"**,
                               **'price'**: 1,
                               **'quantity'**: -10,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 400)

    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **""**,
                               **'price'**: 1,
                               **'quantity'**: 10,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 400)
```

测试 4。确保金额是根据价格和数量计算的。我们力量= 10000，应该忽略。相反，我们应该得到 5*7=35。

```
**def** test_add_orders_calculate(self):
    token = self.get_token()
    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **"Hard Drive"**,
                               **'price'**: 5,
                               **'quantity'**: 7,
                               **'amount'**: 10000,  *# should be ignored* }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 201)
    result = json.loads(res.content)[**"data"**]
    self.assertEquals(result[**"amount"**], 35)
```

测试 5。测试 GET 方法。

```
**def** test_get_records(self):
    token = self.get_token()
    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **"Hard Drive"**,
                               **'price'**: 5,
                               **'quantity'**: 7,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 201)
    id1 = json.loads(res.content)[**"data"**][**"id"**]

    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-02-02"**,
                               **'item'**: **"Monitor"**,
                               **'price'**: 20,
                               **'quantity'**: 30,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 201)
    id2 = json.loads(res.content)[**"data"**][**"id"**]

    res = self.client.get(**'/api/orders/'**,
                          content_type=**'application/json'**,
                          HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )

    self.assertEquals(res.status_code, 200)
    result = json.loads(res.content)[**"data"**]
    self.assertEquals(len(result), 2)  *# 2 records* self.assertTrue(result[0][**"id"**] == id1 **or** result[1][**"id"**] == id1)
    self.assertTrue(result[0][**"id"**] == id2 **or** result[1][**"id"**] == id2)

    res = self.client.get(**f'/api/orders/{**id1**}/'**,
                          content_type=**'application/json'**,
                          HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 200)
    result = json.loads(res.content)[**"data"**]
    self.assertEquals(result[**"date"**], **'2020-01-01'**)
    self.assertEquals(result[**"item"**], **'Hard Drive'**)
    self.assertEquals(result[**"price"**], 5)
    self.assertEquals(result[**"quantity"**], 7)
    self.assertEquals(result[**"amount"**], 35)
```

测试 6。使用 PUT 和 DELETE 方法的场景。

```
**def** test_put_delete_records(self):
    token = self.get_token()
    res = self.client.post(**'/api/orders/'**,
                           data=json.dumps({
                               **'date'**: **"2020-01-01"**,
                               **'item'**: **"Hard Drive"**,
                               **'price'**: 5,
                               **'quantity'**: 7,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 201)
    id = json.loads(res.content)[**"data"**][**"id"**]

    res = self.client.put(**f'/api/orders/{**id**}/'**,
                           data=json.dumps({
                               **'date'**: **"2020-02-02"**,
                               **'item'**: **"Monitor"**,
                               **'price'**: 50,
                               **'quantity'**: 70,
                           }),
                           content_type=**'application/json'**,
                           HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )

    self.assertEquals(res.status_code, 200)
    result = json.loads(res.content)[**"data"**]
    self.assertEquals(result[**"date"**], **'2020-02-02'**)

    res = self.client.get(**f'/api/orders/{**id**}/'**,
                          content_type=**'application/json'**,
                          HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 200)
    result = json.loads(res.content)[**"data"**]
    self.assertEquals(result[**"date"**], **'2020-02-02'**)
    self.assertEquals(result[**"item"**], **'Monitor'**)
    self.assertEquals(result[**"price"**], 50)
    self.assertEquals(result[**"quantity"**], 70)
    self.assertEquals(result[**"amount"**], 3500)

    res = self.client.delete(**f'/api/orders/{**id**}/'**,
                          content_type=**'application/json'**,
                          HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 410)  *# Gone* res = self.client.get(**f'/api/orders/{**id**}/'**,
                          content_type=**'application/json'**,
                          HTTP_AUTHORIZATION=**f'Bearer {**token**}'** )
    self.assertEquals(res.status_code, 404)  *# Not found*
```

如果您现在运行测试:

```
python manage.py test
```

然后应该运行 7 个测试，包括身份验证测试。当然，其中 6 个应该会失败。

现在我们已经为 API 实现做好了准备。

# 实现 API: GET 和 DELETE

导入 views_order 并将这两行添加到 *urls.py* 中的路径:

```
...
**from** app **import** views_orders
...
path(**'api/orders/'**, views_orders.orders),
path(**'api/orders/<int:order_id>/'**, views_orders.order),
...
```

会出现一个错误，没有“views_orders”文件。这是因为我们还没有创建它。

创建这个文件: *app/views_orders.py*

我们的 API 会在那里。

我们从导入所需的库和一个小助手函数开始:

```
**from** rest_framework.decorators **import** api_view
**from** django.shortcuts **import** HttpResponse
**from** rest_framework **import** status
**from** django.forms.models **import** model_to_dict
**from** django.core.exceptions **import** ObjectDoesNotExist
**from** app.models **import** Order
**import** json
**import** datetime

**def** serialize_order(order):
    serialized = model_to_dict(order)
    serialized[**"date"**] = str(order.date)
    serialized[**"amount"**] = float(order.amount)
    serialized[**"price"**] = float(order.price)
    serialized[**"quantity"**] = float(order.quantity)
    **return** serialized
```

这个自制的序列化器将帮助我们从数据库行(对象)创建一个字典。

和“删除”方法的 API:

```
@api_view([**'GET'**, ])
**def** orders(request):
    **if** request.user.is_anonymous:
        **return** HttpResponse(json.dumps({**"detail"**: **"Not authorized"**}), status=status.HTTP_401_UNAUTHORIZED)

    **if** request.method == **"GET"**:
        orders_data = Order.objects.all()

        orders_count = orders_data.count()

        page_size = int(request.GET.get(**"page_size"**, **"10"**))
        page_no = int(request.GET.get(**"page_no"**, **"0"**))
        orders_data = list(orders_data[page_no * page_size:page_no * page_size + page_size])

        orders_data = [serialize_order(order) **for** order **in** orders_data]
        **return** HttpResponse(json.dumps({**"count"**: orders_count, **"data"**: orders_data}), status=status.HTTP_200_OK)

    **return** HttpResponse(json.dumps({**"detail"**: **"Wrong method"**}), status=status.HTTP_501_NOT_IMPLEMENTED)

@api_view([**'GET'**, **'DELETE'**])
**def** order(request, order_id):
    **if** request.user.is_anonymous:
        **return** HttpResponse(json.dumps({**"detail"**: **"Not authorized"**}), status=status.HTTP_401_UNAUTHORIZED)

    **try**:
        order = Order.objects.get(pk=order_id)
    **except** ObjectDoesNotExist:
        **return** HttpResponse(json.dumps({**"detail"**: **"Not found"**}), status=status.HTTP_404_NOT_FOUND)

    **if** request.method == **"GET"**:
        **return** HttpResponse(json.dumps({**"data"**: serialize_order(order)}), status=status.HTTP_200_OK) **if** request.method == **"DELETE"**:
        order.delete()
        **return** HttpResponse(json.dumps({**"detail"**: **"deleted"**}), status=status.HTTP_410_GONE) **return** HttpResponse(json.dumps({**"detail"**: **"Wrong method"**}), status=status.HTTP_501_NOT_IMPLEMENTED)
```

我们首先检查您是否被授权执行这些操作。如果没有，立即回复 401，退出。

现在，一些测试应该通过了。

# 实现 API: POST 和 PUT

POST 和 PUT 方法共享一些公共代码。也就是说，它们将请求中的字段“复制”到订单对象中。他们还应该检查数据是否正确，并计算金额。

这就是为什么有一个单独的功能是一个好主意:

```
**def** save_order(request, order, success_status):
    errors = []
    item = request.data.get(**"item"**, **""**)
    **if** item == **""**:
        errors.append({**"item"**: **"This field is required"**})

    **try**:
        price = request.data.get(**"price"**, **""**)
        **if** price == **""**:
            errors.append({**"price"**: **"This field is required"**})
        **else**:
            price = int(price)
            **if** price < 0:
                errors.append({**"price"**: **"Price cannot be negative"**})
    **except** ValueError:
        errors.append({**"price"**: **"Could not parse field"**})

    **try**:
        quantity = request.data.get(**"quantity"**, **""**)
        **if** quantity == **""**:
            errors.append({**"quantity"**: **"This field is required"**})
        **else**:
            quantity = int(quantity)
            **if** quantity < 0:
                errors.append({**"quantity"**: **"Quantity cannot be negative"**})
    **except** ValueError:
        errors.append({**"quantity"**: **"Could not parse field"**})

    date = request.data.get(**"date"**, **""**)
    **if** date == **""**:
        date = datetime.datetime.now()

    **if** len(errors) > 0:
        **return** HttpResponse(json.dumps(
            {
                **"errors"**: errors
            }), status=status.HTTP_400_BAD_REQUEST)

    **try**:
        order.date = date
        order.item = item
        order.price = price
        order.quantity = quantity
        order.amount = price * quantity
        order.save()
    **except** Exception **as** e:
        **return** HttpResponse(json.dumps(
            {
                **"errors"**: {**"Order"**: str(e)}
            }), status=status.HTTP_400_BAD_REQUEST)

    **return** HttpResponse(json.dumps({**"data"**: serialize_order(order)}), status=success_status)
```

这个函数非常简单明了。它将数据从请求移动到订单。

现在，有了这些，我们可以通过添加 POST 和 PUT 方法来扩展我们的 API:

```
@api_view([**'GET'**, **'POST'**])
**def** orders(request):
    **if** request.user.is_anonymous:
        **return** HttpResponse(json.dumps({**"detail"**: **"Not authorized"**}), status=status.HTTP_401_UNAUTHORIZED)

    **if** request.method == **"GET"**:
        orders_data = Order.objects.all()

        orders_count = orders_data.count()

        page_size = int(request.GET.get(**"page_size"**, **"10"**))
        page_no = int(request.GET.get(**"page_no"**, **"0"**))
        orders_data = list(orders_data[page_no * page_size:page_no * page_size + page_size])

        orders_data = [serialize_order(order) **for** order **in** orders_data]
        **return** HttpResponse(json.dumps({**"count"**: orders_count, **"data"**: orders_data}), status=status.HTTP_200_OK)

    **if** request.method == **"POST"**:
        order = Order()
        **return** save_order(request, order, status.HTTP_201_CREATED)

    **return** HttpResponse(json.dumps({**"detail"**: **"Wrong method"**}), status=status.HTTP_501_NOT_IMPLEMENTED)

@api_view([**'GET'**, **'PUT'**, **'DELETE'**])
**def** order(request, order_id):
    **if** request.user.is_anonymous:
        **return** HttpResponse(json.dumps({**"detail"**: **"Not authorized"**}), status=status.HTTP_401_UNAUTHORIZED)

    **try**:
        order = Order.objects.get(pk=order_id)
    **except** ObjectDoesNotExist:
        **return** HttpResponse(json.dumps({**"detail"**: **"Not found"**}), status=status.HTTP_404_NOT_FOUND)

    **if** request.method == **"GET"**:
        **return** HttpResponse(json.dumps({**"data"**: serialize_order(order)}), status=status.HTTP_200_OK)

    **if** request.method == **"PUT"**:
        **return** save_order(request, order, status.HTTP_200_OK)

    **if** request.method == **"DELETE"**:
        order.delete()
        **return** HttpResponse(json.dumps({**"detail"**: **"deleted"**}), status=status.HTTP_410_GONE)

    **return** HttpResponse(json.dumps({**"detail"**: **"Wrong method"**}), status=status.HTTP_501_NOT_IMPLEMENTED)
```

现在，所有测试都应该通过了。如果没有，请在继续下一章之前修复错误。因为此时，您的后端应该已经准备好了。恭喜你！

如果你努力通过所有测试，将你的代码与 GitHub 中的[进行比较。](https://github.com/timurbakibayev/crud_django_react)

![](img/5879ed28b1d084b92cc1d9ff25b01a73.png)

# 用 React JS 初始化前端

我们首先在新文件夹“app/templates”中添加一个名为“index.html”的新文件。这是您将在本教程结束时看到的文件树:

![](img/3de96cba6cf216d041316d9fddb89716.png)

文件树。确保将文件添加到正确的文件夹中。

编辑“index.html”如下:

```
<!DOCTYPE **html**>
<**html lang="en"**>
<**head**>
  <**link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous"**>
  <**meta charset="UTF-8"**>
  <**title**>Sales Orders Management System</**title**>
</**head**>
<**body**>
  {% **include "react_and_bootstrap.html"** %}
  <**script type="text/jsx" src="/static/js/api.js"**></**script**>
  <**script type="text/jsx" src="/static/js/sales_page.js"**></**script**>
</**body**>
</**html**>
```

添加“login.html”，它非常类似:

```
<!DOCTYPE **html**>
<**html lang="en"**>
<**head**>
  <**meta charset="UTF-8"**>
  <**link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous"**>
  <**title**>Login</**title**>
</**head**>
<**body**>
  {% **include "react_and_bootstrap.html"** %}
  <**script type="text/jsx" src="/static/js/api.js"**></**script**>
  <**script type="text/jsx" src="/static/js/login_page.js"**></**script**>
</**body**>
</**html**>
```

你可以看到我们包含了“react_and_bootstrap.html”。将此文件添加到上面树中的“index.html”旁边，并添加以下内容:

```
<**div id="reactAppContainer"**></**div**>
  <**script src="https://unpkg.com/babel-standalone@6/babel.min.js"**></**script**>
  <**script src="//cdn.jsdelivr.net/npm/sweetalert2@10"**></**script**>
  <**script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin**></**script**>
  <**script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin**></**script**>
<**script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/js/bootstrap.bundle.min.js" integrity="sha384-JEW9xMcG8R+pH31jmWH6WWP0WintQrMb4s7ZOdauHnUtxwoG2vI5DkLtS3qm9Ekf" crossorigin="anonymous"**></**script**>
```

这是 React 的“开发版”。在部署到生产环境之前，确保用下面两行代码替换 react 脚本(脚本编号 3 和 4):

```
<**script src="https://unpkg.com/react@17/umd/react.production.min.js" crossorigin**></**script**>
<**script src="https://unpkg.com/react-dom@17/umd/react-dom.production.min.js" crossorigin**></**script**>
```

现在创建 ***两个文件*** 名为“sales_page.js”和“login_page.js ”,内容很简单:

```
**'use strict'**;
**const *e*** = React.createElement;

**function** *App*() {
  **return** (
    <**div**>
      <**h1**>Hello, World!</**h1**>
    </**div**>
  );
}

**const *domContainer*** = ***document***.querySelector(**'#reactAppContainer'**);
ReactDOM.render(
  ***e***(*App*),
  ***domContainer*** );
```

为了在 Django 中显示这些 HTML 文件，我们需要在 urls.py 中添加两个路径:

```
**from** app **import** views
...
path(**''**, views.index),
path(**'login'**, views.login_view),
```

当然，我们需要这些功能。

将此内容添加到 app/views.py 文件:

```
**from** django.shortcuts **import** render

**def** index(request):
    context = {}
    **return** render(request, **"index.html"**, context=context)

**def** login_view(request):
    context = {}
    **return** render(request, **"login.html"**, context=context)
```

您应该能够运行服务器并看到“Hello，World！”消息。

否则，在继续下一章之前解决问题。

# 从前端验证

接下来，我们准备这个登录视图。

![](img/106dbea00aeb8de4728f0c44eb820ad2.png)

首先，为此创建 API 消费者。

再一次，看看这棵树，

![](img/3de96cba6cf216d041316d9fddb89716.png)

并创建 api.js 文件:

```
**const** *login_api* = **async** (username, password, success, fail) => {
  **const** response = **await** *fetch*(
        **`/api/token/`**,
        {
            **method**: **'POST'**,
            **headers**: {
                **'Accept'**: **'application/json'**,
                **'Content-Type'**: **'application/json'**,
            },
            **body**: ***JSON***.stringify({
              **"username"**: username,
              **"password"**: password,
            })
        }
    );
  **const** text = **await** response.text();
  **if** (response.**status** === 200) {
    ***console***.log(**"success"**, ***JSON***.parse(text));
    success(***JSON***.parse(text));
  } **else** {
    ***console***.log(**"failed"**, text);
    ***Object***.entries(***JSON***.parse(text)).forEach(([key, value])=>{
      fail(**`**${key}**:** ${value}**`**);
    });
  }
};
```

稍后我们将在这里添加更多的 API 消费者。将 API 调用隔离到一个单独的文件中是很好的，因为如果 API 中有什么变化，我们只想在一个地方改变它。

这个函数简单地向 **/api/token/发出 POST 请求。**然后根据结果调用**成功**或**失败**函数。

转到 login_page.js，将其替换为以下内容:

```
**'use strict'**;
**const *e*** = React.createElement;

**function** *App*() {
  **const** [username, setUsername] = React.useState(**""**);
  **const** [password, setPassword] = React.useState(**""**);
  **const** [message, setMessage] = React.useState(**""**);

  **const** success = **async** (text)=> {
    ***console***.log(**"Yeah! Authenticated!"**);
    **await *localStorage***.setItem(**"salesToken"**, text.access);
    ***window***.**location** = **"/"**;
  };

  **const** tryLogin = **async** (e) => {
    e.preventDefault();
    ***console***.log(**"Loggin in with"**, username, password);
    **await** *login_api*(username, password, success, (text)=>{setMessage(text)});
  };

  **return** (
      <**div style=**{{**width**: **"400px"**, **margin**: **"auto"**, **marginTop**: **"200px"**,
        **boxShadow**: **"5px 5px 20px #cccccccc"**,
        **padding**: **"1em"** }}>
        <**form**>
          <**div className="mb-3"**>
            <**label htmlFor="username" className="form-label"**>Username</**label**>
            <**input autoFocus type="text" className="form-control" id="username" placeholder="username"
              onChange=**{(e)=>{setUsername(e.**target**.**value**)}} **value=**{username}/>
          </**div**>
          <**div className="mb-3"**>
            <**label htmlFor="password" className="form-label"**>Password</**label**>
            <**input type="password" className="form-control" id="password" placeholder="password"
              onChange=**{(e)=>{setPassword(e.**target**.**value**)}} **value=**{password}/>
          </**div**>
          <**div style=**{{**margin**: **"1em"**, **color**: **"red"**}}>{message}</**div**>
          <**button type="submit" className="btn btn-primary" onClick=**{tryLogin}>Login</**button**>
        </**form**>
      </**div**>
  );
}

**const *domContainer*** = ***document***.querySelector(**'#reactAppContainer'**);
ReactDOM.render(
  ***e***(*App*),
  ***domContainer*** );
```

我们在这里使用 Bootstrap。现在，您可以在浏览器中导航/登录，先测试不正确的用户名和密码，然后测试正确的用户名和密码。

恭喜你。我们已经走了一半了！

# 显示订单表(GET)

![](img/12a282d7fd81ede634c4d42ef80c8451.png)

首先，从 api.js 中的 GET API 调用开始:

```
**const** *get_orders_api* = **async** (pageNo=**""**, success, fail) => {
  **const** token = **await *localStorage***.getItem(**"salesToken"**);
  **if** (token === **null**) {
    ***console***.log(**"No credentials found, redirecting..."**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **const** response = **await** *fetch*(
        **`/api/orders/?page_no=**${pageNo}**`**,
        {
            **method**: **'GET'**,
            **headers**: {
                **'Content-Type'**: **'Application/JSON'**,
                **'Authorization'**: **`Bearer** ${token}**`**,
            }
        }
    );
  **const** text = **await** response.text();
  **if** (response.**status** === 401) {
    ***console***.log(**"Token not valid"**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **if** (response.**status** === 200) {
    ***console***.log(**"success"**, ***JSON***.parse(text));
    success(***JSON***.parse(text));
  } **else** {
    ***console***.log(**"failed"**, text);
    ***Object***.entries(***JSON***.parse(text)).forEach(([key, value])=>{
      fail(**`**${key}**:** ${value}**`**);
    });
  }
};
```

这里我们已经关心了页码。默认情况下(直到我们实现分页)，它将始终是第一页。

转到 sales_page.js，从这些常量开始。我们以后会用到它们:

```
**function** *App*() {
  **const** [list, setList] = React.useState([]);
  **const** [pages, setPages] = React.useState([]);
  **const** [page, setPage] = React.useState(0);
  **const** [showModal, setShowModal] = React.useState(**false**);
  **const** [modalDescription, setModalDescription] = React.useState(**""**);
  **const** [itemId, setItemId] = React.useState(**null**);
  **const** [error, setError] = React.useState(**""**);
  **const** [item, setItem] = React.useState(**""**);
  **const** [price, setPrice] = React.useState(0);
  **const** [quantity, setQuantity] = React.useState(0);
```

这里，useState 是旧的 React“状态”的简化版本。这个函数(useState)返回一对:一个状态和一个改变这个状态的函数。例如，list 和 setList。这里 setList 是一个改变列表的异步函数。诸如此类。

每当我们加载一些数据时，我们需要刷新上面的一些变量，即 list、pages 和 page。为此，我们编写了这个小函数:

```
**const** success = (data) => {
  setList(data.**data**);
  **const** newPages = [];
  **if** (data.count > 10) {
    **for** (**let** i=0; i<***Math***.ceil(data.count / 10); i++) {
      newPages.push({
        **name**: (i+1).toString(),
        **page**: i,
      });
      ***console***.log(**"page"**,i);
    }
    **if** (page > newPages.**length**-1) {
      setPage(page-1);
    }
  } **else** {
    setPage(0);
  }
  setPages(newPages);
};
```

这里如果数据库中有超过 10 个条目，我们显示分页。请注意，我们的 API 可能在“数据”中只返回 10 条记录，但也会通知我们在“计数”中有 50 项。所以我们依靠这里的计数。

如果您已经编写了 API 调用，那么获取数据就很简单:

```
**const** getData = ()=>{
  *get_orders_api*(page, success, (text)=>{***console***.log(**"Error: "**, text)});
};
React.useEffect(()=>{
  getData();
}, [page]);
```

这里我们使用 useEffect 函数。在我们的例子中，我们希望在页面加载时和每次“page”变量改变时启动它。这就是为什么我们在这里把`[page]`放在最后。它是 useEffect 方法的“依赖项”。

要注销，我们编写这个简单的函数:

```
**const** logout = **async** (e)=>{
  **await *localStorage***.setItem(**"salesToken"**,**null**);
  ***window***.**location** = **"/login"**;
};
```

就是这样。只剩下呈现页面了:

```
**return** (
    <**div**>
      <**div style=**{{**maxWidth**: **"800px"**, **margin**: **"auto"**, **marginTop**: **"1em"**, **marginBottom**: **"1em"**,
                    **padding**: **"1em"**}} **className="shadow"**>
        <**div style=**{{**display**: **"flex"**, **flexDirection**: **"row"**}}>
          <**span**>Super Orders App</**span**>
          <**a className="btn btn-light" style=**{{**marginLeft**: **"auto"**}} **onClick=**{logout}>Logout</**a**>
        </**div**>
      </**div**>
      <**div style=**{{**maxWidth**: **"800px"**, **margin**: **"auto"**, **marginTop**: **"1em"**, **marginBottom**: **"1em"**,
                    **padding**: **"1em"**}} **className="shadow"**>
        <**div style=**{{**display**: **"flex"**, **flexDirection**: **"row"**, **marginBottom**: **"5px"**}}>
          {pages.**length** > 0 && <**nav className="d-lg-flex justify-content-lg-end dataTables_paginate paging_simple_numbers"**>
            <**ul className="pagination"**>
              <**li className=**{**"page-item "** + (page === 0?**"disabled"**:**""**)} **onClick=**{(e)=>{
                    e.preventDefault();
                    setPage(***Math***.max(page-1,0));
              }}><**a className="page-link" href="#" aria-label="Previous"**><**span
                  aria-hidden="true"**>«</**span**></**a**></**li**>
              {pages.map((el)=><**li key=**{**"page"** + el.**page**} **onClick=**{(e)=>{
                  setPage(el.**page**);
                }} **className=**{**"page-item "**+(page===el.**page**?**"active"**:**""**)}>
                <**a className="page-link" href="#"**>
                  {el.**name**}
                </**a**></**li**>)}
              <**li className=**{**"page-item "** + (page === pages.**length**-1?**"disabled"**:**""**)} **onClick=**{(e)=>{
                    setPage(***Math***.min(page+1,pages.**length**-1));
              }}><**a className="page-link" href="#" aria-label="Next"**><**span
                  aria-hidden="true"**>»</**span**></**a**></**li**>
            </**ul**>
          </**nav**>}
          <**a className="btn btn-light" style=**{{**marginLeft**: **"auto"**}}
             **onClick=**{newOrder}
          >New Sales Order</**a**>
        </**div**>
        <**table className="table table-hover caption-top"**>
          <**thead className="table-light"**>
          <**tr**>
            <**th**>id</**th**>
            <**th**>Date</**th**>
            <**th**>Item</**th**>
            <**th**>Price</**th**>
            <**th**>Quantity</**th**>
            <**th**>Amount</**th**>
            <**th**>Action</**th**>
          </**tr**>
          </**thead**>
          <**tbody**>
          { list.map((row)=>
            <**tr key=**{row.**id**}>
              <**td**>{row.**id**}</**td**>
              <**td**>{row.date}</**td**>
              <**td**>{row.**item**}</**td**>
              <**td**>{row.price}</**td**>
              <**td**>{row.quantity}</**td**>
              <**td**>{row.**amount**}</**td**>
              <**td**>
                <**a className="btn btn-light" style=**{{**marginLeft**: **"auto"**}}
                  **onClick=**{(e)=>{editOrder(row)}}>Edit</**a**>{**" "**}
                <**a className="btn btn-light" style=**{{**marginLeft**: **"auto"**}}
                  **onClick=**{(e)=>{deleteOrder(row.**id**)}}>Delete</**a**>
              </**td**>
            </**tr**>
          )}
          </**tbody**>
        </**table**>
      </**div**>
    </**div**>
  );
}
```

现在，您应该能够看到销售订单列表。在测试之前，确保向数据库中添加几个订单。在我们执行下一章“添加新订单”之前，您可以跳过这一部分。

# 实施“添加新订单”

![](img/d27db4b9ff7fdba478ad4d08d484210a.png)

当我们添加一个新订单时，我们希望弹出一个模态窗口。

首先，我们将 POST 方法添加到我们的 api.js 中:

```
**const** *post_order_api* = **async** (data, success) => {
  **const** token = **await *localStorage***.getItem(**"salesToken"**);
  **if** (token === **null**) {
    ***console***.log(**"No credentials found, redirecting..."**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **const** response = **await** *fetch*(
        **`/api/orders/`**,
        {
            **method**: **'POST'**,
            **headers**: {
                **'Content-Type'**: **'Application/JSON'**,
                **'Authorization'**: **`Bearer** ${token}**`**,
            },
            **body**: ***JSON***.stringify(data)
        }
    );
  **const** text = **await** response.text();
  **if** (response.**status** === 401) {
    ***console***.log(**"Token not valid"**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **if** (response.**status** === 201) {
    ***console***.log(**"success"**, ***JSON***.parse(text));
    success(***JSON***.parse(text));
  } **else** {
    ***console***.log(**"failed"**, text);
    ***Object***.entries(***JSON***.parse(text)).forEach(([key, value])=>{
      *fail*(**`**${key}**:** ${value}**`**);
    });
  }
};
```

我们先渲染一下。将此代码放在返回函数的第一个“

”之后:

```
<**div style=**{{**background**: **"#00000060"**}}
    **className=**{**"modal "** + (showModal?**" show d-block"**:**" d-none"**)} **tabIndex="-1" role="dialog"**>
  <**div className="modal-dialog shadow"**>
    <**form method="post"**>
    <**div className="modal-content"**>
      <**div className="modal-header"**>
        <**h5 className="modal-title"**>{modalDescription}</**h5**>
        <**button type="button" className="btn-close" onClick=**{()=>{setShowModal(**false**)}} **aria-label="Close"**></**button**>
      </**div**>
      <**div className="modal-body"**>
        <**label**>Item name</**label**>
          <**div className="form-group"**>
            <**input type="text" className="form-control" name="item" id="itemInput"
                   value=**{item} **onChange=**{(e)=>{setItem(e.**target**.**value**)}}
                   **placeholder="Item name"**/>
          </**div**>
        <**label style=**{{**marginTop**: **"1em"**}}>Price</**label**>
          <**div className="form-group"** >
            <**input type="number" className="form-control" placeholder="Price"
                   value=**{price} **onChange=**{(e)=>{setPrice(e.**target**.**value**)}}
                   **name="price"** />
          </**div**>
        <**label style=**{{**marginTop**: **"1em"**}}>Quantity</**label**>
          <**div className="form-group"**>
            <**input type="number" className="form-control"
                   value=**{quantity} **onChange=**{(e)=>{setQuantity(e.**target**.**value**)}}
                   **placeholder="Quantity" name="quantity"** />
          </**div**>
        <**small className="form-text text-muted"**>{error}</**small**>
      </**div**>
      <**div className="modal-footer"**>
        <**button type="button" className="btn btn-secondary" onClick=**{()=>{setShowModal(**false**)}} **data-bs-dismiss="modal"**>Close</**button**>
        <**button type="submit" className="btn btn-primary" onClick=**{saveOrder}>Save changes</**button**>
      </**div**>
    </**div**>
    </**form**>
  </**div**>
</**div**>
```

所以下一行应该是

```
<**div style=**{{**maxWidth**: **"800px"**, **margin**: **"auto"**, ... 
```

这里可以看到，只有当 showModal 变量为 true 时，才会显示模态形式。在表单内部，我们显示了模型的字段，它们使用变量 item、price 和 quantity。

接下来，当您按下提交功能时，应该会保存表单。添加此功能:

```
**const** saveOrder = (e)=>{
  e.preventDefault();
  setError(**""**);
  ***console***.log(**"saving new"**, item, price, quantity);
  **if** (item.**length** * price * quantity === 0)
    setError(**"Please enter item name, price and quantity"**);
  **else** {
    **if** (itemId === **null**)
      *post_order_api*({item, price, quantity}, ()=>{getData();});
    **else** *put_order_api*(itemId, {item, price, quantity}, ()=>{getData();});
    setShowModal(**false**);
  }
};
```

在这个函数中，我们已经为编辑做好了准备。你可以在这里看到`if (itemId === null)`部分。唯一的区别是“edit”设置 itemId。

当我们按下“新销售订单”按钮时，该表单应该会打开:

```
**const** newOrder = ()=>{
  setModalDescription(**"New order"**);
  setItemId(**null**);
  setItem(**""**);
  setPrice(0);
  setQuantity(0);
  setError(**""**);
  setShowModal(**true**);
  **const** itemInput = ***document***.getElementById(**"itemInput"**);
  *setTimeout*(()=>{itemInput && itemInput.focus()}, 1);
};
```

我们在这里重置所有变量，显示窗口，并将焦点设置到第一个字段，这样用户就不必单击它。

请注意，在聚焦之前，我们有一小段暂停时间。这是因为我们需要一些时间来打开模态窗口。如果由于某种原因，需要更多的时间来打开它，焦点不会被设置，但也不会有错误。因为我们通过计算`itemInput &&`来检查它。

现在，您可以添加任意数量的销售订单。添加超过 10 条记录时，您还应该看到分页。

# 实施“编辑订单”

![](img/dcdeec55a06718a6510e5e3feb42fdc1.png)

对于编辑，我们已经有了一个模态窗口。所以我们只需要添加一个函数:

```
**const** editOrder = (data)=>{
  setModalDescription(**"Edit order"**);
  setItemId(data.**id**);
  setItem(data.**item**);
  setPrice(data.price);
  setQuantity(data.quantity);
  setError(**""**);
  setShowModal(**true**);
  **const** itemInput = ***document***.getElementById(**"itemInput"**);
  *setTimeout*(()=>{itemInput && itemInput.focus()}, 1);
};
```

我们将变量 item、price 和 quantity 设置为记录中的值。其他的都一样，应该马上就能见效。

并将 PUT 调用添加到 api.js 文件中:

```
**const** *put_order_api* = **async** (saleId, data, success) => {
  **const** token = **await *localStorage***.getItem(**"salesToken"**);
  **if** (token === **null**) {
    ***console***.log(**"No credentials found, redirecting..."**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **const** response = **await** *fetch*(
        **`/api/orders/**${saleId}**/`**,
        {
            **method**: **'PUT'**,
            **headers**: {
                **'Content-Type'**: **'Application/JSON'**,
                **'Authorization'**: **`Bearer** ${token}**`**,
            },
            **body**: ***JSON***.stringify(data)
        }
    );
  **const** text = **await** response.text();
  **if** (response.**status** === 401) {
    ***console***.log(**"Token not valid"**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **if** (response.**status** === 200) {
    ***console***.log(**"success"**, ***JSON***.parse(text));
    success(***JSON***.parse(text));
  } **else** {
    ***console***.log(**"failed"**, text);
    ***Object***.entries(***JSON***.parse(text)).forEach(([key, value])=>{
      *fail*(**`**${key}**:** ${value}**`**);
    });
  }
};
```

# 实现“删除订单”

![](img/198c425e999ff1c231e06dfcac515bab.png)

这里我们想先问一下用户是否确定删除订单。为此，我们使用甜蜜警戒。它不仅美观，而且非常易于使用:

```
**const** deleteOrder = (orderId)=>{
  Swal.fire({
    **title**: **'Are you sure?'**,
    **text**: **"You won't be able to revert this!"**,
    **icon**: **'warning'**,
    **showCancelButton**: **true**,
    **confirmButtonColor**: **'#3085d6'**,
    **cancelButtonColor**: **'#d33'**,
    **confirmButtonText**: **'Yes, delete it!'** }).then((result) => {
    **if** (result.isConfirmed) {
      *delete_order_api*(orderId, ()=>{
        Swal.fire({
            **title**: **'Deleted!'**,
            **text**: **"Your order has been deleted!"**,
            **icon**: **'success'**,
            **timer**: 1000,
        });
        getData();
      });
    }
  });
};
```

当然还有删除 API 调用(api.js):

```
**const** *delete_order_api* = **async** (saleId, success) => {
  **const** token = **await *localStorage***.getItem(**"salesToken"**);
  **if** (token === **null**) {
    ***console***.log(**"No credentials found, redirecting..."**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  **const** response = **await** *fetch*(
        **`/api/orders/**${saleId}**/`**,
        {
            **method**: **'DELETE'**,
            **headers**: {
                **'Content-Type'**: **'Application/JSON'**,
                **'Authorization'**: **`Bearer** ${token}**`**,
            }
        }
    );
  **const** text = **await** response.text();
  **if** (response.**status** === 401) {
    ***console***.log(**"Token not valid"**);
    ***window***.**location** = **"/login"**;
    **return** [];
  }
  ***console***.log(response.**status**);
  **if** (response.**status** === 410) {
    ***console***.log(**"success"**, ***JSON***.parse(text));
    success(***JSON***.parse(text));
  } **else** {
    ***console***.log(**"failed"**, text);
    ***Object***.entries(***JSON***.parse(text)).forEach(([key, value])=>{
      *fail*(**`**${key}**:** ${value}**`**);
    });
  }
};
```

注意，在每个 API 调用中，如果响应代码是 401(未授权)，我们将用户重定向到“/log in”URL。

# GitHub 知识库

恭喜你！你做到了！如果有问题，去 GitHub 库，通过比较你的代码来检查问题所在:[https://github.com/timurbakibayev/crud_django_react](https://github.com/timurbakibayev/crud_django_react)

你不仅可以在 GitHub 上获得这个项目，还可以点击“使用这个模板”按钮来创建一个基于它的项目。

# 结论

是的，这么小的项目是需要时间的。我计划在一天内写完这篇文章，但是我花了大约一周的时间来实现它，花了两天的时间来写这篇文章。但是我们现在有了一个很好的模板，可以在其他项目中重复使用。

如果你喜欢这篇文章，请给我买杯咖啡:

[](https://www.buymeacoffee.com/timu) [## 铁木尔·巴卡耶夫正在编课程

### 嘿👋。。。你现在可以给我买杯咖啡了！

www.buymeacoffee.com](https://www.buymeacoffee.com/timu) 

看看我的其他文章，大部分是关于 Python 和 Django 的:

[](https://timurbakibayev.medium.com) [## 帖木儿·巴卡耶夫-中等

### 我们的病人将是一个只有 7 个字段的小 Django 应用程序。我在这里只展示模型:类…

timurbakibayev.medium.com](https://timurbakibayev.medium.com)