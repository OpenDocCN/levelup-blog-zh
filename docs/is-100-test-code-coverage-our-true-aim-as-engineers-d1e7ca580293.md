# 100%的测试代码覆盖率是我们作为工程师的真正目标吗？

> 原文：<https://levelup.gitconnected.com/is-100-test-code-coverage-our-true-aim-as-engineers-d1e7ca580293>

单元测试代码覆盖率:软件行业中每个人都在追求的黄金百分比。但是为什么它如此重要呢？

有些人认为覆盖率越高，越接近 **100%** ，代码就越无 bug、可维护、灵活重构。

但是，这是所有代码都不容易出错的基本标准吗？像生活中的大多数答案一样，这是一个很难的*也许是*。

首先，我们需要理解什么是单元测试覆盖率:*一种度量，它告诉我们单元测试运行的代码行数除以应用程序中的代码总数*。

假设我们有这个方法(顺便说一下，它不是一个[纯函数](/pure-functions-in-software-development-d315a4520da1)):

```
1 int retrieveCurrentYear() {
2   final nowDate = DateTime.now();
3   return nowDate.year;
4 }
```

像这样对它运行单元测试:

```
expect(retrieveCurrentYear(), 2022); // 2022 is the time of writing this article
```

这将导致 **100%的代码覆盖率**，因为所有四行代码都在运行单元测试时被执行。

![](img/c09eee904f90ddb807c6066324ba1b8e.png)

布鲁斯·马尔斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 我们成功了！我们在世界之巅…不到一年👏

假设我们明年(2023 年)运行这个单元测试，**它会失败**，即使我们的代码覆盖率是 100%😥

# 那么我的观点是什么？

代码覆盖率是必不可少的，它应该相对尽可能高，但追求尽可能高的百分比和尽可能少的测试来“通过”那些代码行不应该是我们的目标(就像不恰当地使用测试的金字塔，不是严重依赖单元测试，而是依赖快照测试、集成测试或其他方法)。

相反，我们应该看到什么对我们的团队和我们有意义；也许 70%-80%就足够给我们前进的信心了。

对于其他团队，更低或更高的值可能更有意义。最重要的部分是要达到 100%的确定性，一旦一行代码被覆盖，我们在将来不会有任何意外/副作用:**质量对数量**(如何进行单元测试的完整例子可以在这个故事中找到[)。](https://medium.com/@catalin.patrascu/what-is-unit-testing-and-why-we-should-use-it-2cf8ed65eca7)

感谢你花时间阅读以上所有内容，我希望这个故事能帮助你提高一点点水平。此外，请不要犹豫将知识传播给其他开发人员。

[](https://medium.com/@catalin.patrascu/membership) [## 用我的推荐链接加入媒体- Catalin Patrascu

### 阅读 Catalin Patrascu 的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接…

medium.com](https://medium.com/@catalin.patrascu/membership)