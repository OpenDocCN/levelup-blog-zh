# 如何使用 SendInBlue 从 Node.js 发送电子邮件

> 原文：<https://levelup.gitconnected.com/how-to-send-emails-from-node-js-with-sendinblue-c4caacb68f31>

![](img/4837464864a362cfe1e91e40f190b2fe.png)

在本文中，我们将学习如何使用 SendInBlue 从 Node.js 发送电子邮件。

# 视频教程

# 什么是 SendInBlue？

SendInBlue 是一个邮件服务，允许您从 Node.js 应用程序发送电子邮件。

# 获取 Sendinblue 电子邮件 API 密钥

1.  转到[发送蓝色](https://www.sendinblue.com/)并创建一个账户。
2.  转到仪表板，单击右上角的下拉菜单。
3.  点击 **SMTP & API** 选项卡。
4.  点击`Create new API key`按钮。
5.  现在我们需要将 api 键存储在一个环境变量中。

# 设置

*   安装包:

```
npm init -y
npm i dotenv sib-api-v3-sdk
```

*   创建一个名为`.env`的文件，并添加以下几行:

```
API_KEY=<your_api_key>
```

*   创建一个名为`index.js`的文件，并添加以下几行:

```
const Sib = require('sib-api-v3-sdk')require('dotenv').config()const client = Sib.ApiClient.instanceconst apiKey = client.authentications['api-key']
apiKey.apiKey = process.env.API_KEY
```

说明:`require('dotenv').config()`:用于从`.env`文件中加载环境变量。然后，我们必须将 api 密钥添加到 Sendinblue 客户端。

```
const tranEmailApi = new Sib.TransactionalEmailsApi()const sender = {
    email: 'thatanjan@gmail.com',
    name: 'Anjan',
}const receivers = [
    {
        email: '<email address>',
    },
]
```

说明:使用`tranEmailApi`我们可以发送电子邮件。发件人电子邮件必须是您在 SendinBlue 帐户中使用的电子邮件帐户。

```
tranEmailApi
    .sendTransacEmail({
        sender,
        to: receivers,
        subject: 'Subscribe to Cules Coding to become a developer',
        textContent: `
        Cules Coding will teach you how to become {{params.role}} a developer.
        `,
        htmlContent: `
        <h1>Cules Coding</h1>
        <a href="https://cules-coding.vercel.app/">Visit</a>
                `,
        params: {
            role: 'Frontend',
        },
    })
    .then(console.log)
    .catch(console.log)
```

解释:

*   您可以使用`sendTransacEmail`方法发送电子邮件。
*   主题是必需的。
*   您必须将`textContent`或`htmlContent`传递给该方法。`htmlContent`将覆盖`textContent`。
*   您可以使用`params`
    对象向电子邮件内容传递参数。
*   运行该文件，您将看到电子邮件已发送。

```
node index.js
```

Sendinblue 有您可以使用的模板。如果你想让我教你如何创建简讯，请告诉我。今天到此为止。祝你愉快。