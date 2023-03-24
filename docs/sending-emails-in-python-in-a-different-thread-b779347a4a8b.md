# Python 中邮件的多线程处理

> 原文：<https://levelup.gitconnected.com/sending-emails-in-python-in-a-different-thread-b779347a4a8b>

## 如何用 Python 发送电子邮件，也是用不同的线程

# 动机

发送电子邮件似乎是一项简单的任务。但是有一些复杂的东西需要考虑:

1.  电子邮件应该并行发送，这样就不会暂停整个项目
2.  一封电子邮件应该是漂亮的，并且支持不同的格式——文本和 HTML。
3.  安全问题。你的邮件服务器需要 SSL 吗？从 Gmail 发送。

![](img/cac05c5df46c6a43dff84d89358f1b91.png)

你有邮件

# 简单的电子邮件发送

在 Python 中，发送电子邮件并不是什么难事。像往常一样，电池已经包括在内，所以我们需要插入它们:

```
import smtplib, ssl
from email.mime.text 
import MIMETextfrom email.mime.multipart 
import MIMEMultipart
```

现在，随着电池的装入，我们只需按下红色按钮:

```
server = smtplib.SMTP(email_smtp_server, email_port))
server.login(email_login, email_password)
server.sendmail(email_from, email_to, message.as_string())
```

不对不对，这些变量都是什么？当然，我们需要为代码提供电子邮件属性、主题和电子邮件文本。

电子邮件属性是简单字符串的 **email_smtp_server** 和整数的 **email_port** 。

其余的全部进入**消息**变量，这是一个更复杂的东西:

```
body = "**<html>**"
body += **"<h2>Guten Tag!</h2><br><br>"** body += **"<b>This is an automatic email.</b><br>"** body += **"Please do not reply.<br><br>"** body += **"Best regards,<br>"** body += **"Your server.<br>"** body += **'<img src="**[**http://realitycheckbbs.org/images/fido.jpg**](http://realitycheckbbs.org/images/fido.jpg)**">'** body **+= "</html>"**
```

我们必须对旧的电子邮件客户端友好，所以让我们在这里创建一个纯文本:

```
text = **"Guten Tag!\n\n"** text += **"This is an automatic email.\n\n"** text += **"Please do not reply.\n\n"** text += **"Best regards,\n"** text += **"Your server."**
```

好了，现在正文准备好了，让我们创建消息本身:

```
message = MIMEMultipart(**"alternative"**)
message[**"Subject"**] = **"Here goes the subject**"
message[**"From"**] = "someemail@server.com"
message[**"To"**] = "spam_victim@hisemail.com"part1 = MIMEText(text, **"plain"**)
part2 = MIMEText(body, **"html"**)
message.attach(part1)
message.attach(part2)
```

所有这些东西现在都可以提供给上面的代码。

如果你只需要发一封邮件，那么我们就完成了。但是对于这个，你也可以使用微软的 Outlook。

# 使用 SSL 发送电子邮件

像 Gmail 一样，发送受 SSL 保护的电子邮件几乎一样简单。

我们只需要为 SSL 创建一个上下文并使用 SMTP_SSL 函数，如下所示:

```
context = ssl.create_default_context()
**with** smtplib.SMTP_SSL(**"smtp.gmail.com"**, port, context=context) **as** server:
    server.login(**"youremail@gmail.com"**, password)
    server.sendmail(
        sender_email, receiver_email, message.as_string()
    )
```

警告！如果你用 Gmail 发邮件，在投入生产之前，试着先发 50 封左右的邮件。Gmail 会停止发送邮件，这不是 bug，这是功能。你必须允许 Gmail 发送像你这样的“不太安全的应用程序”邮件。以下是更多信息:[https://myaccount.google.com/lesssecureapps](https://myaccount.google.com/lesssecureapps)

# 从不同的线程发送电子邮件

如果你从 Django 发送邮件，从另一个线程发送邮件是非常重要的，因为你的用户在他们的浏览器中看不到任何东西，直到所有的邮件被发送。

是的，如果你正在写一个非常大的项目，并且期望每天都有大量的邮件发送，你应该这样使用:[https://www.rabbitmq.com/tutorials/tutorial-one-python.html](https://www.rabbitmq.com/tutorials/tutorial-one-python.html)

但是我们现在讨论的是应用程序内部的一个简单的第二线程。

我们从插入电池开始:

```
**from** threading **import** Thread
```

现在我们可以创建一个类。

```
**class** EmailThread(Thread):
    **def** __init__(self, email_to):
        self.email_to = email_to
        Thread.__init__(self)

    **def** run (self):
        body = **"your html body"** text = **"your plain body"** message = MIMEMultipart(**"alternative"**)
        message[**"Subject"**] = **"subject"** 
        message[**"From"**] = "your@email.com"
        message[**"To"**] = email_to
part1 = MIMEText(text, **"plain"**)
        part2 = MIMEText(body, **"html"**)
        message.attach(part1)
        message.attach(part2)
        server = smtplib.SMTP(smtp_server, email_port)
        server.login(email_login, email_password)
        server.sendmail(your_email, email_to, message.as_string())
```

这里我们只传递一个参数:email_to。当然，它可以扩展到你需要的任何参数:文本体，服务器属性等。

我们现在准备从另一个线程发送电子邮件:

```
EmailThread("spam_victim@gmail.com").start()
```

就是这样。谁说使用线程是复杂的？

请看看我的其他文章:

[](/optimizing-django-queries-28e96ad204de) [## 优化 Django 查询

### 使用批量查询、预加载外键等。

levelup.gitconnected.com](/optimizing-django-queries-28e96ad204de) [](/understanding-authentication-fa0f0893c302) [## 了解身份验证

### 使用 Python 的散列函数简介

levelup.gitconnected.com](/understanding-authentication-fa0f0893c302) [](/a-chatbot-without-machine-learning-b76ad00e30c1) [## 没有机器学习的聊天机器人

### 如何不用机器学习和 NLP 写一个聊天机器人

levelup.gitconnected.com](/a-chatbot-without-machine-learning-b76ad00e30c1) [](/writing-tetris-in-python-2a16bddb5318) [## 用 Python 写俄罗斯方块

### 用 Python 和 PyGame 编写俄罗斯方块的分步指南

levelup.gitconnected.com](/writing-tetris-in-python-2a16bddb5318)