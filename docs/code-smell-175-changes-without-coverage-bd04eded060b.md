# 代码味道 175 —没有覆盖的更改

> 原文：<https://levelup.gitconnected.com/code-smell-175-changes-without-coverage-bd04eded060b>

## *如果您的合并请求没有测试变更，您还没有完成您的工作*

![](img/ee12098b649b2423626bde221053c130.png)

> *TL；DR:不破坏一些测试就不要改代码。*

# 问题

*   质量
*   可维护性

# 解决方法

1.  掩盖你的代码。

# 语境

当您需要做出更改时，您需要更新代码的实时规范。

这就是为什么测试是为了？

你应该写一个覆盖使用场景，而不是生成你的代码做什么的死文档。

如果您更改未覆盖的测试，您需要添加覆盖率。

假设您用现有的覆盖率更改代码。你真幸运！去改变你的坏测试。

# 示例代码

## 错误的

```
export function sayHello(name: string): string {
  const lengthOfName = name.length;
-  const salutation = `How are you ${name}?, I see your name has ${lengthOfName} letters!`;
+  const salutation = `Hello ${name}, I see your name has ${lengthOfName} letters!`;
  return salutation;
}
```

## 对吧

```
export function sayHello(name: string): string {
  const lengthOfName = name.length;
-  const salutation = `How are you ${name}?, I see your name has ${lengthOfName} letters!`;
+  const salutation = `Hello ${name}, I see your name has ${lengthOfName} letters!`;
  return salutation;
}import { sayHello } from './hello';test('given a name produces the expected greeting', () => {
  expect(sayHello('Alice')).toBe(
    'Hello Alice, I see your name has 6 letters!'
  );
});
```

# 侦查

[X]自动

我们可以确保所有的合并请求都包含测试代码。

# 例外

如果您的代码和测试工具位于不同的存储库中，您可能会有不同的拉请求。

# 标签

*   质量

# 结论

测试覆盖率和功能代码一样重要。

测试系统是我们第一个也是更忠诚的客户。

我们需要关心他们。

# 关系

[](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [## 代码气味 05 —评论滥用者

### 代码有很多注释。注释与实现联系在一起，很难维护。

blog.devgenius.io](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

文森特·佩雷在 [Unsplash](https://unsplash.com/s/photos/umbrella) 上拍摄的照片

> *在遗留系统中使用一个方法之前，检查一下是否有针对它的测试。如果没有，就写下来。当你坚持这样做的时候，你就把测试作为一种交流的媒介。*

*迈克尔·费瑟斯*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)