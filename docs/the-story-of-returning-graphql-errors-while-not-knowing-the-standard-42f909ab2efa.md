# 不知道标准却返回 GraphQL 错误的故事。

> 原文：<https://levelup.gitconnected.com/the-story-of-returning-graphql-errors-while-not-knowing-the-standard-42f909ab2efa>

# **TL；DR 在您的 GraphQL 调用上返回状态代码 200。**

![](img/2f6a63c4f9dfcbc8cf652a3dd77d0acb.png)

照片由[拉米兹·德达科维奇](https://unsplash.com/@ramche?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你像我一样没有使用 AppSync 就在开发一个 GraphQL API，你很可能是在使用 API Gateway + Lambda 来构建你的 GraphQL 端点。

在前端，你可以同时使用 React 和 Amplify。

您已经编写了您的模式，并在后端构建了您的查询/变化；在前端编码一些用户界面。然后，您认为应该处理来自 GraphQL 的错误，这样，如果出现错误，您可以显示一条漂亮的错误消息，避免用户的失望。

您认为自己很聪明，为您的 GraphQL 端点编写了以下代码:

```
export const handler = async event => {
  try {
    const body = JSON.parse(event.body)
    const reply = await executeGraphql(body)
    if (reply.errors) {
      const errors = reply.errors
      throw errors[0]
    }

    return { statusCode: 200, reply }
  } catch (err) {
    return { statusCode: 400, err }
  }
}
```

在你的前端有这样的东西:

```
import { API, graphqlOperation } from 'aws-amplify'

export const deleteAccount = async params => {
  const mutation = `
    mutation deleteAccount ($id: ID!) {
      deleteAccount (id: $id) {
        id
      }
    }
  `

  try {
    const operation = graphqlOperation(mutation, params)
    const response = await API.graphql(operation)
    return { result: response.data.deleteAccount }
  } catch (err) {
    return { error: err.errors[0] }
  }
}
```

一切都准备好了，你决定试着故意出错，这样你就可以处理那些用户经常发现的边缘情况。当您记录错误时，您只会看到:

```
Request failed with status code 400
```

您感到沮丧，因为您的后端工作正常，将错误发送回前端。但是 Amplify 只显示一般的错误，而不是实际的错误。你骂一点，认定库有 bug，叉回购，开一期，做个 PR，等。

终于有人回复你的问题了。你来回走了一会儿，然后你发现你错了，而不是图书馆。您意识到您不知道 HTTP 上的 GraphQL 响应状态代码的标准是什么。

# 是 200。

您将 GraphQL 端点代码更改为:

```
export const handler = async event => {
  try {
    const body = JSON.parse(event.body)
    const reply = await executeGraphql(body)
    return { statusCode: 200, reply }
  } catch (err) {
    return { statusCode: 400, err }
  }
}
```

当在前端记录错误时，您会从 GraphQL 获得实际的错误，而不是一般的错误。

你们🤦‍♂你自己，感谢帮助你的人，结束这个问题。

每个人都很开心🎉

警察。实际的 Github 问题是这里的。

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)