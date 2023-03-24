# 将 If-Else 视为代码气味，直到被证明并非如此

> 原文：<https://levelup.gitconnected.com/treat-if-else-as-a-code-smell-until-proven-otherwise-3bd2c4c577bf>

## C#中 If-Else 重构技术

![](img/33f0eda99225c67872bd31075bcc0c30.png)

照片由[弗拉季斯拉夫·巴比延科](https://unsplash.com/@garri?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

If-Else 是任何软件应用程序中使用的最简单和最常见的编程结构之一，不管它有多复杂。

只要使用得当，使用 If-Else 语句并没有错。

If-Else 语句相当简单，写起来也很快，这使得它很容易被滥用。

开发者无意识或有意识地可以选择 If-Else 作为问题的短期解决方案。If-Else 通常是一种代码气味，它可能预示着糟糕的设计决策和重构的需要。

如何知道 If-Else 是否被误用？你如何知道在 If-Else 中选择哪个？

让我们深潜吧！

# 将 If-Else 重构为值对象

对原始数据类型的条件检查可以隐藏原始困扰问题。

让我们看一个验证和更改用户电子邮件地址的简单例子。

## If-Else 方法

字符串数据类型用于存储用户的电子邮件地址。从编程语言的角度来看，这种方法是完全有效的，因为可以存储在字符串类型中的所有可能的字符串的范围比所有可能的电子邮件地址的范围大得多。换句话说，每个电子邮件都是一个字符串，但不是每个字符串都是电子邮件。

然而，由于一个字符串可能不仅包含电子邮件，我们必须在每次保存电子邮件、更改电子邮件、发送电子邮件或从应用程序的不同部分执行一些其他重要操作之前编写验证逻辑，以确保该字符串包含有效的电子邮件。这可能导致复制和粘贴验证规则。

通过创建一个单独的类来解决原语困扰问题，该类包含原语字段和与该字段相关联的验证逻辑。

## 价值对象方法

上述方法的主要好处是一旦`UserEmail`对象被创建，我们不再需要验证它。该对象将始终有效。

任何将字符串赋给`UserEmail`的尝试都将导致编译器错误，因此不需要像原语一样不断检查`UserEmail`。

# 重构 If-Else 来陈述设计模式

你有没有注意到，你第一次点击 YouTube 上的喜欢按钮，喜欢的数量增加，但再次点击后，喜欢的数量减少了？或者，如果你点击了不喜欢按钮，不喜欢的数量会增加，但如果你点击了喜欢按钮后又点击了不喜欢，喜欢的数量也会减少？

做同样的动作会导致不同的结果，这意味着正在跟踪喜欢和不喜欢数量的对象有一个状态。

让我们首先用一种简单的方法实现这个逻辑:

## If-Else 方法

对象的状态存储在`Current`属性中。每次我们调用`Like`方法，状态都会发生变化。每个后续调用将执行不同的条件分支，因此行为会有所不同。

下面是如何使用状态设计模式实现相同的逻辑:

## 状态设计模式

这个例子**纯粹是教育性的**，目的是演示如何实现状态模式。事实上，这是过度工程化，因为有少量固定数量的状态具有简单的逻辑。

在以下情况下，开发人员可以考虑使用状态设计模式:

*   对象的行为取决于其状态。
*   与每个状态相关的逻辑是复杂的。
*   有许多条件决定了从一种状态到另一种状态的转变。

如果开发人员使用一个 if-else 块来实现满足上述所有三个条件的逻辑，代码就变成了意大利面条代码。

# 重构 If-Else 到表驱动方法

通常在软件开发过程中，有必要根据提供的输入参数来解析一个值。

例如，范围 1 … 12 对应于月份名称“一月”…“十二月”。或者像 json、xml、yaml 这样的扩展类型对应于`JsonParser`、`XmlParser`和`YamlParser.`类

通常应该有一种方法可以根据提供的输入来解析值，使用条件语句似乎是很自然的。

## If-Else 方法

在这个例子中，表示文件扩展名的字符串对应于具体的类类型。

这种重复的代码可以通过引入一个`Dictionary<TKey, TValue>`来简化，其中 key 是 extension，value 是 lambda 函数，它实例化并返回一个解析器的实例。

## 表格驱动方法

注意，字典的值也可以只是`Parser`而不是`Func<Parser>`类型。这完全取决于你的具体情况。如果每次通过键访问字典时都需要一个新的实例，请选择 lambda 表达式。如果您希望所有的解析器实例被创建一次，使用`Dictionary<string, Parser>`类型并初始化它，如下所示:

```
Dictionary<string, Parser> _parsers = 
   new Dictionary<string, Parser>()
   {
      { ".json", new JsonParser() },
      { ".xml", new XmlParser() },
      { ".yaml", new YamlParser() }
   };
```

转向表驱动的方法有几个好处:

*   代码变得更加紧凑，可读性更强。
*   不应该再修改`Create`方法来引入新的解析器类型(**开/闭原则**)。
*   存储扩展和解析器之间映射的字典可以被其他代码重用，因为它不再被硬编码在`Create`方法中。

# 重构 If-Else 以切换表达式

实现输入到值的映射的另一种方法是构造一个开关表达式。

让我们记住基于 If-Else 的`ParserFatory`类的实现:

## If-Else 方法

下面是如何使用开关表达式语法来简化它:

## 开关表达式方法

注意，每次调用`GetParser`方法时，解析器都会被实例化。如果您希望它们只被创建一次，您需要使用`Dictionary<string, Parser>`类型并初始化它一次。

# 重构 If-Else 到策略设计模式

根据一组规则检查对象的需要会导致代码中出现单一的 if-else 块。

## If-Else 方法

在这里，我们根据一组规则检查文件。检查文件名长度的规则总是应用于每个文件，其余的检查取决于用户的类型。特权用户可以上传扩展名范围更广的较大文件。

当我们只需要在应用程序的不同位置为标准用户重用文件验证逻辑时，就会出现问题。没有复制和粘贴，这是不可能的，因为我们只有一个 monolith 方法可以做所有的事情。此外，大型方法更难进行单元测试，因为更难确保覆盖所有情况。

这些问题可以通过将代码重构为策略模式来解决。

## 战略设计模式

我们最终得到了更小的方法`ValidateFile`和两个封装了特定于用户的验证规则的新的可重用类。

# 重构 If-Else 到 Lazy <t>类</t>

对象的延迟加载可以用几行代码实现。

## If-Else 方法

只有当客户端调用`Instance`属性时，才会创建`Config`类的实例。不早也不晚。

使用条件检查来实现延迟加载对于多线程环境来说是一种代码味道，因为这样的实现不是线程安全的。

应该使用`Lazy<T>`类，而不是手写的惰性加载模式。

## 懒惰的<t>方法</t>

`Lazy<T>`对象是懒惰的、线程安全的，并且还实现了一种性能更好的[双重检查锁定](https://en.wikipedia.org/wiki/Double-checked_locking)技术。

# 结论

我们不能完全摆脱 If-Else 语句，这也不是我们真正的目标。除了执行各种代码分支的主要目的之外，If-Else 结构还有一个次要目的——向我们展示通过应用适当的面向对象设计模式和技术，我们可以在哪些地方改进系统。为什么不利用这些技巧呢？

## 我的其他文章:

[](/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0) [## 如何专业地对 Bug 修复进行代码审查

### 审查 bug 修复时要问的几个重要问题。

levelup.gitconnected.com](/how-to-professionally-to-do-a-code-review-of-a-bug-fix-f17de72d42e0) [](/the-simplest-explanation-of-adapter-design-pattern-cd37f02bfecd) [## 适配器设计模式的最简单解释

### C#中的真实世界示例

levelup.gitconnected.com](/the-simplest-explanation-of-adapter-design-pattern-cd37f02bfecd) [](/you-are-simply-injecting-a-dependency-thinking-that-you-are-following-the-dependency-inversion-32632954c208) [## 你只是简单地注入一个依赖，认为你在遵循依赖倒置…

### 澄清差异。

levelup.gitconnected.com](/you-are-simply-injecting-a-dependency-thinking-that-you-are-following-the-dependency-inversion-32632954c208)