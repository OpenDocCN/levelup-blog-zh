# 消息队列介绍:使用 Django、Celery 和 RabbitMQ 在 30 分钟内构建一个时事通讯应用程序

> 原文：<https://levelup.gitconnected.com/introduction-to-message-queue-build-a-newsletter-app-using-django-celery-and-rabbitmq-in-30-min-6d484162391d>

消息队列广泛用于异步系统中。在数据密集型应用中，使用队列可以确保用户在完成复杂任务的同时获得快速体验。例如，当任务在后台完成时，您可以在 UI 中显示进度条。这允许用户从等待任务完成中解脱出来，因此可以在此期间做其他工作。

一个典型的请求-响应架构不会减少响应时间不可预测的地方，因为您有许多长时间运行的请求。如果您确信您的系统请求将会以指数或多项式的形式增长，那么队列将会非常有用。

![](img/b2e6b24efc4addcd820cea08df2faadf.png)

霍比工业在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

消息队列提供了一些有用的特性，比如持久性、路由和任务管理。消息队列是典型的“代理”，它通过提供其他服务可以访问的接口来促进消息传递。这个接口连接了创建消息的**生产者**和处理消息的**消费者**。

我们将建立一个简讯应用程序，用户可以订阅各种简讯，他们将定期收到他们的电子邮件。但是在我们继续之前，让我们理解 workers +消息队列的工作原理。

![](img/16fe58310478d7e08375c935db46c7c7.png)

信息排队

# 工人和消息队列

工人是“后台任务服务器”。当您的 web 服务器响应用户请求时，工作服务器可以在后台处理任务。这些工作人员可以用来发送电子邮件、对数据库进行大的修改、处理文件等。

工人通过消息队列被分配任务。例如，考虑存储大量消息的队列。它将以先进先出(FIFO)的方式进行处理。当一个工人变得可用时，它从队列的前面取出第一个任务并开始处理。如果我们有许多工人，每个人都按顺序完成一项任务。该队列确保每个工作线程一次只能获得一个任务，并且每个任务只能由一个工作线程处理。

我们将使用 **Celery** ，它是用于 **Python** web 应用**的任务队列实现，用于在 HTTP 请求-响应周期之外异步执行工作。我们还将使用 [**RabbitMQ**](https://www.rabbitmq.com/) ，这是部署最广泛的开源消息代理。它支持多种消息协议。**

# 构建简讯应用程序

我们将建立一个简讯应用程序，用户可以同时订阅各种简讯，并将定期通过电子邮件接收问题。

![](img/7fa052fcd6d0af34e18071a2fabe5e00.png)

产品概述

我们将有我们的通讯应用程序作为一个芹菜 Django 应用程序运行。每当作者发布新的一期，Django 应用程序就会发布一条消息，通过电子邮件将该期内容发送给使用 celery 的订户。芹菜工人将从经纪人那里收到任务，并开始发送电子邮件。

![](img/fccf365c29f159536f6a07c8438925ba.png)

基础设施

## **要求**

*   Python 3+版本
*   [Pipenv](https://pipenv.pypa.io/en/latest/)

## 设置 Django

在本地创建一个文件夹`newsletter`，在虚拟环境中安装 Django。内部文件夹运行:

```
pipenv shell
pipenv install django
```

创建应用程序:

```
django-admin startproject newsletter_site .
```

设置模型:

```
python manage.py migrate
```

确保它正常工作并访问 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) :

```
python manage.py runserver 8000
```

创建简讯应用程序:

```
python manage.py startapp newsletter
```

## 装置

*   安装芹菜
*   安装 dotenv 从环境中读取设置。
*   安装 psycopg2-binary 以连接 Postgres。

```
pipenv install celery
pipenv install python-dotenv
pipenv install psycopg2-binary
```

## 设置 Postgres 和 RabbitMQ

创建一个`docker-compose.yaml`在后台运行 Postgres 和 Rabbitmq。

```
version: '3'
services:
  db:
    image: postgres:13
    env_file:
      - .env
    ports:
      - 5432:5432
  rabbitmq:
    image: rabbitmq
    ports:
      - 5672:5672
```

## 配置设置. py

*   为了在我们的项目中包含该应用程序，我们需要在`newsletter_site/settings.py`的`[**INSTALLED_APPS**](https://docs.djangoproject.com/en/3.2/ref/settings/#std:setting-INSTALLED_APPS)`设置中添加对其配置类的引用。

```
INSTALLED_APPS = [
    'newsletter.apps.NewsletterConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

*   我们需要告诉芹菜怎么找到 RabbitMQ。所以，打开`settings.py`，加上这一行:

```
CELERY_BROKER_URL = os.getenv('CELERY_BROKER_URL') 
```

*   我们需要配置数据库设置:

```
uri = os.getenv('DATABASE_URL')

result = urlparse(uri)

database = result.path[1:]
user = result.username
password = result.password
host = result.hostname
port = result.port

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': database,
        'USER': user,
        'PASSWORD': password,
        'HOST': host,
        'PORT': port,
    }
}
```

*   我们需要在`settings.py`中配置 SMTP 服务器。SMTP 服务器是负责向用户发送电子邮件的邮件服务器。对于开发，您可以使用 Gmail SMTP 服务器，但这有一定的限制，如果您有 2 FA，这将不起作用。可以参考这篇[文章](https://dev.to/abderrahmanemustapha/how-to-send-email-with-django-and-gmail-in-production-the-right-way-24ab)。对于生产，您可以使用商业服务，如 sendgrid。

```
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = os.getenv('EMAIL_HOST')
EMAIL_USE_TLS = bool(os.getenv('EMAIL_USE_TLS'))
EMAIL_PORT = os.getenv('EMAIL_PORT')
EMAIL_HOST_USER = os.getenv('EMAIL_HOST_USER')
EMAIL_HOST_PASSWORD = os.getenv('EMAIL_HOST_PASSWORD')
```

作为参考，你可以在这里看到 settings.py [。](https://github.com/Nancy-Chauhan/newsletter/blob/main/newsletter_site/settings.py)

## 创建环境文件

*   创建一个`.env`文件并分配密码。

```
EMAIL_HOST=**{EMAIL_HOST}**
EMAIL_USE_TLS=True
EMAIL_PORT=**{EMAIL_PORT}**
EMAIL_HOST_USER=**{EMAIL_HOST_USER}**
EMAIL_HOST_PASSWORD=**{EMAIL_HOST_PASSWORD}**CELERY_BROKER_URL="pyamqp://localhost:5672"SECRET_KEY=**{SECRET_KEY}**DATABASE_URL=postgres://postgres:password@localhost:5432/postgresPOSTGRES_PASSWORD=password
APP_DOMAIN=*
DEBUG=True
```

## 芹菜

*   我们需要用一些配置选项来设置 Celery。在`newseletter_site`目录下创建一个名为`celery.py`的新文件:

```
import os

