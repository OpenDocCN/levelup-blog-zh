# 可靠软件架构中的“S”——单一责任原则

> 原文：<https://levelup.gitconnected.com/the-s-in-solid-5a6e0d778cbc>

## s 代表“单一责任”，意思是只做一件事，但要做好。

在软件开发中，用基本块构建复杂结构的概念无处不在，无论是以类、组件还是模块的形式。

根据坚实的原则，这些基本块中的每一个都应该只有一个作业，并将额外的作业委托给其他模块。

![](img/59cc6dc26e12e41912a7f7d57302bec8.png)

如果你搜索“焦点”，Unsplash 上会显示什么(作者[基兰·奥尔德沃斯](https://unsplash.com/@kyran12?utm_source=medium&utm_medium=referral))。

例如，假设有人想要对应用程序中抛出的事件做出反应。应该将事件保存到数据库中，并向用户发送通知电子邮件。这可能看起来像下面这样(以打字稿编写，但易于阅读——这是关于原则，而不是语言):

```
onAccountCreated(event: Event) { 
  // Persist event in database.
  database.table("events").create({
    name: event.getName(),
    data: event.getPayload(),
    createdAt: time.getISO(event.getTimestamp())
  })
  // Send new account email notification.
  http.post(
    "api.my-email-service.com/send-email",
    {
      to: "me@example.com",
      subject: "New event",
      body: `The event ${event.getName()} has occured!`
    },
    {
      Authorization: config.environment.get("EMAIL_API_KEY")
    }
  )
}
```

那么，这有什么问题呢？有三个:

**1。可读性**

代码被阅读的次数比它被编写的次数多得多，尤其是当项目变得越来越大的时候。罗伯特·c·马丁估计这一比例为 10:1。这意味着从开发和商业的角度来看，优化阅读时间是有意义的。

在上面的例子中，多级缩进、方法的长度以及注释使得代码难以浏览。

**2。可测试性**

在每个严肃的软件项目中，测试的存在通常是假定的。特别是当单元测试模拟其他服务的能力对于确保我们只测试一个特定的模块很重要的时候。

在这个例子中，我们需要模拟四个不同的模块:`database`、`time`、`http`和`config`。每一次模仿都会带来更多的复杂性——意味着更多的出错空间和更难阅读和理解的代码。

**3。关注点分离**

具有相似职责的软件部分应该被分组，而具有不同职责(关注点)的部分应该被分离。或者，正如鲍勃大叔所说:

> “收集因相同原因而改变的事物。把那些因为不同原因而改变的东西分开。"

这是基于这样一个事实，即软件最终是为人服务的。正因为如此，它不仅应该围绕一个流程的特定流程图来构建，还应该围绕它所服务的人来构建。如果设计者请求改变应用程序的布局，那么不需要改变将数据从这个布局保存到数据库的代码。关注点分离。

在这个例子中，代码更改有多种原因，每个原因都有不同的动机。后端开发人员可以对数据库模式进行更改，这需要 DB 调用进行调整。设计团队可以对电子邮件样式进行更改，要求电子邮件服务请求进行调整。运营团队可能会改变部署，要求配置进行调整。所有这些需要修改同一段代码的动机都是令人头疼的问题。

那么什么是更合适的解决方案呢？让我来建议一下:

```
onAccountCreated(event: Event) {
  eventRepository.persist(event)
  notifications.broadcast(event)
}
```

存储库可以完全负责如何保存事件:

```
class EventRepository {
  persist(event: Event) {
    database.table("events").create({
      name: event.getName(),
      data: event.getPayload(), 
      createdAt: time.getISO(event.getTimestamp())
    })
  }
}
```

通知模块可以利用邮件程序通知用户:

```
class Notifications {
  …
  broadcast(event: Event) {
    this.mailer.send(
      "me@example.com",
      "New event",
      `The event ${event.getName()} has occured!`
    );
  }
}

class Mailer {
  …
  send(to: string, subject: string, body: string) {
    this.http.post(
      "api.my-email-service.com/send-email",
      { to, subject, body },
      {
        Authorization: config.environment.get("EMAIL_API_KEY")
      }
    )
  }
}
```

即使这会产生更多的代码行，但它的整体质量会得到提高:

*   人们可以很快理解`onAccountCreated`方法在做什么(不需要注释)。
*   人们只能调查与自己的问题相关的代码。
*   通过减少每个模块的依赖性来提高可测试性。
*   出于相同原因而更改的代码被分组——只有当数据库模式更改时,`EventRepository`才会更改，只有当设计团队想要对电子邮件正文进行调整时,`Notifications`模块才会更改，只有当运营团队决定使用不同的配置设置时,`Mailer`才会更改。

到目前为止，以这种方式构建代码的好处和必要性应该是显而易见的。

然而，与大多数事情一样，我们不应该盲目地应用单一责任原则所提倡的方法。仅仅将你所有的代码分成自己的类，直到每个类都只有一行方法，将会导致过度工程化的代码，这是低效的。

人们应该理解这个原则是关于[平衡除法和聚合](https://hackernoon.com/you-dont-understand-the-single-responsibility-principle-abfdd005b137)——基于代码的某些部分改变的原因。

有了这种理解，在日常开发中自信地应用单一责任原则应该会容易得多。如果你觉得这篇文章有帮助或者有任何反馈，请告诉我——我非常感激！

罗伯特·c·马丁,《干净的代码:敏捷软件工艺手册》

在我的博客上注册[，就可以收到关于新帖子的电子邮件通知。](http://blog.richartkeil.com)