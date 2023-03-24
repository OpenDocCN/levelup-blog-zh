# 正在测试。NET Core 与 xUnit 和 NSubstitute

> 原文：<https://levelup.gitconnected.com/testing-in-net-core-with-xunit-and-nsubstitute-a145c7b928c6>

![](img/8c03cced09e1d287182aa7c0a3c1b6d0.png)

照片由[德米特里·拉图什尼](https://unsplash.com/@ratushny?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 安装要求

从 nuget 获取:

*   xunit
*   xunit.runner.visualstudio
*   不可替代

1.  在名为`<Project>.Test`的解决方案中创建新的 xUnit 项目。该项目将为您创建一个名为“UnitTest1.cs”的文件。我们将使用这个文件来测试嘲讽。
2.  右键单击 Dependencies > Add Reference >选择您想要测试的实际项目
3.  我想模仿注入到我的服务中的存储库。这非常容易做到:

**第 9 行:**我创建了一个名为`mockRepository`的替代存储库，它实现了 IRepository。

**第 10 行:**我说调用方法`GetString()`将返回字符串“样本”。

**第 11 行:**我创建了我想要测试的服务，并注入了我新的模拟存储库。

**第 12 行:**断言`skillService.GetString()`返回“样本”。

这需要 5 分钟来设置和运行，所以创建测试根本不需要很长时间。