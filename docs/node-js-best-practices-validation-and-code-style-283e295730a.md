# Node.js 最佳实践—验证和代码风格

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-validation-and-code-style-283e295730a>

![](img/69f7dbc76805cbddfa45da3ed07fb84a.png)

照片由 [peyman toodari](https://unsplash.com/@peymantdr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量应用程序。为了使维护它们变得更容易，我们必须为人们设定一些准则来遵循。

在本文中，我们将研究输入验证和编写具有良好风格的代码。

# 使用专用库验证参数

我们应该在我们的应用程序中验证输入，这样用户就不能提交无效的和导致数据损坏的数据。

对于 express 应用程序，我们应该检查我们的端点是否在检查有效的输入。如果我们还没有检查，我们应该检查一下。

例如，我们可以使用`express-validator`包来简化表单验证。要安装它，我们运行:

```
npm install --save express-validator
```

那么我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const { check, validationResult } = require('express-validator');const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.post('/', [
  check('email').isEmail(),
], (req, res, next) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(422).json({ errors: errors.array() });
  }
  const { email } = req.body;
  res.send(email);
});app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们用`check(‘email’).isEmail()`来检查与正文一起提交的电子邮件是否是有效的电子邮件。

然后在路由处理器中，如果它不是有效的电子邮件，我们将向用户发送一个错误响应。

# 使用 ESLint

JavaScript 是一种非常宽容的语言。这意味着它让我们做很多人们可能不喜欢的事情。它的动态类型和宽松的语法也很容易出错。

因此，我们应该像 ESLint 一样使用 linter 来检查可能的代码错误并修复不良样式。它对于识别像间距这样的小问题很有用，但是也检查严重的反模式，比如没有分类就抛出错误。

它还可以与“更漂亮”和“美化”结合使用，以一种易于阅读的方式格式化代码，

# ESLint 的 Node.js 特定插件

像`eslint-plugin-node`、`eslint-plugin-mocha`和`eslint-plugin-node-security`这样的节点有特定的 ESLint 插件。

他们检查我们代码中的节点特定错误和可能的安全问题，这样我们的应用程序就不会受到它们的影响。

# 在同一行开始代码块的花括号

对于块，我们应该在同一行开始花括号。例如，如果我们定义一个函数，我们写:

```
function foo() {
  // code block
}
```

而不是:

```
function foo() 
{
  // code block
}
```

# 正确分隔语句

我们应该使用分号来分隔我们的语句，我们应该适当地使用换行符来确保我们的代码易于阅读。它们都有助于消除常规语法错误。

ESLint、Prettier 和 Standardjs 可以自动解决这些问题。

我们不应该让 JavaScript 解释器自动插入分号。这是产生意外错误的一个简单方法。

正确分离状态的示例包括:

```
function foo() {
  // code block
}foo();
```

或者:

```
const items = [1, 2, 3];
items.forEach(console.log);
```

下面的例子是一个会抛出错误的坏例子:

```
const count = 1
(function foo() {

}())
```

我们将得到`1 is not a function`，因为它试图将`1`作为一个函数运行。为了避免错误，我们应该在`1`后面加一个分号:

```
const count = 1;
(function foo() {

}())
```

![](img/f789c67fc3ffd3fa1fd6bab45cc25cc1.png)

[斯蒂夫·亚当斯](https://unsplash.com/@sradams57?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 说出我们的功能

我们应该给我们的函数命名，这样我们就可以很容易地调试它们。匿名函数没有名字，所以很难追踪。在检查内存快照时，命名函数更容易理解我们在看什么。

# 使用变量、常量、函数和类的命名约定

小写是常量、变量和函数名的约定。命名类时使用大写字母 camel。这将帮助我们区分普通变量和函数，以及需要实例化的类。

此外，我们应该使用描述性的名称，但保持简短。

JavaScript 允许在不实例化构造函数的情况下调用构造函数，因此区分大小写很重要，这样我们就不会将构造函数与常规函数混淆。

例如，我们应该写:

```
function Person(name){
  this.name = name;
}function fooBar(){

}
```

对于函数，将变量命名为:

```
let fooBar = 1;
```

# 结论

我们应该根据惯例来命名事物，并对代码进行 lint 处理，以防止不良风格和语法错误。

此外，我们应该验证我们的输入，以防止数据损坏和其他错误。