# 代码味道 139 —用户界面中的业务代码

> 原文：<https://levelup.gitconnected.com/code-smell-139-business-code-in-the-user-interface-83b0941b657a>

## 验证是否应该在接口上进行？

![](img/832df7923cb0f2ce735e6116ece353e8.png)

> *TL；DR:总是在你的后端创建正确的对象。ui 是偶然的。*

# 问题

*   安全问题
*   代码复制
*   易测性
*   API、微服务等的可扩展性。
*   贫血和[易变的](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)物体
*   [双射违例](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)

# 解决方法

1.  将您的验证移到后端。

# 语境

代码重复是对过早优化的警告。

构建一个带有 UI 验证的系统可能会发展成 API 或外部组件消费。

我们需要在后端验证对象，并将有效的验证错误发送给客户端组件。

# 示例代码

## 错误的

```
<script type="text/javascript"> function checkForm(form)
  {
    if(form.username.value == "") {
      alert("Error: Username cannot be blank!");
      form.username.focus();
      return false;
    }
    re = /^\w+$/;
    if(!re.test(form.username.value)) {
      alert("Error: Username must contain only letters, numbers and underscores!");
      form.username.focus();
      return false;
    } if(form.pwd1.value != "" && form.pwd1.value == form.pwd2.value) {
      if(form.pwd1.value.length < 8) {
        alert("Error: Password must contain at least eight characters!");
        form.pwd1.focus();
        return false;
      }
      if(form.pwd1.value == form.username.value) {
        alert("Error: Password must be different from Username!");
        form.pwd1.focus();
        return false;
      }
      re = /[0-9]/;
      if(!re.test(form.pwd1.value)) {
        alert("Error: password must contain at least one number (0-9)!");
        form.pwd1.focus();
        return false;
      }
      re = /[a-z]/;
      if(!re.test(form.pwd1.value)) {
        alert("Error: password must contain at least one lowercase letter (a-z)!");
        form.pwd1.focus();
        return false;
      }
      re = /[A-Z]/;
      if(!re.test(form.pwd1.value)) {
        alert("Error: password must contain at least one uppercase letter (A-Z)!");
        form.pwd1.focus();
        return false;
      }
    } else {
      alert("Error: Please check that you've entered and confirmed your password!");
      form.pwd1.focus();
      return false;
    } alert("You entered a valid password: " + form.pwd1.value);
    return true;
  }</script><form ... onsubmit="return checkForm(this);">
<p>Username: <input type="text" name="username"></p>
<p>Password: <input type="password" name="pwd1"></p>
<p>Confirm Password: <input type="password" name="pwd2"></p>
<p><input type="submit"></p>
</form>
```

## 对吧

```
<script type="text/javascript"> // send a post to a backend
  // backend has domain rules
  // backend has test coverage and richmodels
  // it is more difficult to inject code in a backend
  // Validations will evolve on our backend
  // Business rules and validations are shared with every consumer
  // UI / REST / Tests / Microservices ... etc. etc.
  // No duplicated code
  function checkForm(form)
  {
    const url = "https://<hostname/login";
    const data = {
    }; const other_params = {
        headers : { "content-type" : "application/json; charset=UTF-8" },
        body : data,
        method : "POST",
        mode : "cors"
    }; fetch(url, other_params)
        .then(function(response) {
            if (response.ok) {
                return response.json();
            } else {
                throw new Error("Could not reach the API: " + response.statusText);
            }
        }).then(function(data) {
            document.getElementById("message").innerHTML = data.encoded;
        }).catch(function(error) {
            document.getElementById("message").innerHTML = error.message;
        });
    return true;
  }</script>
```

# 侦查

[X]半自动

我们可以在 UI 代码中发现一些行为模式

# 标签

*   易变性

# 例外

如果您有关于严重性能瓶颈的有力证据，您需要在前端自动复制您的业务逻辑。

你不能跳过后端部分。

您不应该手动创建它，因为您会忘记这样做。

# 结论

使用 [TDD](https://blog.devgenius.io/tdd-conference-2021-all-talks-e1eeef89497e) 。

您将把所有的业务逻辑行为放在您的域对象上。

# 关系

[](https://blog.devgenius.io/code-smell-97-error-messages-without-empathy-952c320976d8) [## 代码气味 97——没有同理心的错误消息

### 我们应该特别注意用户(和我们自己)的错误描述。

blog.devgenius.io](https://blog.devgenius.io/code-smell-97-error-messages-without-empathy-952c320976d8) [](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [## 代码气味 01——贫血模型

### 你的对象是一堆没有行为的公共属性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [](https://blog.devgenius.io/code-smell-40-dtos-ca35f5d8f7c9) [## 代码气味 40—dto

### dto 被广泛使用并“解决”了实际问题，是吗？

blog.devgenius.io](https://blog.devgenius.io/code-smell-40-dtos-ca35f5d8f7c9) [](https://blog.devgenius.io/code-smell-90-implementative-callback-events-df1d2b371087) [## 代码气味 90 —实现性回调事件

### 在创建事件时，我们应该将触发器与操作分离。

blog.devgenius.io](https://blog.devgenius.io/code-smell-90-implementative-callback-events-df1d2b371087) [](https://blog.devgenius.io/code-smell-78-callback-hell-aeb4a3010270) [## 代码气味 78 —回调地狱

### 将算法作为一系列嵌套回调来处理并不明智。

blog.devgenius.io](https://blog.devgenius.io/code-smell-78-callback-hell-aeb4a3010270) 

# 更多信息

*   [变种人的邪恶力量](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)

# 信用

Lenin Estrada 在 Unsplash 上拍摄的照片

> *我认为另一个好的原则是将演示或用户界面(UI)从你的应用的真正本质中分离出来。通过遵循这个原则，我一次又一次地幸运地做出改变。所以我认为这是一个很好的原则。*

马丁·福勒

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)