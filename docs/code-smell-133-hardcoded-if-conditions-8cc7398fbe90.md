# 代码气味 133 —硬编码 IF 条件

> 原文：<https://levelup.gitconnected.com/code-smell-133-hardcoded-if-conditions-8cc7398fbe90>

## *硬编码没问题。短时间内*

![](img/79f51f4653ad912979a2e9356ac40c24.png)

> *TL；DR:不要在 if 上留下硬编码的混乱。*

# 问题

*   易测性
*   硬编码值
*   违反开放/封闭原则

# 解决方法

1.  将所有的[if](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)替换为动态条件或[多态](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)。

# 语境

硬编码*如果*条件很棒的时候做[测试驱动开发](https://blog.devgenius.io/tdd-conference-2021-all-talks-e1eeef89497e)。

我们需要清理东西。

# 示例代码

## 错误的

```
private string FindCountryName (string internetCode)
{
  if (internetCode == "de")
    return "Germany";
  else if(internetCode == "fr") 
    return "France";
  else if(internetCode == "ar")
    return "Argentina";
    //lots of elses
  else
    return "Suffix not Valid";
}
```

## 对吧

```
private string[] country_names = {"Germany", "France", "Argentina"} //lots more
private string[] Internet_code_suffixes= {"de", "fr", "ar" } //moreprivate Dictionary<string, string> Internet_codes = new Dictionary<string, string>();//There are more efficient ways for collection iteration
//This pseudocode is for illustration
int currentIndex = 0; 
foreach (var suffix in Internet_code_suffixes) {
  Internet_codes.Add(suffix, Internet_codes[currentIndex]);
  currentIndex++;
}private string FindCountryName(string internetCode) {
  return Internet_codes[internetCode];
}
```

# 侦查

[X]自动

通过检查 If/else 条件，我们可以检测硬编码的条件。

# 标签

*   可安装文件系统

# 结论

在过去，硬编码不是一个选项。

使用现代方法，我们通过硬编码来学习，然后，我们概括和重构我们的解决方案。

# 关系

[](https://blog.devgenius.io/code-smell-36-switch-case-elseif-else-if-statements-73f0215abf2b) [## 代码气味 36 — Switch/case/elseif/else/if 语句

### 第一节编程课:控制结构。高级开发人员的教训:避免它们。

blog.devgenius.io](https://blog.devgenius.io/code-smell-36-switch-case-elseif-else-if-statements-73f0215abf2b) [](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269) [## 代码气味 102 —箭头代码

### 嵌套的 if 和 Elses 很难阅读和测试

blog.devgenius.io](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269) 

# 更多信息

*   [如何永远摆脱 IFs](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)
*   [测试驱动开发](https://blog.devgenius.io/tdd-conference-2021-all-talks-e1eeef89497e)

# 信用

杰西卡·约翰斯顿在 Unsplash 上的照片

> 不要太聪明。我的观点是不鼓励过于聪明的代码，因为“聪明的代码”很难写，容易出错，更难维护，并且通常不会比更简单的替代方案更快，因为它很难优化。

*比雅尼·斯特劳斯特鲁普*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)