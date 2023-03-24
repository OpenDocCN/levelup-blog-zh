# 使用 Python 发送批量电子邮件

> 原文：<https://levelup.gitconnected.com/sending-bulk-emails-via-python-4592b7ee57a5>

## 发送电子邮件的自动化方式

![](img/2bf3a17d00064421960f06aa74f5c9ba.png)

手动发送电子邮件是一项乏味、耗时且容易出错的任务。我们可以使用 Python 来自动化这个过程。

# **入门**

Python 附带了各种内置模块。我们可以用来发送邮件的是 [*smptlib*](https://docs.python.org/3/library/smtplib.html) ，，它使用简单邮件传输协议( [SMTP](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) )。我们编写的代码将使用 Gmail SMTP 服务器通过端口 **587** 或 **465** 发送电子邮件。

# **发送纯文本电子邮件**

我们将从导入发送电子邮件所需的所有助手模块开始。

我们将使用 [*email.mime*](https://docs.python.org/2/library/email.mime.html) 来构建我们的电子邮件消息。

我们需要指定电子邮件帐户及其凭证，它们将用于发送电子邮件。

现在，核心部分将包括接收邮件列表和我们邮件的各种标题，如*主题*、*正文*。

在这种情况下，我们将发送一条简单的文本消息。

```
# list of users to whom email is to be sent
email_send = ['LIST_OF_RECIPIENTS']subject = 'EMAIL_SUBJECT'msg = MIMEMultipart()
msg['From'] = email_user# converting list of recipients into comma separated string
msg['To'] = ", ".join(email_send)msg['Subject'] = subjectbody = 'EMAIL_BODY'
msg.attach(MIMEText(body,'plain'))text = msg.as_string()
server = smtplib.SMTP('smtp.gmail.com',587)
server.starttls()
server.login(email_user,email_password)
server.sendmail(email_user,email_send,text)
server.quit()
```

上述代码可以与任何前端屏幕集成，以动态接收`LIST_OF_RECIPIENTS`来一次发送批量邮件。

# 发送包含 HTML 内容的电子邮件

当发送带有 HTML 内容的电子邮件时，没有太大的变化。我们只需要稍微调整一下我们的邮件正文，它将包含 HTML 格式的文本。

所以我们将我们的正文文本改为包含 HTML 的文本，包含我们正文的 MIMEText 将把它称为`html`而不是`plain`。

您还可以使用上述方法发送包含附件的电子邮件。

# 更多信息/文档

*   [Python 文档](https://docs.python.org/3/library/email.examples.html)
*   [真正的 Python 文章](https://realpython.com/python-send-email/)