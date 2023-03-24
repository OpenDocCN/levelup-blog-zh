# ㄥ8ɩllǝɯsǝpoɔ—spɹɐʍʞɔɐq ǝslǝ/ⅎi

> 原文：<https://levelup.gitconnected.com/%E3%84%A58%C9%A9-ll%C7%9D%C9%AFs-%C7%9Dpo%C9%94-sp%C9%B9%C9%90%CA%8D%CA%9E%C9%94%C9%90q-%C7%9Dsl%C7%9D-%E2%85%8Ei-beab7a040d66>

## *我们在 if 条件之后读到的第一件事是 IF*

![](img/c8ab025478f20dd8942cb8af2c1db5e7.png)

> *TL；大卫:你在 else 上有重要的 else 条件。*

# 问题

*   易读性

# 解决方法

1.  交换条件。

# 语境

以优雅的方式编写 IF 子句并不像看起来那么简单。

有很多变化和选择。

我们需要特别注意可读性。

# 示例代码

## 错误的

```
fun addToCart(item: Any) {
    if (!cartExists()) {
        // Condition is negated
        this.createCart();
        this.cart.addItem(Item);
        // Repeated Code
    }
    else {
        // Normal case is on the else clause
        this.cart.addItem(Item);
    }
}
```

## 对吧

```
fun addToCart(item: Any) {
    if (cartExists()) {
        this.cart.addItem(Item);
    }
    else {      
        this.createCart();
        this.cart.addItem(Item);
    }
}   

fun addToCartShorter(item: Any) {
    if (!cartExists()) {
        this.createCart();
    }
    this.cart.addItem(Item);    
}
```

# 侦查

[X]半自动

我们可以找到 IF 条件的否定表达式，并检查这种反模式。

# 标签

*   可安装文件系统

# 结论

我们需要像阅读散文一样阅读代码。

人们首先阅读标准案例，然后阅读例外案例。

# 关系

[](https://blog.devgenius.io/code-smell-51-double-negatives-993b6160f3e1) [## 气味代码 51 —双重否定

### 不是操作员是我们的朋友。不是不是操作员不是我们的朋友

blog.devgenius.io](https://blog.devgenius.io/code-smell-51-double-negatives-993b6160f3e1) [](/code-smell-156-implicit-else-2e5c96e64c0c) [## 代码气味 156 —隐式 Else

### 我们在第一个编程日学习 if/else。然后我们忘记其他的

levelup.gitconnected.com](/code-smell-156-implicit-else-2e5c96e64c0c) 

# 更多信息

*   [知道密码](https://knowthecode.io/if-else-backwards-code-pattern)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[凯罗尔·卡萨尼斯基](https://unsplash.com/@karolkas)在 [Unsplash](https://unsplash.com/s/photos/upside) 上拍摄

> 因为软件是如此复杂，所以美观在计算中比在技术中的任何其他地方都更重要。美丽是对抗复杂的终极防御。

d .杰伦特

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)