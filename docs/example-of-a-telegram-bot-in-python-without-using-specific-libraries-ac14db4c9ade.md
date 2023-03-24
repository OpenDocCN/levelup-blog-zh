# 不使用特定库的 python 电报机器人示例

> 原文：<https://levelup.gitconnected.com/example-of-a-telegram-bot-in-python-without-using-specific-libraries-ac14db4c9ade>

![](img/1b4170ce898fe6f94969ea87ca61db77.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[Adem may](https://unsplash.com/@ademay?utm_source=medium&utm_medium=referral)拍摄的照片

要制作一个电报机器人，有很多库允许你这样做。
然而，如果你想在不使用特定库的情况下用 python 制作一个机器人，这篇文章正适合你。

使用的库有:

```
import json
import requests
import urllib
```

首先你必须使用机器人父亲([https://t.me/BotFather](https://t.me/BotFather))创建机器人

要检查某人是否联系过机器人:

```
def get_updates(offset=None):
    url = URL + "getUpdates?timeout=100"
    if offset:
        url += "&offset={}".format(offset)
        js = get_json_from_url(url)
    return js
```

要处理向 bot 发出的请求:

```
def echo_all(updates):
    for update in updates["result"]:
    if update.get("message") != None:
        if update.get("message", {}).get("text") != None:
            text = update["message"]["text"]
            chat = update["message"]["chat"]["id"]
            print(text)
            if text == "/test" or text == "/test@" + USERNAME_BOT:
                text = "test response"
                send_message(text, chat)
            elif text == "/start" or text == "/start@" + USERNAME_BOT:
                send_message("/test for test the bot", chat)
```

这是一个示例，它在接收到 **/text** 命令时发送响应“ **test response** ”，并且在 **/start** 命令响应“ **/test 以测试机器人**

这是发送短信的功能:

```
def send_message(text, chat_id):
    tot = urllib.parse.quote_plus(text)
    url = URL + "sendMessage?text={}&chat_id={}".format(tot, chat_id)
    get_url(url)
```

这是发送带有文档的消息的函数(变量“doc”表示文件的路径):

```
def send_document(doc, chat_id):
    files = {'document': open(doc, 'rb')}
    requests.post(URL + "sendDocument?chat_id={}".format(chat_id), files=files)
```

这是发送带有图像的消息的函数(变量“doc”表示文件的路径):

```
def send_image(doc, chat_id):
    files = {'photo': open(doc, 'rb')}
    requests.post(URL + "sendPhoto?chat_id={}".format(chat_id), files=files)
```

在这里你可以找到所有的代码:

如果你有任何问题或者你已经用其他方式解决了它们，不要犹豫，在评论中写下它们！

为了获得无限的故事，你也可以考虑只花 5 美元注册成为媒体会员。如果你使用我的 [*链接*](https://pietrocolombo.medium.com/membership) *注册，我会收到一小笔佣金。*