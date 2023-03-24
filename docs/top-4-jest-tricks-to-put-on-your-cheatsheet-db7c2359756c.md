# 放在你的备忘单上的 4 个笑话

> 原文：<https://levelup.gitconnected.com/top-4-jest-tricks-to-put-on-your-cheatsheet-db7c2359756c>

## 通过这些简单的技巧，测试不一定是痛苦的

![](img/67d7f99fbedf8a9a9faf8542f1fac60b.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我最讨厌的过去之一是用 Javascript 进行测试。没有什么比摸索模拟、处理错误和试图弄清楚我的代码到底怎么了更让我兴奋的了。

尽管非常不喜欢测试，但它还是有极大的价值，尤其是在开发风格中，比如测试驱动开发(TDD)。测试驱动开发是通过测试用例充实软件规范，并通过使用所述测试用例进行持续测试来跟踪开发进度的过程。

为了帮助你最小化痛苦，我已经编辑了四个必要的有用的提示来克服测试中那些讨厌的错误和痛点*(它也可以作为每当我必须重新测试时的提醒)*。

## 模仿类模块

Javascript 类非常适合使用，它们有助于模块化和封装您的数据。

假设一个用户类管理一个软件应用程序的单个用户实例。每个实例都用名称、年龄和电子邮件进行初始化。在演示中，让我们将 name、age 和 email 类变量设为私有，id 类变量设为公共，并且只通过 getter 方法返回这些信息。

getter 方法接受一个可选参数`*publicOnly*` *。*如果`*publicOnly*` 为真，那么只返回用户的姓名和年龄。但是，如果是 *false* ，则返回用户的所有信息。

```
/src/services/user.serviceclass User { id
  private name
  private age
  private email constructor(fullName, userAge, userEmail) {
    this.name = fullName
    this.age = userAge
    this.email = userEmail
  }

  getUser(publicOnly = true) {
    if (publicOnly) return { name: this.name, age: this.age }
    return { id: this.id, name: this.name, age: this.age, email: this.email }
  }
}
```

对这个模块进行单元测试或多或少非常简单。然而，如果这只是您的应用程序流程的一部分，会发生什么呢？如何模拟这个类来测试应用程序流中更重要的部分？

您可以模拟整个类，并模拟您的测试需要使用`jest.mock`进行交互的特定方法。

```
**jest.mock**('../../src/services/user.service', () *=>* {
    *return* **jest.fn().mockImplementation**(() *=>* {
        *return* { *getUser*: jest.fn().mockReturnValue(
          { name: 'Test User',
            age: 28,
            email: 'test-email@gmail.com'}) }
    })
})
```

如果您有与 API 交互或与其他下游服务通信的类方法，这种模仿方式是有益的。我们可以绕过所有额外的通信，为我们的测试提供正确执行所需的精确值。

## 处理承诺

当我们与 API 或其他下游服务通信时，我们经常会遇到令人生畏的*承诺*。但是，我向你保证，他们没有你想的那么可怕！如果你不熟悉 Jest 丰富的库，测试承诺可能会有点混乱，但是嘲笑和处理它们并没有那么糟糕！

如果您需要模拟一个本质上异步的模块，例如 HTTP 请求库`axios`，您可以使用两种方法`mockRejectedValue`和`mockResolvedValue`中的一种

类似于`mockReturnValue`，`mockRejectedValue`和`mockResolvedValue`处理承诺的拒绝和解决途径。

让我们添加上一个例子中的用户服务，并添加一个使用`axios`向下游服务发送用户数据请求的方法。如果 post 请求成功，它将返回我们刚刚添加的客户的标识符，我们将用该标识符更新我们的类

```
async upsertUser() {
    try {
       const { data } = await axios.post('/user', 
         { name: this.name, age: this.age, email: this.email }) const { customerID } = data
       this.id = customerID } catch (e) {
       throw new "Cannot fulfill request at this time" }}
```

让我们测试一下这个方法是否正确地设置了标识符。

用 jest mock 函数直接初始化`axios.post`，然后用`mockResolvedValue`将它链接起来，设置一个测试返回值。然后，我们调用这个模拟函数，而不是调用实际的`/user`端点。我们不关心客户 id 的值，我们希望确保`upsertUser` 方法在 POST 成功时设置 ID 类变量。

```
test('Upsert user and set id', **async** () => {
   ** axios.post = jest.fn().mockResolvedValue(
         { data: { customerID : 18920 } })** const userA = new User('Cory', 19, 'cory@gmail.com')
    await userA.upsertUser()

    expect(userA.id).toEqual(18920)}) 
```

## 测试路线

测试 API 端点不是我经常遇到的事情，所以这个测试领域对我来说很新，我想它对你们中的一些人来说也是如此。所以为了减轻你的担心，我建议你去 NPM 图书馆。

如果你和我一样，你可能会想，*“难道没有另一种不使用外部库来测试路由的方法吗？”*

我的回答是:*不太会。*

在开发的时候，通常你的服务器都是启动的，所以你可以很容易地在你的浏览器，邮差，或者卷曲它们。但是，在测试环境中，您的服务器是离线的。所以端点还没有真正“存在”。那么你如何解决这个问题呢？

[Supertest](https://www.npmjs.com/package/supertest) 是一个 NPM 软件包，允许您

> 为测试 HTTP 提供一个高级抽象，同时仍然允许您下降到较低级别的 API——Supertest

这意味着您不必担心您的服务器运行或者您的测试将如何与您的服务器通信；相反，您可以专注于您已经创建的端点以及上游服务将如何与它们通信。

```
describe('User Route', () => {
  test('/ returns successfully', async () => {
    const response = **await** **request(app)
      .get('/api/v1/users')
      .set('Authorization', 'Bearer testing1234')**

    expect(response.status).toEqual(200)
    expect(response.body).toEqual({ success: true, data: [] })
  })
})
```

虽然在这个例子中没有显示，但是您也希望在模拟路由时模拟路由与之通信的下游服务。这确保了您不会受到其他服务的连接和通信的妨碍，并且您可以将测试集中在路由处理程序中的逻辑上。

## 测试异步错误

这最后一个技巧可能是我存在的祸根，因为我花了很长时间才弄明白它是如何工作的！此外，如果做得不正确，你的测试会产生假阳性。

类似于我们在前面的例子中所涉及的，我们可以利用`mockRejectedValue`来帮助测试错误。如果您的服务在任何时候抛出一个错误，它将遵循一个承诺的拒绝路径，因此我们可以使用这个方法来模拟该行为。

对于这些例子，我将使用`http-errors`库来帮助呈现您可以捕获的不同类型的 HTTP 错误，但是请记住这适用于所有的 Javascript 错误。

假设您想要模拟一个失败的 POST 请求。也许你想模拟一个未经授权的请求。

```
const error = *new* CreateError.Unauthorized('You are unauthorized')
axios.post = **jest.fn().mockRejectedValue(error)**
```

创建错误的新实例，但不要抛出它！将该错误实例传递给`mockRejectedValue`方法，类似于我们在使用`mockResolvedValue`的示例中所做的。

```
await expect(service.upsertUser()).rejects.toThrowError(error)
```

然后，为了测试正在进行下游调用的服务，将关键字`await`添加到 expect 语句的**前面**处(不在语句内),并将`expect`与`rejects.toThrowError(error)`链接起来

这个链检查 expect 方法`service.upsertUser()`中调用的方法是否返回了一个被拒绝的承诺，当所述承诺被解除时，它将抛出一个错误。

我最大的错误是在`service.upsertUser()`调用前面添加了`await`关键字。但是，通过这样做，即使控制台显示函数的预期/实际返回值有差异，如果抛出任何错误，测试也会通过。

我希望这些技巧对您有用，请记得关注并订阅更多的编码技巧和技巧！✌️

编码快乐！