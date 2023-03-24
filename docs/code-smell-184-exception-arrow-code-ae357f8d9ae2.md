# 代码气味 184 —异常箭头代码

> 原文：<https://levelup.gitconnected.com/code-smell-184-exception-arrow-code-ae357f8d9ae2>

## *箭码是一种码味。污染是另一个例外。这是一个致命的组合。*

![](img/1cf4002fac59036d67764bb77de9b228.png)

> *TL；DR:不要级联你的异常*

# 问题

*   可读性
*   复杂性

# 解决方法

1.  重写嵌套子句

# 语境

同样,[箭头代码](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269)很难阅读，当我们必须以级联方式处理主题时，处理异常是常见的情况。

# 示例代码

## 错误的

```
class QuotesSaver {
    public void Save(string filename) {
        if (FileSystem.IsPathValid(filename)) {
            if (FileSystem.ParentDirectoryExists(filename)) {
                if (!FileSystem.Exists(filename)) {
                    this.SaveOnValidFilename(filename);
                } else {
                    throw new I0Exception("File exists: " + filename);
                }
            } else {
                throw new I0Exception("Parent directory missing at " + filename);
            }
        } else {
            throw new ArgumentException("Invalid path " + filename);
        }
    }
}
```

## 对吧

```
public class QuoteseSaver {
    public void Save(string filename) {
        if (!FileSystem.IsPathValid(filename)) {
            throw new ArgumentException("Invalid path " + filename);
        } else if (!FileSystem.ParentDirectoryExists(filename)) {
            throw new I0Exception("Parent directory missing at " + filename);
        } else if (FileSystem.Exists(filename)) {
             throw new I0Exception("File exists: " + filename);
        }
        this.SaveOnValidFilename(filename);
    }
}
```

# 侦查

[X]半自动

当我们有这种复杂性时，一些短绒警告我们

# 标签

*   例外

# 结论

例外情况没有正常情况严重。

如果我们需要读取比正常情况更多的异常代码，那么是时候改进我们的代码了。

# 关系

[](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269) [## 代码气味 102 —箭头代码

### 嵌套的 if 和 Elses 很难阅读和测试

blog.devgenius.io](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269) [](https://blog.devgenius.io/code-smell-26-exceptions-polluting-9246aca40234) [## 气味代码 26 —污染例外

### 有许多不同的例外是非常好的。您的代码是声明性的和健壮的。还是没有？

blog.devgenius.io](https://blog.devgenius.io/code-smell-26-exceptions-polluting-9246aca40234) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由 [Remy Gieling](https://unsplash.com/@gieling?) 在 [Unsplash](https://unsplash.com/s/photos/archer) 上拍摄

> 除非你拒绝改正，否则错误不会变成错误。

奥兰多·金鲁贤·巴蒂斯塔

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)