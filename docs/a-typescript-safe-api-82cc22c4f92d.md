# 类型脚本安全 API 请求

> 原文：<https://levelup.gitconnected.com/a-typescript-safe-api-82cc22c4f92d>

## 如何将 TypeScript 类型添加到客户端发出的 API 请求中，从而允许类型安全扩展到整个 web 应用程序。

![](img/839766553c1fa1544309f40af87c8cda.png)

通过将 TypeScript 引入到您的项目中，您可以获得许多立竿见影的效果。当它们开始累积时，您的工程团队获得的信心水平本身就是值得的。

提高应用程序的类型安全性的最快方法之一是使 API 调用类型安全。通过这样做，您将设置编排这些调用的代码是类型安全的，很快类型安全就会渗透到代码库的其余部分。

# 类型安全轴

这里展示的例子是特定于 [axios](https://github.com/axios/axios) 的，但是可以推广到任何其他具有 TypeScript 绑定的 http 客户端。

虽然 axios 目前是用普通 JS 编写的，但是 TS 定义是直接存储在 repo 中的[。发布的 axios npm 包包含了这些定义，因此无论您在什么地方使用 axios，都可以在`.ts`文件中获得 TS 支持。](https://github.com/axios/axios/blob/v0.19.0/index.d.ts)

## 创建客户端

现在来定义你的第一个类型安全的 API 函数。您将设置 axios 客户端。

```
const apiClient = axios.create({
  baseURL: 'https://yoursite.com/api',
  responseType: 'json',
  headers: {
    'Content-Type': 'application/json'
  }
});
```

## 创建一个 API 函数

然后为使用`apiClient`的单个 API 调用创建一个方法。

```
const createUser = async (newUser: NewUser) => {
  try {
    const response = await apiClient.post<User>('/users', newUser);
    const user = response.data;
    return user;
  } catch (err) {
    if (err && err.response) {
      const axiosError = err as AxiosError<ServerError>
      return axiosError.response.data;
    }

    throw err;
  }
};
```

让我们剖析一下`createUser`函数中发生了什么。首先，我们已经决定不直接将`axiosClient`暴露给我们代码库中的任何模块。它的所有用法都将封装在为我们的应用程序进行的每个远程调用编写的函数中。这允许我们在这个位置只关心 Axios 特定的类型，而应用程序的其余部分将使用最终从这些函数返回的域特定的类型。它还具有在一个位置定义所有远程调用的额外好处。

## 锁定请求参数

```
const createUser = async (newUser: NewUser) => {
```

我们的函数接受一个类型为`NewUser`的`newUser`参数。这为我们的 API 调用主体将采用的参数提供了类型安全。它最有可能定义一个带有几个字段的对象(例如`type NewUser = { firstName: string, lastName: string }`)。

## 使用泛型来键入响应正文

```
const response = await apiClient.post<User>('/users', newUser);
```

这是我们与 Axios 类型的第一次交互。Axios 通过利用[泛型](https://www.typescriptlang.org/docs/handbook/generics.html)使得它的`post`方法可重用且类型友好。我们作为`<User>`提供的通用参数是[什么 Axios 将使用](https://github.com/axios/axios/blob/v0.19.0/index.d.ts#L136)来键入成功的响应体。

## 受益于隐式类型

```
const user = response.data;
return user;
```

因为我们使用泛型向 Axios 提供响应主体类型，所以下一行静态地保证将`const user`识别为类型`User`。这很好，因为现在任何调用`createUser`函数的代码都知道返回类型必须是`User`。

## 处理错误响应

```
} catch (err) {
  if (err && err.response) {
    ...
  }

  throw err;
}
```

好的，那么 catch 块呢？响应成功的快乐路径在 try 块中被捕获。当响应失败时，我们希望确保正确输入了路径。

Axios 将任何非 200 级别的响应视为异常，这就是我们使用 try/catch 的原因。它[抛出的数据类型为](https://github.com/axios/axios/blob/v0.19.0/index.d.ts#L79) `AxiosError<TResponseBody>`。一些基本的验证是通过检查它确实是一个`AxiosError`，而不是通过验证`err.response`存在而抛出的其他 JS 错误来完成的。如果它不是一个`AxiosError`，那么将再次抛出错误，并需要由其他应用程序代码捕获。

```
const axiosError = err as AxiosError<ServerError>
return axiosError.response.data;
```

至于当确定我们正在处理非 200 级响应时会发生什么，我们可以也应该在正常的申请流程下处理。为此，我们将 catch 块中的`err`转换为所提供的`AxiosError`类型。它还接受响应体的通用参数，与`AxiosResponse`相同。`ServerError`是一个自定义类型，您可以根据自己知道的 API 返回来定义(例如`type ServerError = { code: string, description: string }`)。现在，我们可以像在 200 级快乐路径中那样返回响应体了。

`createUser`函数现在有一个隐式返回值`Promise<User | ServerError>`。

# 有什么意义？

似乎写了很多额外的代码，目的是什么？现在，调用堆栈中位于该函数之下的任何其他代码都将受益于它提供的静态类型。它可能是 Redux 中的一个 thunk，现在知道了返回类型，然后导致一个知道类型的动作创建者，然后是一个 reducer，然后是存储状态，一直到您的 UI 库。类型将到处传播，使您的整个代码库更有弹性。您不仅可以在命令运行时得到类型验证，而且这些类型将开始成为您、您未来的自己以及您的合作者可以使用的一种通用语言。

通过在 API 级别提供静态类型安全，您可以从底层开始锁定您的 TS 代码。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)