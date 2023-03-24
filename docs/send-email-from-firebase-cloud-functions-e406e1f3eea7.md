# 从 Firebase 云功能发送电子邮件

> 原文：<https://levelup.gitconnected.com/send-email-from-firebase-cloud-functions-e406e1f3eea7>

## 如何从 Firebase 云功能发送包含 HTML 内容的电子邮件

![](img/39e74394969123cd610ce7e1d8f73f51.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

正如你可能想象的那样，在 [DeckDeckGo](https://deckdeckgo.com) ，我们没有任何合作者来检查公开发布的幻灯片是否有下降内容。我们也还没有实现一个能做到这一点的机器学习机器人。

我正在手动处理这样的任务。我得补充一点，这样做让我很开心。到目前为止发表的所有演讲都很有趣。

尽管如此，我必须被告知何时甲板出版。这就是为什么我实现了一个 [Firebase Cloud 函数](https://firebase.google.com/docs/functions)来给我自己发送一封电子邮件，其中包含我快速查看新内容所需的所有信息。

# 设置新的云功能

我假设你已经有一个 Firebase 项目，也已经创建了一些功能。如果没有，可以按照下面的[指南](https://firebase.google.com/docs/functions/get-started)开始。

此外，注意我使用的是[类型脚本](https://www.typescriptlang.org/)。

# 我们开始吧

一个函数需要一个触发器，这就是为什么我们在一个名为`demo`的集合上的`index.ts`中注册一个函数(当然你的集合可以有不同的名字)。

```
import * as functions from 'firebase-functions';export const watchCreate=
       functions.
       firestore.
       document('demo/{demoId}').onCreate(onCreateSendEmail);
```

我们可以使用任何其他触发器或生命周期，不一定是`create`那个。

为了响应触发器的执行，我们声明了一个新的函数来检索新创建的值(`const demo = snap.data()`)，并且现在添加了一个`TODO`，它应该被一个有效的发送电子邮件的方法所取代。

```
import { EventContext } from "firebase-functions";
import { DocumentSnapshot } from "firebase-functions/lib/providers/firestore";interface Demo {
  content: string;
}

async function onCreateSendEmail(
                 snap: DocumentSnapshot, 
                 _context: EventContext) {
  const demo: Demo = snap.data() as Demo;

  try {
    // TODO: send email
  } catch (err) {
    console.error(err);
  }
}
```

# 节点邮件程序

为了有效地发送邮件，我们将使用 [Nodemailer](https://nodemailer.com/) 。

> Nodemailer 是 Node.js 应用程序的一个模块，可以轻松发送电子邮件。该项目始于 2010 年，当时没有发送电子邮件的合理选项，今天它是大多数 Node.js 用户默认采用的解决方案。
> 
> Nodemailer 在 MIT 许可证下获得许可。

正如你所注意到的，Nodemailer 不仅与 Firebase Cloud 函数兼容，还与任何 [Node.js](https://nodejs.org/) 项目兼容。

为了在我们的项目中安装它，我们运行以下命令:

```
npm install nodemailer --save
```

此外，我们还安装了它的类型定义。

```
npm install @types/nodemailer --save-dev
```

## SMTP 传输

Nodemailer 使用 SMTP 作为传递邮件的主要传输方式。因此，您的电子邮件服务提供商应该支持这样的协议。它还支持 LTS 或 STARTTLS 扩展。在这篇文章中，我们将使用 STARTTLS，因此我们将设置标志`secure`为`false`来激活这个协议。

您可以在库[文档](https://nodemailer.com/smtp/)中找到所有选项。

# 配置

特别是如果您的项目是开源的，您可能会有兴趣不在代码中硬编码您的 SMTP 登录、密码和主机，而是将它们隐藏在配置中。

Firebase 提供这样的能力。我们可以创建一个脚本来`set`这些。

```
#!/bin/sh
firebase functions:config:set mail.from="hello@domain.com" mail.pwd="password" mail.to="david@domain.com" mail.host="mail.provider.com"
```

为了在我们的函数中检索这些配置，我们可以通过`functions.config()`访问配置，后跟我们刚刚在上面定义的键。

```
const mailFrom: string = functions.config().mail.from;
const mailPwd: string = functions.config().mail.pwd;
const mailTo: string = functions.config().mail.to;
const mailHost: string = functions.config().mail.host;
```

# 发送电子邮件

我们有了传输，我们有了配置，我们只需要最后一块:消息。

我更喜欢发送我自己的 HTML 电子邮件，允许我在内容中包含链接，这就是为什么在这里，我们也使用这样的格式。

```
const mailOptions = {
  from: mailFrom,
  to: mailTo,
  subject: 'Hello World',
  html: `<p>${demo.content}</p>`
};
```

最后，我们可以使用 Nodemailer 来创建通道，并最终发送我们的电子邮件。

```
const transporter: Mail = nodemailer.createTransport({
  host: mailHost,
  port: 587,
  secure: false, // STARTTLS
  auth: {
    type: 'LOGIN',
    user: mailFrom,
    pass: mailPwd
  }
});await transporter.sendMail(mailOptions);
```

# 总共

总而言之，我们的功能如下:

```
import * as functions from 'firebase-functions';

import { EventContext } from "firebase-functions";
import { DocumentSnapshot } from "firebase-functions/lib/providers/firestore";

import * as Mail from "nodemailer/lib/mailer";
import * as nodemailer from "nodemailer";

export const watchCreate=
       functions.
       firestore.
       document('demo/{demoId}').onCreate(onCreateSendEmail);

interface Demo {
  content: string;
}

async function onCreateSendEmail(
                 snap: DocumentSnapshot, 
                 _context: EventContext) {
  const demo: Demo = snap.data() as Demo;

  try {
    const mailFrom: string = functions.config().info.mail.from;
    const mailPwd: string = functions.config().info.mail.pwd;
    const mailTo: string = functions.config().info.mail.to;
    const mailHost: string = functions.config().info.mail.host;

    const mailOptions = {
      from: mailFrom,
      to: mailTo,
      subject: 'Hello World',
      html: `<p>${demo.content}</p>`
    };

    const transporter: Mail = nodemailer.createTransport({
      host: mailHost,
      port: 587,
      secure: false, // STARTTLS
      auth: {
        type: 'LOGIN',
        user: mailFrom,
        pass: mailPwd
      }
    });

    await transporter.sendMail(mailOptions);
  } catch (err) {
    console.error(err);
  }
}
```

# 摘要

在 Firebase 和 Nodemailer 的帮助下，可以相对快速地设置触发电子邮件的功能。我希望这篇介绍能给你一些关于如何实现这样一个特性的提示，并且希望你能在下一次演讲中尝试使用 [DeckDeckGo](https://deckdeckgo.com) 。

我期待着收到一封电子邮件，告诉我我必须检查您发布的幻灯片😉。

到无限和更远的地方！

大卫