# 代码中违反单一责任原则(SRP)的症状

> 原文：<https://levelup.gitconnected.com/symptoms-of-single-responsibility-principle-srp-violation-in-your-code-dd14a7bd1698>

![](img/8334bf4998f5dcf54a2079f105a26325.png)

违反单一责任原则(SRP)也会给你和你的代码带来麻烦

*单一责任原则(SRP)是贯穿编程的最重要、众所周知和实践的原则之一[1]。尽管这个概念看起来很简单，但是实践 SRP 的重要性怎么强调都不为过。SRP 的简单性也导致了多年来对它的误解。这就是为什么知道你什么时候违反 SRP 是很重要的。*

*在这篇文章中，我们将讨论你的代码向你暗示的四种症状，它**可能**违反了单一责任原则。下面提到的要点是通过总结我多年来阅读的一些非常著名的编程书籍以及基于我的经验收集的。以下是四点。我们走吧！！🚶*

## ***1。你的模块体积很大***

*在这里，我们在计算容量时引用的度量是*行代码* (LOC)。Robert C. Martin 在他的书 [Clean Code](https://www.oreilly.com/library/view/clean-code-a/9780136083238/) 中明确提到*函数的第一条规则是它们应该很小*【2】。一个函数应该讲述一个故事，一个简短的故事。就是这样！虽然没有硬性规定函数应该有多长才能确认 SRP，但平均来说，任何 5-6 行长的函数都足够了。SRP 的定义足以断言一个函数应该非常小。*

> *一个软件模块应该只做一件事。*

*那么课程呢？对于班级，其体积不应以 LOC 计量，而应由*计量。**

> **一个软件模块应该只对一个角色负责。**

**这意味着，一个类应该只负责一件事。当一个类倾向于为多个参与者做多件事情时，这是该类需要重构的好迹象。关于这一点，我们将在文章的后面详细介绍。**

## **2.你不能用一句话来描述你的功能。你也不能不用‘和’，‘或’等词来描述它。😏**

**现在，试着用一句话来描述你的源代码中的函数是做什么的。如果您倾向于编写长句，或者倾向于编写使用诸如' ***或*** '以及' ***和*** '等词语的句子，则该功能可能违反 SRP。去试试吧！**

```
**function login(string $email, string $password) 
{
  try {
    if(isEmailValid($email) and isPasswordValid($password)) {
      $user = User::findFromEmail($email, $password);

      if($user) {
        // do something else like logging login event
        //return success with token
      }
      else {
        throw new Exception('No user found with given credentials');
      }
    }
    else {
    throw new Exception ('Invalid email or password')
    }
  }
  catch(Exception $e) {
    throw new Exception ('Exception occured');
  }
}**
```

**我们试着描述一下上面源代码中的`login`函数。这个`login`函数是做什么的？**

***登录函数以邮箱和密码为参数。如果电子邮件和密码的格式有效，并且凭据与数据库匹配，则该函数返回带有令牌的成功。如果用户不在数据库中，它会抛出一个异常，指出电子邮件或密码不正确。***

**类似这样的描述暗示了这个函数可能在做多件事情。存在将该功能分成更短的功能的范围。**

## **3.为您的函数或类编写测试似乎非常困难**

**当您为一个函数编写单元测试时，您可能会在同一个测试中编写多个断言。或者，您可能会编写许多测试来测试一个功能。这也可能表明该函数违反了 SRP，因为该函数做了许多事情，因此测试也必须断言所有这些事情。顺便说一下，这也有助于我们理解为什么*测试驱动开发(TDD)* 的实践有助于我们设计更模块化的代码，但这是另一篇文章的主题。**

**对另一个类有太多依赖的类也很难测试。你最可能遇到的一个问题是，由于所有这些依赖关系，构造函数可能很难初始化[4]。对一个类注入太多的依赖也是这个类做了太多事情的好迹象。**

## **4.你的类有服务于不同角色的函数**

**这一点只是`class`具体，而不是`functions`。让我们从参与者的角度再看一下 SRP 的定义。**

> **一个软件模块应该只对一个角色负责。**

**当一个类有多个函数为系统的不同参与者服务时，其中一个函数(或多个函数)内部的代码迟早会发生变化。因为归根结底，是系统的参与者决定了系统应该如何运行。像业务需求和用户行为这样的事情往往会一直改变软件的行为。**

**让我们以罗伯特·马丁书中的`Employee`类为例:[干净的架构](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/)【3】。**

```
**class Employee
{
  function calculatePay() { ... }
  function reportHours() { ... }
  function save() { ... }
}**
```

**假设类`Employee`的所有功能都简单、简短，并且只做一件事——那太完美了。所以这些函数当然都遵循 SRP。所以，自动类`Employee`应该遵循 SRP。对吗？可惜， ***没有*** 。**

**类`Employee`违反了 SRP，因为这三个函数负责服务三个不同的参与者。**

*   **`calculatePay()`负责*会计部门*及其政策的职能。**
*   **`reportHours()`负责*人力资源部*及其政策的职能。**
*   **`save()`功能负责*数据库管理员*。**

**同样，由于任何软件的行为都是由其用户决定的，或者说最终是由参与者决定的，所以参与者可以负责改变软件的行为。例如——如果会计部门明天改变了应该如何支付雇员的政策，这种改变应该反映在`calculatePay()`函数中。同样在同一天，DBA 团队更改了他们数据库中的*用户*表结构，这也应该反映在同一类的`save()`函数中。像这样的场景给这个类带来了额外的复杂性，因为开发人员单独处理这些函数会给文件带来不必要的合并错误。此外，两个完全不相关的功能之间不必要的协作在这里也很明显。在 PHP 这样的语言中，`calculatePay()`函数中的语法错误肯定会影响到函数`save()`，因为函数`save()`的客户端在调用`save()`时也会出错。**

# **结论**

**保持单一责任很重要，非常重要。在编写代码时，有许多危险信号是你应该注意的。从项目开始就坚持单一责任原则来维护一个良好的代码库是非常重要的，因为随着代码库的增长，增加不必要的依赖和代码重复的机会也会增加，结果是代码的可维护性、成本和时间也会增加。**

**干杯！！这是给你的咖啡，感谢你坚持到最后。☕️**

****参考文献****

**[1] ***单一责任原则***[https://en . Wikipedia . org/wiki/Single-respons ibility _ Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle)**

*****清洁码*** 作者*罗伯特·塞西尔·马丁***

**[3] ***干净的架构:软件结构和设计的工匠指南*** 作者*罗伯特·塞西尔·马丁***

**[4] ***有效地与遗留代码*** 一起工作**

**感谢您成为我们社区的一员！在你离开之前:**

*   **👏为故事鼓掌，跟着作者走👉**
*   **📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容**
*   **🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)**

**🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)**