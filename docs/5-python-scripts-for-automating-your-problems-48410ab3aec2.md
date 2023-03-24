# 自动化问题的 5 个 Python 脚本

> 原文：<https://levelup.gitconnected.com/5-python-scripts-for-automating-your-problems-48410ab3aec2>

## 为您的日常问题收集黑仔现成的脚本

![](img/2310ab16b73de8526cb900022d490acd.png)

**由** [**设计**](https://www.freepik.com/wayhomestudio) **上** [**Freepik**](https://www.freepik.com/)

让你的事情自动化是节省时间的好方法。这篇文章将向您展示自动化解决您每天面临的问题的 5 个 Python 脚本。所以把这个放在单子上，让我们开始吧。

> 自动化和技术并不能治愈行为上的俗套:它们只是创造了新的实例。
> 
> —肯尼斯·戈德史密斯

# 👉将图像转换为 PDF

这个自动化脚本将帮助您将大量图像转换为 PDF 格式。该脚本使用 **PyMuPDF 模块**进行转换。当你有一堆图像或者需要在一个项目中使用它时，这是一个方便的脚本。

```
# Images to PDF 
# pip install fpdffrom tkinter import Image
from fpdf import FPDFdef Img2PDF(images):
    convert = FPDF()
    for img in images:
        convert.add_page()
        convert.image(img, 0, 0, 210, 297)
    convert.output("output.pdf", "F")Img2PDF(["img1.png", "img2.png"])
```

# 👉自动化 Whatsapp 信息

如果你想让你的 WhatsApp msg 自动化，或者需要一个 WhatsApp 的营销机器人，这是给你的脚本。这使用了 WhatsApp-py 模块，允许您发送 WhatsApp 消息、获取联系人列表、群发消息等等。

```
# Automate Whatsapp Messages
# pip install whatsappy-pyfrom time import clock_settime
from whatsappy import Whatsapppywhat = Whatsapp()
pywhat.login(visible=True)# Select Chat by name
msg = pywhat.chat("John")
msg.send("Hy From Medium")# Get All Contact list
contact = pywhat.get_contact_list()
print(contact)# Creating new group 
group = pywhat.new_group(name="Team", contacts=["John", "Jane"])# Send Group msg
group.send("Hi Team")
```

# 👉解码二维码

这个自动化脚本将帮助您解码您的二维码图像。该脚本使用 **Qrtool 模块**扫描您的 Qr 码图像，解码并获取其数据。

```
# Decode Qr Code
# pip install pyqrcodeimport qrtoolsdef Decode_Qr(qr_img):
    decoder  = qrtools.QR()
    decoder.decode(qr_img)
    print(decoder.data)Decode_Qr('qr_code.png')
```

# 👉自动发送电子邮件

这个 Python 自动化脚本将帮助你发送电子邮件。该脚本使用可以连接到您的 Gmail、Outlook T21 或任何电子邮件服务器的 Smtplib，让以编程方式向您发送电子邮件。

```
# Automate Email Sendingfrom email.message import EmailMessage
import smtplib
import osmail_addr = "[myemail@mail.com](mailto:myemail@mail.com)"
mail_pass = "mypassword"Email = EmailMessage()
Email['Subject'] = "Test Email"
Email['From'] = mail_addr
Email['To'] = "[reciever@mail.com](mailto:reciever@mail.com)"
Email.set_content("Hy from Medium")with smtplib.SMTP_SSL('smtp.gmail.com', 465) as server:
    server.login(mail_addr, mail_pass)
    server.send_message(Email)
```

# 👉自动化 PDF 工作

需要用 Python 自动完成 PDF 任务。这个自动化使用了 **PyPDF4 模块**，它可以帮助你解析你的 PDF 文件。检查下面的脚本。

```
# pip install PyPDF4import PyPDF4 as PDF# Fetch Text
pdf = PDF.PdfFileReader(open('test.pdf', 'rb'))for i in range(pdf.numPages):
    page = pdf.getPage(i)
    print(page.extractText())
```

# 最后的想法

嗯，自动化是一件很酷的事情，我希望你喜欢这篇文章。好吧，我很想知道你的反应，如果你喜欢这篇文章，然后与你的朋友分享❤️。

成为灵媒的一员，你可以支持我和其他人，你将获得无限的故事👇

[](https://codedev101.medium.com/membership) [## 通过我的推荐链接加入 Medium—hai der Imtiaz

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

codedev101.medium.com](https://codedev101.medium.com/membership)