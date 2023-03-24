# 气味代码 148 — ToDos

> 原文：<https://levelup.gitconnected.com/code-smell-148-todos-5a23d024ad70>

## 我们为未来的自己购买债务。是回报的时候了

![](img/ad12d2b6e35f5760444ab5259db3e12f.png)

> *TL；DR:不要在你的代码中留下 TODOs。修理他们！*

# 问题

*   技术债务
*   可读性
*   缺乏信心

# 解决方法

1.  解决你的 TODOs

# 语境

我们在代码中遇到了 TODOs。我们数了数。

我们很少解决它。

我们开始欠技术债。

然后我们支付债务+利息。

几个月后，我们支付的利息比原来的债务多。

# 示例代码

## 错误的

```
public class Door
{ 
    private Boolean isOpened; public Door(boolean isOpened)
    {       
        this.isOpened = false;
    }    public void openDoor()
    {
        this.isOpened = true;
    } public void closeDoor()
    {
        // TODO: Implement close door and cover it
    } }
```

## 对吧

```
public class Door
{ private Boolean isOpened; public Door(boolean isOpened)
    {       
        this.isOpened = false;
    }    public void openDoor()
    {
        this.isOpened = true;
    } public void closeDoor()
    {
        this.isOpened = false;
    } }
```

# 侦查

[X]自动

我们可以数托多斯。

# 标签

*   技术债务

# 结论

我们可以数托多斯。

大多数棉绒都这样做。

我们需要政策来减少它。

如果我们使用 TDD，我们马上写下缺失的代码。

在这种情况下，TODOs 只有在进行深度优先开发以记住开放路径时才有效。

# 更多信息

*   [应该放 ToDos 吗？](https://www.osedea.com/en/blog/should-you-put-todos-in-the-source-code)
*   [破窗理论](https://en.wikipedia.org/wiki/Broken_windows_theory)

# 信用

伊登·康斯坦丁诺在 [Unsplash](https://unsplash.com/s/photos/todo) 上拍摄的照片

> 在你完成一个项目的前 90%后，你必须完成剩下的 90%。

迈克尔·阿布拉什

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)