# 低效程序员的 3 个习惯

> 原文：<https://levelup.gitconnected.com/3-habits-of-unproductive-programmers-98dab92d3f51>

![](img/8946788828f1da8a7925596eeda1a416.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Manja Vitolic](https://unsplash.com/@madhatterzone?utm_source=medium&utm_medium=referral) 拍摄的照片

在我的技术生涯中，我很少见到纯粹的程序员**。少数人转变成了更好的自己。**

**我的其他同事转变成了管理角色。但他们中只有少数人有管理野心。其余的人都不擅长编码。**

**低效是一种慢性毒药。这造成了恶性循环。它不会杀了你。相反，它会慢慢地把你变成一条毒蛇，而你并没有意识到这种转变。**

**唯一能让你的非生产性职业持续下去的方法就是升职。**

**以下是我观察到的低效程序员的三个习惯。**

**(就这一点而言:我也有一些，但我很幸运得到了指导，也有足够的决心去做。)**

# **#1:他们不记录:**

**这里我不是在谈论规范、文档或代码注释。**

**所有没有效率的程序员都避免写他们在一天/一周/冲刺中做了什么。这一点在他们的每周工作报告(时间表)、年度绩效评估、个人简历，甚至他们自己的日志中都很明显。**

**如果你不能记住、分析和反思你在时间中有效地做了什么，你将如何改善自己？**

**如果你不愿意思考你正在为你的组织创造的价值，你就不能继续交付，更不用说成长了。**

**如果你不擅长写作，那就多尝试一下。写工作日志。试着用至少三句话来描述你的日常工作。一年后，当你决定修改你的简历时，你将拥有你能找到的最好的版本。**

**![](img/e2e95a0c605e61ad2f38c1a9ddc55136.png)**

**照片由[蒂姆·莫斯霍尔德](https://unsplash.com/@timmossholder?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄**

**最初，不要担心质量或内容。你会好起来的。毕竟，编码是你的主要技能，也是你大脑燃料的来源。**

**然而，除了燃料，发动机也需要油来保持润滑。如果它没有得到它，它会在正常年龄之前就磨损。**

# **#2:它们不分离:**

**这一条似乎与上面的第一条截然不同。我们已经进入了技术领域。**

**但实际上，我们没有。因为就像写作一样，解耦是反思的另一个结果。**

**在写作中，你用语言描述你的作品。在解耦中，你提炼它。**

**我仍然记得我写这段代码的那一天(这是很久以前的一个不准确和过于简单的例子):**

```
function updateUI(model: UIModel) {
    if (condition1) {
       textField.text = model.text1
       textField.font = Font(size: 16, weight: Font::Bold)    
    } else {
        textField.text = model.text2
    }
}
```

**经过手动单元测试，它按照要求工作:【condition1 的更大字体。**

**我仍然记得那个晚上。一喊“**耶！**”，同事抓住我的肩膀，把我带到最近的咖啡机旁，我们在那里聊了一个小时。我觉得这是理所应当的，因为前一天我额外花了 4 个小时让它工作。**

**我们发布了这个功能，每个人都很高兴。**

**两周后，T4 来了，需要另一个字体版本。我高兴地再次交付(匆忙中，我确保自己是团队中最快的票据解析者):**

```
function updateUI(model: UIModel) {
    if (condition1) {
       textField.text = model.text1
       textField.font = Font(size: 16, weight: Font.Bold)    
    } else if (condition2) {
       textField.text = model.text1
       textField.font = Font(size: 16, weight: Font.Medium)        
    } else {
       textField.text = model.text2
    }
}
```

**然而，一天后，客户抱怨了——在**条件 2** 背后的想法是在**文本字段**中有更多的数据。我不得不将一些常量字符串附加到 **model.text1** 中。我很快就修好了，送到了。**

```
function updateUI(model: UIModel) {
    if (condition1) {
       textField.text = model.text1
       textField.font = Font(size: 16, weight: Font.Bold)    
    } else if (condition2) {
       textField.text = model.text1 + Constant
       textField.font = Font(size: 16, weight: Font.Medium)        
    } else {
       textField.text = model.text2
    }
}
```

**不久之后，条件逻辑的切换进入了用户的领域:**

*   **引入了一个新按钮。**
*   **只需轻轻一点，用户就可以在**条件 1**->条件 2 之间切换。**

**没有人费心去更新 updateUI()函数，尽管它是通过单击按钮来调用的。**

**第二天早上，一个 bug 在我们的队列中等待:在没有**条件 1** 或**条件 2** 的情况下，字体显得更大了！**

**我调试了一下，在我幼稚的热情中，冲到我的资深开发者的椅子上:“在于最后的 else。我会在 5 分钟内修好它！我太傻了，忘了最后一个！”(虽然我不是指最后一行)**

**学长面无表情的看着我。然后，他来到我的办公桌前，要求看 updateUI()函数。我不情愿地给他看。**

**他一句话也没说，立刻就把它重构了:**

```
function updateText(model: UIModel) {
    if (condition1) {
       textField.text = model.text1      
    } else if (condition2) {
       textField.text = model.text1 + Constant
    } else {
       textField.text = model.text2
    }
}function updateFormat() {
    if (condition1) {
       textField.font = Font(size: 16, weight: Font.Bold)    
    } else if (condition2) {
       textField.font = Font(size: 16, weight: Font.Medium)        
    } else {
       textField.text = Font(size: 16, weight: Font.Default)
    }
}
```

**他在屏幕加载和按钮点击时调用了这两个函数。我告诉他默认字体在 UI xml 文件里(不需要设置默认！)，但他没有理会。**

**很快，当他写了两个单元测试分别测试 **updateText()** 和 **updateFormat()** 时，我明白了为什么。**

> **永远不要将混合意图的代码放在一个函数中。**

**当他们经过时，他看着我。我从来没有像那天那样感到自己如此无用。他用力拍了拍我的肩膀，然后离开了。**

**正如我所反映的:我的错误在于我忘记了设置默认字体。但阻止我看到它的是呈现和格式的混合逻辑。**

**在那次事件之后，我再也没有在一个函数中留下混合意图的代码。**

**解耦使你能够避免你目前看不到的错误。花五分钟去耦合(也就是根据意图分离代码)可以为你节省几个小时(或者几天，随着复杂性的增加)调试错误和测试失败的时间。**

**缺乏解耦会让你远离一个高效的程序员-**

*   **高效的调试器**
*   **对于一个没有生产力的测试者**
*   **对一个没有生产力的团队成员**

**意大利面代码的邪恶之美确保了它像慢性毒药一样工作。**

**你永远不会意识到你在变坏。当你这样做的时候，已经太晚了，除非你已经记下了你一天中做得最好的事情。**

# **#3:他们不创造捷径:**

**聪明的用户创建 UI 快捷方式。聪明的开发者创造开发捷径。**

**很长一段时间，我没有创建任何快捷方式(别名，在 Unix 世界里)。我觉得他们很愚蠢。毕竟，我们所做的只是给一个键赋值并永久保存它。**

**考虑一下这个:**

```
alias my_shortcut = my_long_long_commandExample:
alias cdtomyfavoritefolder = cd myfavoritefolder
```

**(此后，用户还必须确保每次切换用户时设置别名定义。根据所使用的 shell 和 Unix/Linux 版本，这可以通过多种方式实现，但这在这里并不重要。)**

## **在未来的某个地方，这曾经发生过:**

*   **我创造的捷径是什么？**
*   **谷歌的实际需求和复制它**

```
> my_long_long_command //to hell with the shortcut
```

**原因？我的日常工作主要是通过基于 UI 的客户端完成的，我很少需要终端来运行任何脚本，更不用说创建它们了。所以我太急于重用我过去的明智的工作。**

**直到我大量接触 Linux 服务器设置(我的自愿项目)，我才意识到 [Unix 别名](https://en.wikipedia.org/wiki/Alias_(command)) ( [shell 链接](https://superuser.com/a/392082)，在 Windows 世界中)的强大。就是在那个时候，我意识到了自己的错误。**

**我的理想步骤应该是:**

*   **创建一个易于记忆的别名**

```
$ alias favoritedir='cd myfavoritefolder'
```

*   **围绕着它们创造很多:cp、mv、ls、echo、ssh、git——你能想到的。**
*   **谷歌如何看到它们，并在我的便利贴上涂鸦/输入:**

```
$ alias
favoritedir = cd myfavoritefolder
```

**如果这仍然是愚蠢的(根据我自己的定义)，那么你可以这样做:你也可以使用 shell 函数向它们传递参数，就像这样:**

```
$ alias messagewithargs='f(){ I took the argument: "$@"; unset -f f; };f'
$ messagewithargs "Hello"
I took the argument: Hello
```

**现在，如果这些是你的小步骤，你的工作会不会很惊人？**

**我到底在暗示什么？**

# **结论:**

> **从心中的目标开始**
> 
> **F.柯维(作者，《高效人士的 7 个习惯》)**

**所以以下是你改正坏习惯的顺序:**

> **#3 -> #2 -> #1**

**使用别名节省下来的时间，你会发现更多的时间来解耦你的意大利面条代码。更少的错误，通过测试，绿灯，快乐的客户。**

**自然地，当达到这种状态时，你也会想用日志来回顾你富有成效的旅程，经常在你的朋友中吹嘘你作为一个程序员是多么神奇。沿着这条路，有人也会与你合作，纠正你的错误，用漂亮的砖块来补充你伟大的基础。**

**这就是你如何走出低效的恶性循环，并开始规划你的指数编程轨迹。**