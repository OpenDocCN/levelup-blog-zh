# 代码气味 160 —无效 Id = 9999

> 原文：<https://levelup.gitconnected.com/code-smell-160-invalid-id-9999-bbc9aaba7464>

## 对于无效 id 来说，Maxint 是一个非常好的数字。我们永远也到不了。

![](img/5124272b898c017780f34647bb10a443.png)

> *TL；DR:不要把真身份证和无效身份证配在一起。事实上:避免 id。*

# 问题

*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)违例
*   您可能会比想象中更快地找到无效 id
*   也不要对无效的 id 使用空值
*   将标志从调用者耦合到函数

# 解决方法

1.  用特殊对象模拟特殊情况。
2.  避免 9999，-1 和 0，因为它们是有效的域对象和实现耦合。
3.  引入空对象

# 语境

在计算的早期，数据类型是严格的。

然后我们发明了[十亿美元的错误](https://codeburst.io/null-the-billion-dollar-mistake-c2918c92f7e0)。

然后我们长大了，用多态特殊值建模特殊场景。

# 示例代码

## 错误的

```
#include "stdio.h"
#include "stdlib.h"
#include "stdbool.h"
#define INVALID_VALUE 999int main(void)
{    
    int id = get_value();
    if (id==INVALID_VALUE)
    { 
        return EXIT_FAILURE;  
        // id is a flag and also a valid domain value        
    }
    return id;
}int get_value() 
{
  // something bad happened
  return INVALID_VALUE;
}// returns EXIT_FAILURE (1)
```

## 对吧

```
#include "stdio.h"
#include "stdlib.h"
#include "stdbool.h"
// No INVALID_VALUE definedint main(void)
{    
    int id;
    id = get_value();
    if (!id) 
    { 
        return EXIT_FAILURE;
        // Sadly, C Programming Language has no exceptions
    }  
    return id;
} get_value() 
{
  // something bad happened
  return false;
}// returns EXIT_FAILURE (1)
```

# 侦查

[X]半自动

我们可以检查代码中的特殊常量和特殊值。

# 标签

*   空

# 结论

我们应该使用数字来关联外部标识符。

如果外部标识符不存在，那么它就不是一个数字。

# 关系

[](https://mcsee.medium.com/code-smell-120-sequential-ids-512e29ad31a0) [## 代码气味 120 —顺序 id

### 大多数 id 都是代码气味。顺序 id 也是一个漏洞

mcsee.medium.com](https://mcsee.medium.com/code-smell-120-sequential-ids-512e29ad31a0) [](https://blog.devgenius.io/code-smell-12-null-64fbd7792a7c) [## 代码气味 12 —空

### 程序员使用 Null 作为不同的标志。它可以提示缺席、未定义的值、错误等。

blog.devgenius.io](https://blog.devgenius.io/code-smell-12-null-64fbd7792a7c) 

# 更多信息

[](https://codeburst.io/null-the-billion-dollar-mistake-c2918c92f7e0) [## Null:十亿美元的错误

### 他不是我们的朋友。它不会简化生活，也不会让我们更有效率。只是更懒。是时候停止了…

codeburst.io](https://codeburst.io/null-the-billion-dollar-mistake-c2918c92f7e0) [](https://mcsee.medium.com/y2k22-the-mistake-that-embarrasses-us-1f9d3040a130) [## Y2K22 —让我们尴尬的错误

### 是 2022 年。我们在 20 世纪 50 年代继续编程

mcsee.medium.com](https://mcsee.medium.com/y2k22-the-mistake-that-embarrasses-us-1f9d3040a130) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

由 [Markus Spiske](https://unsplash.com/@markusspiske) 在 [Unsplash](https://unsplash.com/s/photos/flag-number) 上拍摄的照片

> *虫子潜伏在角落，聚集在边界。*

鲍里斯·贝泽

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)