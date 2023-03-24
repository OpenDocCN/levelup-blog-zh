# 使用依赖注入使你的代码可测试

> 原文：<https://levelup.gitconnected.com/use-dependency-injection-to-make-your-code-testable-2ba2801189a5>

## 不要让单元测试对你自己太苛刻

![](img/67b4545c34c33f6f3abad8960ba6541b.png)

[CDC](https://unsplash.com/@cdc?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

你是否曾经想为你的代码编写单元测试，但是你发现很难做到？这通常是没有考虑到测试而编写代码的结果。解决这个问题的一个简单方法是利用[测试驱动开发](https://medium.com/swlh/a-case-for-test-driven-development-8e36df33dc21)，这是一个在应用程序代码之前编写测试*的开发过程。*

但是，即使您不喜欢测试驱动开发，您仍然可以通过使用一种简单的技术**依赖注入**来使您的代码更容易测试，我们将在本文中讨论这一点。

# 什么是依赖注入？

依赖注入是一种非常简单却非常强大的技术。简而言之，函数不是将依赖关系硬编码到函数中，而是允许使用函数的开发人员通过参数传递任何需要的依赖关系。

为了帮助巩固这个概念，让我们一起来看一个例子。

# 解析 Cookie 字符串

![](img/b33c78c87ead6aa57ac3705c213d4e66.png)

约翰·丹西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

假设您想要编写一个 JavaScript 函数，它可以从`document.cookie`字符串中解析出单个 cookie 键值对。

例如，假设您想检查是否有一个名为`enable_cool_feature`的 cookie，如果它的值是`true`，那么您想为浏览您站点的用户启用一些很酷的特性。

不幸的是，在 JavaScript 中使用`document.cookie`字符串是非常糟糕的。如果我们可以用类似`document.cookie.enable_cool_feature`的东西来查找房产价值就好了，但是，唉，我们不能。

因此，我们将求助于编写我们自己的 cookie 解析函数，该函数将在一些潜在复杂的底层代码上提供一个简单的外观。

(声明一下，有几个 JavaScript 库和包已经完全做到了这一点，所以除非你想，否则不要觉得有必要在你自己的应用程序中重新编写这个函数。)

作为第一步，我们可能希望定义一个简单的函数，如下所示:

```
function getCookie(cookieName) { /* body here */ }
```

该函数将允许我们通过如下调用来查找特定 cookie 的值:

```
getCookie('enable_cool_feature')
```

# 样本溶液

![](img/9a911a290327b5a11db7679df5c2c641.png)

由[absolute vision](https://unsplash.com/@freegraphictoday?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在谷歌上搜索“如何解析 JavaScript 中的 cookie 字符串”,会发现来自不同开发者的许多不同的解决方案。对于这篇文章，我们将看看由 [W3Schools](https://www.w3schools.com/js/js_cookies.asp) 提供的解决方案。看起来是这样的:

```
export function getCookie(cookieName) {
  var name = cookieName + '='
  var decodedCookie = decodeURIComponent(document.cookie)
  var ca = decodedCookie.split(';') for (var i = 0; i < ca.length; i++) {
    var c = ca[i]
    while (c.charAt(0) == ' ') {
      c = c.substring(1)
    } if (c.indexOf(name) == 0) {
      return c.substring(name.length, c.length)
    }
  } return ''
}
```

# 对样本解决方案的批评

![](img/7df6e1ca8432d01bd124ff02318f2c7c.png)

[傅永华](https://unsplash.com/@hhh13?utm_source=medium&utm_medium=referral)摄于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

现在，这有什么问题？我们不会批评代码的主体本身，而是看这一行代码:

```
var decodedCookie = decodeURIComponent(document.cookie)
```

函数`getCookie`依赖于`document`对象和`cookie`属性！乍一看，这似乎没什么大不了的，但它确实有一些缺点。

首先，如果出于某种原因，我们的代码无法访问`document`对象，该怎么办？例如，在节点环境中，`document`是`undefined`。让我们看一些样本测试代码来说明这一点。

让我们使用 Jest 作为测试框架，然后编写两个测试:

```
import { getCookie } from './get-cookie-bad'describe('getCookie - Bad', () => {
  it('can correctly parse a cookie value for an existing cookie', () => {
    document.cookie = 'key2=value2'
    expect(getCookie('key2')).toEqual('value2')
  }) it('can correctly parse a cookie value for a nonexistent cookie', () => {
    expect(getCookie('bad_key')).toEqual('')
  })
})
```

现在让我们运行我们的测试来看看输出。

```
ReferenceError: document is not defined
```

哦不！在节点环境中，`document`没有定义。幸运的是，我们可以在`jest.config.js`文件中更改 Jest 配置，指定我们的环境应该是`jsdom`，这将创建一个 DOM 供我们在测试中使用。

```
module.exports = {
  testEnvironment: 'jsdom'
}
```

现在，如果我们再次运行我们的测试，他们通过。但是，我们仍然有一点问题。我们正在全局修改`document.cookie`字符串，这意味着我们的测试现在是相互依赖的。如果我们的测试以不同的顺序运行，这会导致一些奇怪的测试用例。

例如，如果我们在第二个测试中写`console.log(document.cookie)`，它仍然会输出`key2=value2`。哦不！这不是我们想要的。我们的第一次测试影响了我们的第二次测试。在这种情况下，第二个测试仍然通过了，但是当您的测试没有相互隔离时，很可能会陷入一些混乱的情况。

为了解决这个问题，我们可以在第一次测试的`expect`声明之后做一些清理工作:

```
it('can correctly parse a cookie value for an existing cookie', () => {
  document.cookie = 'key2=value2'
  expect(getCookie('key2')).toEqual('value2')
  document.cookie = 'key2=; expires = Thu, 01 Jan 1970 00:00:00 GMT'
})
```

(一般来说，我会建议你用一个`afterEach`方法来做一些清理工作，它会在每次测试后运行其中的代码。但是，不幸的是，删除 cookies 并不像说`document.cookie = ''`那么简单。)

W3Schools 解决方案的第二个问题是，如果您想要解析当前没有在`document.cookie`属性中设置的 cookie 字符串。你是怎么做到的？在这种情况下，你不能！

# 有更好的方法

![](img/0f5c7e531644c3f5dfba73f7315c7b5a.png)

[Cam Adams](https://unsplash.com/@camadams?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

既然我们已经探索了一个可能的解决方案和它的两个问题，让我们看看一个更好的方法来编写这个方法。我们将使用依赖注入！

我们的函数签名看起来与我们最初的解决方案有些不同。这一次，它将接受两个参数:

```
function getCookie(cookieString, cookieName) { /* body here */ }
```

所以我们可以这样称呼它:

```
getCookie(<someCookieStringHere> 'enable_cool_feature')
```

一个示例实现可能如下所示:

```
export function getCookie(cookieString, cookieName) {
  var name = cookieName + '='
  var decodedCookie = decodeURIComponent(cookieString)
  var ca = decodedCookie.split(';') for (var i = 0; i < ca.length; i++) {
    var c = ca[i]
    while (c.charAt(0) == ' ') {
      c = c.substring(1)
    } if (c.indexOf(name) == 0) {
      return c.substring(name.length, c.length)
    }
  } return ''
}
```

请注意，这个函数与原始函数之间的唯一区别是，该函数现在接受两个参数，并且在解码第 3 行的 cookie 时使用参数作为`cookieString`。

现在让我们为这个函数编写两个测试。这两个测试将测试与我们最初的两个测试相同的东西:

```
import { getCookie } from './get-cookie-good'describe('getCookie - Good', () => {
  it('can correctly parse a cookie value for an existing cookie', () => {
    const cookieString = 'key1=value1;key2=value2;key3=value3'
    const cookieName = 'key2'
    expect(getCookie(cookieString, cookieName)).toEqual('value2')
  }) it('can correctly parse a cookie value for a nonexistent cookie', () => {
    const cookieString = 'key1=value1;key2=value2;key3=value3'
    const cookieName = 'bad_key'
    expect(getCookie(cookieString, cookieName)).toEqual('')
  })
})
```

请注意我们如何完全控制我们的方法现在使用的 cookie 字符串。

我们不必依赖环境，我们不会遇到任何测试挂起，我们也不必假设我们总是直接从`document.cookie`解析 cookie。

好多了！

# 结论

就是这样！依赖注入实现起来非常简单，并且通过使您的测试易于编写并且使您的依赖易于模仿，它将极大地改善您的测试体验。(更不用说它有助于解耦您的代码，但这是另一天的主题。)

感谢阅读！