from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'newsletter_site.settings')

app = Celery('newsletter_site')

app.config_from_object('django.conf:settings', namespace='CELERY')

app.autodiscover_tasks()
```

## 设计和实施模型并配置管理员

这是我们试图建立的模式。模式在这里被实现[。创建一个内容相同的`newsletter/models.py`。](https://github.com/Nancy-Chauhan/newsletter/blob/main/newsletter/models.py)

![](img/d8468cdd76bbd2efbcd66638bc51adef.png)

模型设计

我们需要一个用户界面来管理时事通讯。为此，我们将使用 Django Admin。用这个[文件](https://github.com/Nancy-Chauhan/newsletter/blob/main/newsletter/admin.py)的内容创建一个`newsletter/admin.py`。

在`newsletter_site/urls.py`为管理员注册网址:

```
urlpatterns = [
    path('admin/', admin.site.urls),
]
```

# 运行应用程序

运行 docker-compose 启动依赖项:

```
docker-compose up
```

为我们的模型生成迁移:

```
python manage.py makemigrations
```

要将生成的迁移应用于数据库运行:

```
python manage.py migrate 
```

要创建登录用户，请运行以下命令并提供您的详细信息:

```
python manage.py createsuperuser
```

运行以下命令来运行应用程序，并打开[http://127 . 0 . 0 . 1:8000/Admin](http://127.0.0.1:8000/admin/)来打开 Django Admin:

```
python manage.py runserver
```

![](img/9ab51add01b7a58b75bcaa070df8b527.png)

运行芹菜:

```
celery -A newsletter_site worker --loglevel=INFO
```

添加新闻稿和订阅者，并为其订阅。创建一个问题并发送它。如果一切正常，您会在电子邮件中看到一个问题。

## 它是如何工作的？

当我们单击“发送”时，将执行以下操作:

```
def send(modeladmin, request, queryset):
    for issue in queryset:
        tasks.send_issue.delay(issue.id)

send.short_description = "send"
```

这段代码负责排队一个新任务，用 celery 发送一个问题。它将任务发布到 RabbitMQ。

```
@shared_task()
def send_issue(issue_id):
    issue = Issue.objects.get(pk=issue_id)
    for subscription in Subscription.objects.filter(newsletter=issue.newsletter):
        send_email.delay(subscription.subscriber.email, issue.title, issue.content)

@shared_task()
def send_email(email, title, content):
    send_mail(
        title,
        content,
        'newsletters@nancychauhan.in',
        [email],
        fail_silently=False,
    )
```

芹菜工人使用这些任务。当生产者发布任务时，工人运行相应的任务。

当我们发布`send_issue`任务时，我们确定时事通讯的订阅者，并发布子任务来发送实际的电子邮件。这种策略称为扇出。扇出是有用的，因为它允许我们在失败的情况下重试向单个用户发送电子邮件。

## 结论

在这篇文章中，我们看到了如何使用 RabbitMQ 作为带有 Celery 和 Django 的消息队列来发送批量电子邮件。这非常适合适合消息队列。如果请求不确定，或者流程需要长时间运行且占用大量资源，请使用消息队列。

您可以在这里找到完成的项目:

[](https://github.com/Nancy-Chauhan/newsletter) [## 南希-肖汉/时事通讯

### 在 GitHub 上创建一个帐户，为 Nancy-Chauhan/时事通讯的发展做出贡献。

github.com](https://github.com/Nancy-Chauhan/newsletter) 

感谢您的阅读！在评论框中分享您的反馈。