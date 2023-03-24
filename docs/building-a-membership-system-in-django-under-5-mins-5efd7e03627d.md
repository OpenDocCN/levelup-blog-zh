# 在 Django 建立会员系统不到 5 分钟

> 原文：<https://levelup.gitconnected.com/building-a-membership-system-in-django-under-5-mins-5efd7e03627d>

![](img/96168a49baeefb2e5bba21412b45da15.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

明白了逻辑，那么其他的一切都是空谈。

开发人员在他们的网站上销售特定服务时，经常会遇到实现某种会员系统的需求，我们马上就来做这件事。

本文只关注在现有的 Django 应用程序中构建或集成会员/订阅系统。处理支付可以使用 Paypal 或 Stripe API 实现，但这方面将是另一篇文章的一部分。

首先，我们将启动一个名为 Membership 的新应用。

```
./manage.py startapp membership
```

接下来，我们将进入 models.py 并开始添加我们将使用的必要模型。大多数实现将在数据库级完成，因此，正确定义表之间的关系将决定我们的成员资格应用程序的正确性。

我们将需要 3 个模型——一个**成员资格**模型，保存关于成员资格类型(免费、高级或其他)的信息，价格……一个**用户成员资格**模型，保存用户信息，最后一个订阅模型将处理用户订阅。

先说我们的会员课。

```
from django.db import models
from django.conf import settings# Create your models here.MEMBERSHIP_CHOICES = (
(‘Premium’, ‘pre’),
(‘Free’, ‘free’)
)class Membership(models.Model):
    slug = models.SlugField(null=True, blank=True)
    membership_type = models.CharField(
    choices=MEMBERSHIP_CHOICES, default=’Free’,
    max_length=30
      )
    price = models.DecimalField(default=0)def __str__(self):
       return self.membership_type
```

为了便于学习，我们创建了两个级别的会员资格，但这取决于您。slug 属性将用于 URL 模式。接下来，我们将讨论 UserMembership 类。

```
class UserMembership(models.Model):
    user = models.OneToOneField(settings.AUTH_USER_MODEL,     related_name=’user_membership’, on_delete=models.CASCADE) membership = models.ForeignKey(Membership, related_name=’user_membership’, on_delete=models.SET_NULL, null=True) def __str__(self):
       return self.user.username
```

这个类只包含两个属性 user 和 membership

*   **用户**:一对一关系，指向应用程序的用户模型。
*   **membership** :这是前面定义的成员模型的外键。

最后，我们将通过添加订阅模型来结束。

```
class Subscription(models.Model):
    user_membership = models.ForeignKey(UserMembership, related_name=’subscription’, on_delete=models.CASCADE) active = models.BooleanField(default=True) def __str__(self):
      return self.user_membership.user.username
```

这里我们有一个 UserMembership 的外键，然后添加另一个属性来检查 user_membership 是否是活动的。

现在进行迁移，然后通过运行。

```
./manage.py makemigrations membership
./manage.py migrate
```

正如我在开始时所说的，最具决定性的方面是在数据库级别正确处理您的实施，这是我们到目前为止所做的，现在我们需要看到它的行动。

从在`admin.py`中添加以下内容开始

```
### admin.py from .models import Membership, UserMembership, Subscription# Register your models here.admin.site.register(Membership)
admin.site.register(UserMembership)
admin.site.register(Subscription)
```

跳过管理界面，添加两个会员实体，免费和高级。

接下来我们需要添加一个视图函数，它将负责显示所有可用的会员计划。

```
## views.pyfrom django.views.generic import ListView
from membership.models import Membership, UserMembership, Subscriptionclass MembershipView(ListView):
    model = Membership
    template_name = 'memberships/list.html' def get_user_membership(self):
        user_membership_qs = UserMembership.objects.filter(user=self.request.user)
        if user_membership_qs.exists():
            return user_membership_qs.first()
        return None def get_context_data(self, *args, **kwargs):
        context = super().get_context_data(**kwargs)
        current_membership = self.get_user_membership(self.request)
        context['current_membership'] = str(current_membership.membership)
        return context
```

然后，我们需要连接将调用视图功能的 URL，并最终返回 HTML 模板。

```
### urls.pyfrom django.urls import path, include
from . import viewsapp_name = 'membership'urlpatterns = [
       path('memberships/', views.MembershipView.as_view(), name='select'),
]
```

HTML 模板。

```
% block content %}<**div** *class*="container"><**br**><**h1**>Choose a plan according to your needs</**h1**><**hr**><**div** *class*="row">{% for object in object_list %}<**div** *class*="col-sm-4 col-md-4"><**div** *class*="card" *style*="width: 18rem;">*<!-- <img class="card-img-top " src="{{ object.image.url }}" alt="Card image cap"> -->*<**div** *class*="card-body"><**h5** *class*="card-title">{{ object.membership_type }}</**h5**><**p** *class*="card-text">{{ object.desc | linebreaks }}</**p**></**div**><**ul** *class*="list-group list-group-flush"><**li** *class*="list-group-item"> ${{ object.price }}<**small**>/month</**small**></**li**></**ul**><**div** *class*="card-body">{% if object.membership_type != 'Free' %}<**form** *method*="POST" *action*="{% url 'web:select' %}">{% csrf_token %}{% if object.membership_type != current_membership %}<**button** *class*="btn btn-warning">Change</**button**>{% else %}<**small**>Your current plan</**small**>{% endif %}<**input** *type*="hidden" *name*="membership_type" *value*="{{ object.membership_type }}"></**form**>{% endif %}</**div**></**div**></**div**>{% endfor %}</**div**></**div**>{% endblock content %}
```

现在只能看到什么是可用的，我们需要在用户注册时处理用户成员的创建。打开包含处理用户注册的代码的文件(我在 signUpForm 中处理我的)。

```
### import datetimefrom django import forms
from django.forms import ModelForm
from django.contrib.auth.forms import UserCreationForm
from membership.models import Membership, UserMembership, Subscriptionclass SignUpForm(UserCreationForm):
    free_membership = Membership.objects.get(membership_type=’Free’) class Meta(UserCreationForm.Meta):
       model = User def save(self):
      user = super().save(commit=False)
      user.save() # Creating a new UserMembership
      user_membership = UserMembership.objects.create(user=user, membership=self.free_membership)
      user_membership.save() # Creating a new UserSubscription
      user_subscription = Subscription()
      user_subscription.user_membership = user_membership
      user_subscription.save()
      return user
```

我们所做的只是查询免费会员资格，然后将其分配给新注册的用户，最后返回该特定用户。

这是每个会员系统背后的逻辑，希望你能明白，如果没有，请随时留言，我很乐意跟进。

干杯。