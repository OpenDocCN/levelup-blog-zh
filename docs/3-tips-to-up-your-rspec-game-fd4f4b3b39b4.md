# 提升 RSpec 游戏的 3 个技巧

> 原文：<https://levelup.gitconnected.com/3-tips-to-up-your-rspec-game-fd4f4b3b39b4>

## 测试，烦人但有用！

在日常工作中，每个开发人员都应该被要求为他们的工作编写测试，如果没有人被要求，他应该去要求。虽然它们写起来很麻烦，很烦人，而且通常是事后才想到的，但是在重构代码、升级框架或者将它们作为特性文档的一种形式时，它们确实非常有用。

![](img/e4b00a02250fd0ad7e3e8e3d1e2dfb9c.png)

由[卡里·谢伊](https://unsplash.com/@karishea?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这里有一些帮助我写出更清晰、更有结构、整体更好的规范的技巧。实施这些技巧也将简化你的工作流程，并给你一个关于如何进行测试的方向和更好的想法。

# 构建代码

如果我们接受测试将作为我们代码的文档这一前提，那么编写描述性测试并使用框架为我们提供的**描述、上下文、信息技术**是很重要的。

这里有一个开源项目 ChatWoot 的例子，值得推荐。

在这个例子中，很容易理解我们正在测试什么，以及在给定的上下文中它应该如何表现。因此，现在当其他人试图做这项工作时，他可以阅读规范并理解给定端点的目的，而不是阅读代码。

# 使用数据工厂

D ata 工厂(或简称为*工厂*)是一个蓝图，它允许我们用预定义的值集创建一个对象或对象集合。虽然没有它们你也可以工作，但你绝对应该使用它们。工厂会让你的代码变得枯燥，让你的工作流程变得更快更简单。

我个人使用 [FactoryBot](https://github.com/thoughtbot/factory_bot_rails) ，它很容易设置，几乎没有学习曲线，你需要的一切都有据可查，易于实施。

让我们看一个简单的例子，考虑这个模型:

我们可以创建一个工厂，为一个对象创建所有必要的数据:

通过阅读这个要点，我们可以得出结论，下一次我们想要创建一个 article 对象，它是由用户自动创建的，我们只需调用:

```
create :article, :published
```

从这一行开始，我们将创建一个新的文章，以及带有名称和状态属性的用户对象，显然，如果我们为它们传递不同的值，这些属性可以被覆盖，这显然比手动创建多个对象好得多。

但这并不是 FactoryBot 必须提供的全部，它为您可能遇到的不同场景构建了各种有用的回调。所有这些特性都是为了让你的生活更简单，你的代码更干净，这绝对值得一看，[入门](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md)。

# 追踪您的 Cove 覆盖范围

知道你是否已经很好地测试了你的代码的最好方法是用一个专门设计的工具来跟踪你的覆盖率。注意，检查你的代码是否被覆盖并不等同于检查你的测试是否有效。

用测试覆盖你的代码库是非常有用的，这是一个健康的项目和负责任的开发者的标志。

为了检查我的代码覆盖率，我使用了 [SimpleCov](https://github.com/simplecov-ruby/simplecov) ，它设置起来很简单，并且给了你大量的数据来逐行检查你的覆盖率。

设置很简单，将 simplecov 添加到您的 gemfile 中，并在您的`*spec_helper.rb*` *或* `*rails_helper*` *的顶部添加:*

```
require 'simplecov'
SimpleCov.start :rails
```

设置完成后，你的规格完成运行，你会得到生成一个 HTML 文件，非常容易理解和阅读。

# 结论

不管你喜不喜欢，测试你的代码是每个开发人员一天中不可或缺的一部分，一旦你接受了编写测试，并开始将它们视为开发过程中的一个重要部分，那么事后的生活将会变得更加容易。

这些工具帮助我提高了我的 Rspec 游戏，我不得不艰难地学习它，从一开始我不注意我的覆盖范围一直到现在我不得不回去覆盖错过的行。

[](https://rspec.info/) [## r spec:Ruby 的行为驱动开发

### Ruby 的行为驱动开发。让 TDD 变得高效和有趣。首先，安装 RSpec 并运行 rspec…

rspec.info](https://rspec.info/) [](https://github.com/simplecov-ruby/simplecov) [## simplecov-ruby/simplecov

### SimpleCov 是一个用于 Ruby 的代码覆盖分析工具。它使用 Ruby 内置的覆盖率库来收集代码覆盖率…

github.com](https://github.com/simplecov-ruby/simplecov) [](https://github.com/thoughtbot/factory_bot_rails) [## 思维机器人/工厂机器人轨道

### factory_bot 是 fixtures 的替代品，具有简单的定义语法，支持多种构建策略…

github.com](https://github.com/thoughtbot/factory_bot_rails)