# 代码气味 178 —违反子集

> 原文：<https://levelup.gitconnected.com/code-smell-178-subsets-violation-3b3b9cb15d85>

## *不可见的物体有我们需要在单点实施的规则*

![](img/b4e0964f3eabf8be0ee6e74884410fac.png)

> *TL；DR:创建小对象，限制你的领域。*

# 问题

*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)故障
*   失败快速原则违反
*   [重复代码](https://blog.devgenius.io/code-smell-46-repeated-code-433d3feb516d)验证

# 解决方法

1.  创建小对象并验证域。

# 语境

这是一种原始的痴迷气味。

*电子邮件地址*是*字符串*的子集。

*有效年龄*是*真实年龄*的子集。

*端口*是*整数*的子集。

一个单词是字符串的子集。

# 示例代码

# 错误的

```
destination = "destination@example.com"destination = "destination.example.com"
// No error thrown
```

# 对吧

```
public class EmailAddress {
    public String emailAddress; public EmailAddress(String address) {
        string expressions = @"^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$";
        if (!Regex.IsMatch(email, expressions) {
          throw new Exception('Invalid address');
        }
        this.emailAddress = address;
    }
}destination = new EmailAddress("destination@example.com");
```

不要与贫血的 Java 版本[混淆](http://officedev.github.io/ews-java-api/docs/releases/api-2.0/apidocs/microsoft/exchange/webservices/data/property/complex/EmailAddress.html)

# 侦查

[X]手册

这是一种语义气味。

# 标签

*   原始痴迷

# 结论

我们需要忠于真实世界的投影。

子集对于早期验证和快速失败原则非常重要。

# 关系

[](https://mcsee.medium.com/code-smell-122-primitive-obsession-ad1f821f7158) [## 代码气味 122——原始痴迷

### 物品就在那里等待挑选。即使是最小的。

mcsee.medium.com](https://mcsee.medium.com/code-smell-122-primitive-obsession-ad1f821f7158) 

# 更多信息

[](https://codeburst.io/fail-fast-3f3f036032b0) [## 快速失败

### 失败是时尚。制造比思考容易得多，失败不是耻辱，让我们把这个想法带到我们的…

codeburst.io](https://codeburst.io/fail-fast-3f3f036032b0) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由 [Mona Eendra](https://unsplash.com/@monaeendra) 在 [Unsplash](https://unsplash.com/s/photos/boxed?) 上拍摄

> 每个工匠都从一套基本的优质工具开始他或她的旅程。

安德鲁·亨特

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)