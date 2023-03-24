# 使用 django-pgpubsub 以健壮和轻量级的方式异步处理数据库事件。

> 原文：<https://levelup.gitconnected.com/asynchronous-processing-of-database-events-in-a-robust-and-lightweight-manner-using-django-pgpubsub-cfb0dcf12803>

现代 web 开发中的一个常见模式是，在一些用户操作或数据库事件之后，需要异步处理数据。在本文中，我们将通过一个具体的例子来描述一种传统的方法，使用 [django 信号](https://docs.djangoproject.com/en/4.0/topics/signals/)和[芹菜信号](https://docs.celeryq.dev/en/stable/getting-started/introduction.html)来解决基于 Django/Postgres 的应用程序的这个问题。然后，我们将继续讨论这种方法的一些缺点，并演示如何使用 [django-pgpubsub](https://github.com/Opus10/django-pgpubsub) 来提供一个轻量级的、更健壮的解决方案。

![](img/0a274b19fd58e5ca89264bb624b12420.png)

## Easy:一个分享和评论科学文章的平台

Easy 是一个平台，用户可以在这个平台上撰写和发布科学文章，其他用户可以对文章发表评论。Easy 的产品负责人想要引入一个新功能:每当有人对一篇文章发表评论时，该文章的作者就会收到一封电子邮件，让他们了解新的评论。

此时， *Easy* 的技术基础设施是由单个 Postgres 数据库服务的单个 django 实例。有了这些信息，让我们来探索如何增加对这个新需求的支持。

## **设置**

我们有两个简单的 Django 模型:

```
**class** Article(models.Model):
    content = models.TextField()
    user = models.ForeignKey(contrib.auth.models.User)class Comment(models.Model):
    content = models.TextField()
    article = models.ForeignKey(Article)
    user = models.ForeignKey(contrib.auth.models.User)
```

每当用户在帖子上发表评论时，就会创建一个引用登录用户和发表评论的`Article`实例的`Comment`对象。因此，为了解决新的需求，我们需要找到一种方法，每当一个`Comment` is 对象被插入到数据库中时，发送一封电子邮件给正确的`User`。

## **信号**

Django 的[信号](https://docs.djangoproject.com/en/4.0/topics/signals/)允许我们在动作发生时调用回调。在我们的例子中，操作是创建一个评论，回调是一个向留下评论的文章作者发送电子邮件的函数。Django 内置的 [post_save](https://docs.djangoproject.com/en/4.0/ref/signals/#post-save) 信号非常适合这种情况:

```
from django.db.models.signals import post_save
from django.dispatch import receiverfrom email_utils import email @receiver(post_save, sender=Comment)
def email_article_author(sender, instance, **kwargs):
    article_author = instance.post.user
    email(user)
```

通过这个简单的添加，我们实际上已经为新的需求设计了一个解决方案。然而，这种解决方案有一个主要缺陷:电子邮件是在与评论创建相同的线程中发送的。这意味着发表评论的用户需要等待电子邮件发送，然后才能收到他们的评论已被保存的通知。此外，它在创建评论时引入了另一个失败点:如果评论碰巧是在一个[原子事务](https://docs.djangoproject.com/en/4.0/topics/db/transactions/)中创建的，那么当试图发送电子邮件时遇到的任何错误都将回滚评论的创建。

## **使用芹菜异步发送电子邮件**

我们需要将电子邮件逻辑移出用户线程，并让它异步发生。人们可以使用 python [Thread](https://realpython.com/intro-to-python-threading/) 对象来实现这一点，但这将很快耗尽资源。一个常见的选择是使用某种分布式消息处理框架，django 应用程序最流行的选择是 [Celery](https://docs.celeryq.dev/en/stable/getting-started/introduction.html) 。Celery 允许我们定义一个辅助进程，专门处理从用户线程接收到的消息。我们不会在这里详细讨论实现，但它可能看起来像这样:

```
# tasks.pyapp = Celery('tasks', broker='rabbitmq')**@**app.task
def email_user(user):
    email(user)# signals.py@receiver(post_save, sender=Comment)
def email_article_author(sender, instance, **kwargs):
    article_author = instance.post.user
    # send a message to email_user task running in another process
    email_user.delay(user) 
```

有了这个解决方案，我们的电子邮件逻辑现在从评论创建逻辑中分离出来，用同步解决方案解决了前面提到的问题。然而，上述解决方案仍然存在一些严重的缺陷:

*   **信号可能会被错过**:在常规的`Comment.objects.create`呼叫之后，总会呼叫一个`post_save`信号。当我们通过`Comment.objects.bulk_create`批量创建时，它将*而不是*被调用。因此，如果在某些时候 *Easy* 需要批量创建评论(例如，他们可能希望在一段时间内不活动的文章上留下自动评论)，我们的解决方案将意味着这些评论是在没有向文章作者发送邮件的情况下创建的。
*   **与 celery 沟通增加了另一个失败点:**虽然我们在添加评论时删除了电子邮件作为一个失败点，但是我们引入了与 celery(或者更确切地说是经纪人)沟通作为一个失败点。
*   **添加一个芹菜和一个代理在操作上是繁重的:**设置和维护芹菜框架在操作上是昂贵的。对于发送电子邮件这样简单的任务来说，这可能被认为是过分的。
*   **就评论事务**而言，与 Celery 的通信不是原子的:如果我们的评论是在原子事务中创建的，而该事务恰好在我们的 email_user 任务被异步调用后*回滚，那么我们最终会在作者的文章没有新评论时向作者发送电子邮件。然而，这可以使用 Django 的`transaction.on_commit`函数来解决，但是很容易忘记包括这一点。*

## 使用 django-pgpubsub 异步发送电子邮件

现在让我们探索一下 *Easy* 是如何使用 django-pgpubsub(pgpubsub)添加这个新特性的，django-pgpubsub 为使用 PostgreSQL 数据库在 django 应用程序之上构建异步和分布式消息处理网络提供了一个框架。这是通过利用 Postgres 的 [LISTEN/NOTIFY](https://www.postgresql.org/docs/current/sql-notify.html) 协议在数据库层构建消息队列来实现的。

由于 *Easy* 已经在 Postgres 上运行 Django，开始使用 pgpubsub(通过`pip install django-pgpubsub`安装后)不需要额外的操作工作。这意味着我们可以直接开始编写业务逻辑(关于这里使用的对象和术语的更详细的解释，请参见[文档](https://github.com/Opus10/django-pgpubsub#documentation-by-example)):

```
# Channels are the medium through which we send notifications
# Define a channel in channels.py
from pgpubsub.channels import TriggerChannel @dataclass
class CommentChannel(TriggerChannel):
    model = Comment
    lockable_notifications = True # A *listener* is the function which processes notifications sent  
# through a channel.
# Define a listener function in listeners.py
@pgpubsub.post_insert_listener(CommentChannel)
def email_user(old: Comment, new: Comment):
    email(comment.post.user)
```

注意现在已经从之前的解决方案中丢弃了我们的`post_save`信号。正如我们指出的，信号很容易被遗漏(例如被`bulk_create`)。相反，我们使用了一个 [Postgres 触发器](https://www.google.com/search?q=postgres+trigger&oq=postgres+trigger&aqs=chrome.0.69i59j35i39j0i512j69i61l2j69i65l2j69i61.2271j0j7&sourceid=chrome&ie=UTF-8):如上定义的监听器利用了 [django-pgtrigger](https://github.com/Opus10/django-pgtrigger) 库来编写一个 Postgres 触发器到我们的数据库，它的工作是[通知](https://www.postgresql.org/docs/current/sql-listen.html)我们的通道，无论何时一个评论被插入到数据库中。对于检测数据库写事件，触发器远比信号更健壮；应用级触发器很容易被遗漏，而触发器总是会被执行。

以上是我们想要的功能所需的所有代码。现在我们需要做的就是启动我们的*监听*进程，它的工作是通过我们的`CommentChannel`通道监听来自我们的触发器的通知，并将它们交给我们的`email_user`监听器函数进行处理。Easy 的开发人员决定将服务器的两个进程用于监听和处理电子邮件。这是通过命令实现的

```
./manage.py listen --processes 2
```

它使用 python 的`multiprocessing`库来启动两个进程，分别用于监听通知和发送电子邮件。

该解决方案的一些亮点:

*   我们可以肯定 django-pgpubsub 的[恰好一次消息传递](https://github.com/Opus10/django-pgpubsub#lockable-notifications-and-exactly-once-messaging)功能将意味着我们可以让两个进程并行监听同一个通道，而不用担心同一封邮件会被发送两次。
*   由于 Postgres `NOTIFY`协议尊重它所调用的事务的原子性，当且仅当注释成功保存在数据库中时，才会发送电子邮件。
*   内置的[恢复](https://github.com/Opus10/django-pgpubsub#recovery)选项允许我们稍后重放任何未能发送的电子邮件。
*   我们没有引入另一个故障点或技术；我们仍然只使用 Django 和 Postgres。
*   如前所述，我们不再担心潜在的信号丢失，因为我们使用的是 Postgres 触发器。

*更多我的作品请看*[*https://github.com/PaulGilmartin/*](https://github.com/PaulGilmartin/graph_wrap)*。*