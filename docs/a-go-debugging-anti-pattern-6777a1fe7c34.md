# 去调试反模式

> 原文：<https://levelup.gitconnected.com/a-go-debugging-anti-pattern-6777a1fe7c34>

![](img/e7be31ba5a11aa62c81cf00030059ebe.png)

在我工作的地方，单元测试中出现了一种模式，使用一级函数在测试函数中声明局部作用域的函数。我相信它有机地进入了我们的代码，有人用它来避免以封装良好的方式复制一组断言。下面是一个示例:

```
func TestSomething(t *testing.T) {
  validate := func(expectX, expectY int) {
    // some assertions making sure we got the proper X and Y
  } // Do some testing
  validate(1, 2) // Do some more testing
  validate(3, 4)
}
```

一组断言被封装在一个 *validate* 函数中，并在测试的不同时间被调用。我第一次看到这个模式时，它引起了我的注意，因为我通常会这样写:

```
func TestSomething(t *testing.T) {
  // Do some testing
  validate(1, 2) // Do some more testing
  validate(3, 4)
}func validate(expectX, expectY int) {
  // some assertions making sure we got the proper X and Y
}
```

我有点喜欢局部作用域的第一类函数的封装，但是我的直觉告诉我它似乎是关闭的，所以当我把它留在代码中时，我自己继续使用普通函数。

## 我的直觉被证明是正确的

今天，我向其中一个包含一级函数的测试中添加了新的测试用例。一级函数中的一个断言失败。我没有预料到失败，需要进行调查，所以我在断言处设置了一个断点，在调试模式下运行测试，然后……*什么都没有*。调试器完全忽略了断点。套用杰瑞·宋飞的话:

> 它知道如何获取断点，但它只是不知道如何在断点处中断，这是断点最重要的部分，中断。

我将一级函数重构为一个普通函数，瞧，断点成功了。

虽然这似乎是我的 IDE 调试集成中的一个 bug(IntelliJ——参见读者评论),但我可以看到为什么会发生这种情况。使用一个一级函数，我会*假设*(也就是我在猜测)*，*改变了确定代码位置的方式，而调试器没有为此做好准备。

由于我没有看到封装的巨大优势，我声明这是一个反模式，至少对 IntelliJ 来说是这样，并且鼓励人们**而不是**使用它。