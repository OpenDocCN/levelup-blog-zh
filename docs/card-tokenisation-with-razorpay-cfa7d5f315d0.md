# 使用 Razorpay 进行卡令牌化

> 原文：<https://levelup.gitconnected.com/card-tokenisation-with-razorpay-cfa7d5f315d0>

## Razorpay TokenHQ，印度首个多网卡令牌化解决方案

## 了解如何在遵守新的 RBI 准则的同时实施储值卡功能

![](img/6a36e4d587ebd3942a2de83da9e07bb3.png)

[皮卡伍德](https://unsplash.com/@pickawood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 介绍

有了 Razorpay standard checkout，开发者可以在几秒钟内支持跨平台的多种支付模式。使用这种方法，开发者和公司不再关心货币兑换、支付验证、支持新的支付模式等。要了解有关标准结帐的更多信息，请阅读以下文章:

[](https://betterprogramming.pub/how-to-add-razorpay-payments-in-android-apps-7bd908666205) [## 如何在 Android 应用中添加 Razorpay 支付

### 探索 Android 中的 Razorpay 标准结账

better 编程. pub](https://betterprogramming.pub/how-to-add-razorpay-payments-in-android-apps-7bd908666205) 

但在这个现代时代，客户不仅关心首选的支付模式，他们还想要更多，比如每次付款时不要重新输入卡的所有细节，或者记住 UPI ID。另一方面，通过减少支付流程中的摩擦，公司的收入也大幅增加。

直到几个月前，与敏感数据(如卡的详细信息、UPI ids 等)相关的支付只能存储在安全且经过批准的支付网关服务器中。但是随着印度储备银行的[新指导方针的出台，事情发生了变化。](https://www.livemint.com/money/personal-finance/new-rbi-rules-for-online-payments-from-1-january-what-it-means-for-debit-credit-card-holders-11640238436540.html)

> RBI 卡令牌化指南禁止企业、支付集成商、支付网关和收单银行保存客户卡的详细信息。卡网络和发卡机构是现在唯一可以保存卡数据的机构。这将于 2022 年 1 月 1 日生效。

# 什么是卡令牌化？

新卡令牌化只不过是将持卡人的 16 位卡号和 PAN 号与随机唯一的 16 位字符串相结合的过程，只不过是一个令牌。

这些令牌可以与非敏感数据(如卡的最后 4 位数字和到期信息)一起保存在安全的支付网关服务器(如 Razorpay)中。稍后，商家可以向客户显示非敏感信息，当他们选择用其中一个支付时，Razorpay 将使用令牌进行支付。

# 卡令牌化的好处是什么？

遗留的好处之一是，如果您选择将卡保存在商家处，则不必多次重新输入任何卡的详细信息。但关键的好处是通过减少存储敏感数据的位置来保护敏感数据，从长远来看，这可以大大减少数据泄露的机会。

# 通过 Razorpay 进行卡令牌化

大多数企业选择退出 Razorpay 提供的标准结账流程，如果你是其中之一，那么你很幸运。默认情况下，Razorpay 处理 RBI 指南，因此零变化，商家遵守 RBI 新指南。

然而，如果你是像我一样渴望设计定制支付流程的商人，那么需要做一点工作。坦率地说，大部分过程保持不变，只是在最后做了一些调整。

不再拖延，让我们从编码部分开始。

# Razorpay **授权**

在接下来的章节中，我们将主要讨论 Razorpay APIs。所以大多数 Razorpay APIs 都遵循一个基本的 Auth 实现，其中需要一个授权头。

我们需要用 base64 编码对`Razorpay_key:Razorpay_secret_key`字符串进行编码，并在带有前缀`Basic`的 API 调用头中传递它。我们将在文章中讨论的所有 API 都需要遵循这一点。

```
**header.put(“Authorization”, “Basic $base64_encoded_string”)**
```

# 在网关服务器中创建一个客户

为了保存详细信息，我们首先需要在支付网关服务器中创建客户，在本例中是 Razorpay。为了创建客户，Razorpay 提供了一个 API，您可以在其中传递用户的详细信息，如姓名、电子邮件、电话号码、自定义注释等。在响应中，Razorpay 会给出客户 id。

```
**POST** [https://api.razorpay.com/v1/customers](https://api.razorpay.com/v1/customers)**Request body:** {
   "name":"John Dao",
   "contact":"1234567890",
   "email":"John.dao@example.com",
   "fail_existing":"0",
   "gstin":"29XA09A436910PC",
   "notes":{
     "notes_key_1":"Sample notes 1",
     "notes_key_2":"Sample notes 2"
   }
}**Response:** {
  "id" : "cust_1Aa00000001002",
  "entity": "customer",
  "name":"John Dao",
  "contact":"1234567890",
  "email":"John.dao@example.com",
  "fail_existing":"0",
  "gstin":"29XA09A436910PC",
  "notes":{
     "notes_key_1":"Sample notes 1",
     "notes_key_2":"Sample notes 2"
   },
  "created_at ": 1234567590
}
```

Create customer 是 Razorpay 的一个简单的 POST API 调用，它将客户详细信息作为一个 JSON 对象，在响应中，它将返回所有保存的客户详细信息以及客户 id。

在进入下一步之前，我想讨论一下我们在请求体中发送的`fail_existing`键-值对。这是一个可选字段，有两个可能的值:`0`和`1`。

*   `0`:如果已经有相同明细的客户，则取已有客户的明细。
*   `1`(默认):如果已经存在具有相同详细信息的客户，则抛出一个错误。

# 结账时保存卡

我是一名 Android 开发人员，所以我选择通过 android SDK 实现存储卡功能，但坦率地说，这对于其他平台也是一样的。因此，一旦用户输入了卡的详细信息——卡号、到期月份和年份、卡上的姓名和 CVV，我们需要通过 Razorpay 客户端继续支付。

在此之前，如果您想通过保存卡的详细信息来为客户提供更快的结账，您需要获得客户的同意来保存卡。Razorpay 为此提供了两种方式:要么商家同意，要么让 Razorpay 在结账时处理。开发者可以在 Razorpay 仪表盘中进行配置。

一旦您同意保存卡，我们需要向 Razorpay 客户端发送以下有效负载来处理支付。有效负载包括卡详细信息、订单详细信息、客户 id 和保存标志。看一看:

大多数与订单和卡详细信息相关的字段都是不言自明的。但是有一个不同寻常的字段是`save`，它有两个可能的值:`0`和`1`。

*   `1` : Razorpay 应将客户卡的详细信息保存为卡网络的令牌。只有在从客户处获得明确的客户同意后，这才会起作用。
*   `0` : Razorpay 不应该保存卡的详细信息。

在 sendRequest 函数中，我们将验证和字段，并使用 Razorpay 客户端处理支付。看一看:

开发人员可以处理成功和失败状态，类似于我在本文的[中解释的标准结帐流程。](https://betterprogramming.pub/how-to-add-razorpay-payments-in-android-apps-7bd908666205)

如果支付成功并且保存值为`1`，那么 Razorpay 将获得与该卡相关的令牌，并将其与一些基本的非敏感细节一起保存在他们的服务器中。

# 获取客户的所有令牌

现在我们保存了一张卡，下一次当用户进入支付流程时，我们需要向客户显示所有保存的卡，以便他可以选择其中一张卡进行快速结账。

为此，Razorpay 提供了一个 GET API 来获取与客户相关的所有令牌。我们只需要客户 id。看一看:

```
**GET https://api.razorpay.com/v1/customers/:customer_id/tokens**
```

**回应:**

获取令牌 API 响应

现在，您可以实现自定义 UI，向客户显示保存的卡详细信息，以便更快、更安全地结账。请记住，卡的 CVV 现在将存储在任何地方。

# 使用令牌结账

既然我们已经向用户显示了保存的卡的详细信息，那么用户很可能会选择使用其中一个卡来结账，如果他们这样做了，我们就必须使用与卡相关联的令牌来结账。

我们必须传递令牌，而不是卡的详细信息，如号码和到期信息。现在，当请求到达发卡方时，它将获取映射到此令牌的卡详细信息来处理支付。 ***请记住，用户需要手动输入 CVV，我们可以使用卡详情或代币进行支付。***

[](https://betterprogramming.pub/how-to-integrate-google-pay-into-your-existing-android-app-d75b269cd623) [## 如何将 Google Pay 集成到您现有的 Android 应用程序中

### 开始通过 GPay 接受付款

better 编程. pub](https://betterprogramming.pub/how-to-integrate-google-pay-into-your-existing-android-app-d75b269cd623) 

目前就这些。希望你学到了有用的东西。感谢阅读。