# 要求和规格

> 原文：<https://levelup.gitconnected.com/gorestful-1st-iteration-requirements-and-api-specification-f2b5e40e9571>

![](img/35e060f4ef51c87f10849f0760cbb61b.png)

照片由[伯纳德·塔克](https://unsplash.com/@jodrell?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## 去休息吧

## 走向 RESTful 系列—迭代#1

在这篇文章中，我列出了我们的 Go RESTful 项目第一次迭代的所有需求。这样可以让我们知道要做什么，要实现什么，然后适当的设计和实现。我也在为这次迭代起草 API 规范。

这将像一个“迷你”规划会议，在这里我从业务所有者(在这种情况下是我自己)那里接收业务需求，进行分析，并编写技术规范。业务需求集实际上很小，因此可以认为它在合理的时间内(2 到 4 周)是不变的和可实现的。

![](img/7a4110ff727e1493b005179be9e8aa20.png)

# 要求

## 实体

**用户**

*   `username` :
    -独一无二。
    -仅允许数字、下划线、破折号、点和字母。
    -以下划线或字母开头。
    -最少 1 个字符，最多 32 个字符。
*   `email` :
    -是有效的电子邮件。
    -独一无二。
*   `fullName` :
    -长度至少为 1 个字符，最多为 128 个字符。
*   `bio` :
    -可选。
    -最多 256 个字符。

## 用例

*   列出所有用户。
*   通过用户名检索用户的信息。
*   更新用户。每个字段都允许更改。
*   删除用户。

# 规格

## 模型

```
User {
    "username": "string",
    "email": "string",
    "fullName": "string",
    "bio": "string" | null
}Error {
    "message": "string"
}
```

## 蜜蜂

*   列出用户:

```
GET /users/
# Response: User[]
```

*   获取用户:

```
GET /users/@username
# Response: User
```

*   创建用户:

```
POST /users/
# Body: User# Response: User
```

*   更新用户:

```
PUT /users/@username
# Body: User# Response: User
```

*   删除用户:

```
DELETE /users/@username
# Response: Empty or Error
```

就是这样！下一篇关于建筑设计的文章再见。