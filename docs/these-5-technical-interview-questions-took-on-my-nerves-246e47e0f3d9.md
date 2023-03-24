# 这 5 个技术面试问题让我紧张

> 原文：<https://levelup.gitconnected.com/these-5-technical-interview-questions-took-on-my-nerves-246e47e0f3d9>

![](img/9c2d93157bf6ef461478d59849e1e147.png)

蒂姆·莫斯霍尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当招聘人员问我技术面试时间表时，我通常会预留一周时间。这么多时间让我可以准备(刷新)以下内容:

*   通用编程面试问题(大多是扎实的原理)
*   特定于平台/工作角色的 API 问题(例如 Angular、Typescript、Swift、Java)
*   关于组织经验的问题——流程、团队合作、工具等等。

然而，任何时间都是不够的。我总是有一些让我思考的问题:

> 这与我想象的相去甚远。

下面是我遇到的一些例子，以及我遇到它们的年份。

# #1:在 C++中，你如何确保只有一个对你的类实例的引用(2001)？

答案是一个**智能指针**。然而，这里重要的因素是时间——那是在 2001 年。我是一个行业新手，几乎没有机会实现单例模式，更不用说内存管理了。

此外，网上也找不到准备面试的资源。

经过相当大的努力，我用 singleton 模式(静态初始化器)解决了这个问题。自然，面试官不太高兴。我得到了一份工作，但不是很令人兴奋。我不得不放弃这个职位。

无论如何，这个问题促使我以一种积极主动的方式来加强我的 OOPS 和 C++。

# #2:使用冒泡排序对数组进行排序(2007):

这个问题让我大吃一惊，因为它是在一次财富 500 强产品公司的面试中被问到的。这个职位是高级开发人员！

因为我已经很紧张了，所以我高估了问题的复杂性。毕竟，这个职位的薪水是我当时薪水的两倍。招聘经理已经知道了。

为什么他们会问这么简单的问题？

我在写我的解决方案的时候汗流浃背(没错，是 F2F，没有白板)。在整个过程中，我一直在想事情没那么简单。或者他们已经决定和另一个候选人在一起，只是和我一起消磨时间。

无论如何，这是我当时用 C++写的东西的粗略翻译的 Java 版本。

```
int[] sort(int list[], int size)
{
    int temp;
    for(int i=0; i<size; i++)
    {
        for(int j=size-1; j>i; j--)
        {
            if(list[j]<list[j-1])
            {
                temp=list[j-1];
                list[j-1]=list[j];
                list[j]=temp;
            }
        }
    }    return list;
}
```

当我完成时，面试官让我用一个输入 5 个未排序整数的例子来逐步解决这个问题。我再次震惊，但无论如何跟着。我还放了一些插图来展示循环的冒泡排序是如何工作的。最后，面试官很开心。

后来，我成功地敲定了这个提议。我加入后，有机会问面试官:为什么要冒泡排序？他的回答出乎意料:

> 我想看到真正的编程成果，而不是排练过的解决方案。

[我在这里描述了](/why-senior-devs-should-not-be-subject-to-whiteboard-interviews-c0de3316a000)整个经历。

# #3:沉睡的理发师问题(2009 年):

这个问题的奇怪之处在于，它是在一轮电话会议中提出的，而不是在白板/F2F 中提出的。然而，我不会有任何不同，因为在这次采访时，我对这种类型的问题毫无准备，尽管这在当时是相当主流的。

下面的问题陈述是[从一个网站](https://www.codingninjas.com/codestudio/library/sleeping-barber-problem)上复制的，但是在电话交谈中，采访者的叙述有很多漏洞。

```
This problem is based on a hypothetical scenario where there is a barbershop with one barber. The barbershop is divided into two rooms, the waiting room, and the workroom. The waiting room has n chairs for waiting customers, and the workroom only has a barber chair.Now, if there is no customer, then the barber sleeps in his own chair(barber chair). Whenever a customer arrives, he has to wake up the barber to get his haircut. If there are multiple customers and the barber is cutting a customer’s hair, then the remaining customers wait in the waiting room with “n” chairs(if there are empty chairs), or they leave if there are no empty chairs in the waiting room.
```

正如我已经说过的，我从来没有想到这是一个线程同步问题，所以我只是浪费时间猜测。

这个问题让我有点鄙视这些公司的面试方法，这些方法涉及的问题与他们正在做的工作相去甚远。然而，尽管如此，它们还是很吸引人，因为它们与现实生活紧密相关。

经过那一轮失败后，我解决了许多需要编程解决的谜题。

其中最著名的是由 IBM 研究部门[推广的](https://research.ibm.com/haifa/ponderthis/challenges/July2002.html)23 囚犯问题。

```
**Ponder This Challenge:**This puzzle has been making the rounds of Hungarian mathematicians’ parties.The warden meets with the 23 prisoners when they arrive. He tells them:You may meet together today and plan a strategy, but after today you will be in isolated cells and have no communication with one another.There is in this prison a “switch room” which contains two light switches, labelled “A” and “B”, each of which can be in the “on” or “off” position. I am not telling you their present positions. The switches are not connected to any appliance. After today, from time to time, whenever I feel so inclined, I will select one prisoner at random and escort him to the “switch room”, and this prisoner will select one of the two switches and reverse its position (e.g. if it was “on”, he will turn it “off”); the prisoner will then be led back to his cell. Nobody else will ever enter the “switch room”.Each prisoner will visit the switch room arbitrarily often. That is, for any N it is true that eventually each of you will visit the switch room at least N times.)At any time, any of you may declare to me: “We have all visited the switch room.” If it is true (each of the 23 prisoners has visited the switch room at least once), then you will all be set free. If it is false (someone has not yet visited the switch room), you will all remain here forever, with no chance of parole.Devise for the prisoners a strategy which will guarantee their release.
```

在知道了[解](https://research.ibm.com/haifa/ponderthis/solutions/July2002.html)之后，很长一段时间里，我希望有采访者会问我这个问题，但它从未发生。

# #4:用你觉得舒服的任何语言写一个散列图(2017)

这在 FAAMG 的在线题库中是相当存在的。然而，我在一个移动应用程序开发职位上被问到这个问题，在这个职位上，人们几乎不需要重写基本算法。

最重要的是，该公司在一个城市里几乎无人知晓。如果我被录用，他们每年只会付给我 36000 美元的薪水。我没有准备好处理如此复杂的算法问题。

我最后一次看到地图的源代码可以追溯到 15 年前。

我只是告诉他们我将如何在一张纸上实现散列图的基本知识(为了澄清事实:他们没有白板)。我的描述并不完美，但它给了我一个关于何时以及如何使用它们的合理想法。

我的第一印象是面试官在故意糊弄我。但事实上，他们很卑鄙。面试后的三个星期，我没有得到任何回应。在我写了一封措辞严厉的后续邮件后，我得到了一句话的拒绝回复。

> “我们找到了另一个候选人。”

整个练习是徒劳的，因为我在他们的编码任务后浪费了时间——这一轮把我推到了我刚才描述的费力的 F2F。

然而，在一天结束时，它让我再一次经历了基础，这是一次相当令人耳目一新的经历。

# #5:编写从 C++到 iOS 和 Android 的跨平台绑定(2020 年):

**对于外行来说:**跨平台绑定使移动 OS 本机(iOS/Android)代码能够使用各自的本机接口调用 C++库，反之亦然。

> 一旦你使用跨平台 C++进行移动开发，你将永远不会想到 React Native 和 Flutter

使用 C++进行跨平台移动开发是一种罕见的现象，尽管这是一种强大的现象。一旦你看到它，你就不能视而不见。换句话说，看过之后，你可能永远不会用 React Native 或 Flutter 来编码。

自然，这个问题是一个大问题，也是编码任务的一部分。它来自 GIS 领域的一家公司。

该解决方案必须涉及某种从 IDL 到本地语言的代码生成(Java 和 Objective C 分别用于 Android 和 iOS)。这种方法是由 Dropbox 开创的[，并在 2014 年 Cppcon 期间宣布。](https://www.youtube.com/watch?v=ZcBtF-JWJhM)

生成的代码(绑定)将是 Objective C++和 JNI，这将使 Objective C 和 Java 能够调用 C++代码。C++代码拥有通用的业务逻辑。

> 敢于对可能需要超过 4-6 个小时的编码任务说不

虽然我非常喜欢做我过去从未做过的工作，但这项运动占据了我整整一周的时间。我也写过单元测试。这是完全不公平的，因为虽然缩小(*只有一个功能！*)，这正是公司已经付钱给员工去研究和实施的工作。我免费做了这一切。

一旦可以为一个功能开发一个界面，类似的方法可以复制到 10000 多个功能，唷——这就是他们的跨平台移动产品 1B 估值！

尽管我很成功，但是面试官对我的 C++代码质量有所保留。我被拒绝了。但这次经历给我上了一堂非常重要的职业课:

敢于[对可能占用超过 4-6 小时时间的编码任务](https://medium.com/swlh/senior-devs-say-no-to-coding-assignments-b66577299b2e#:~:text=Any%20senior%20dev%20can%20say,them%20to%20copy%20and%20release.)说不。

# 结论:

在我看来，这些是最奇怪的技术面试问题，也是我参加面试的时间。

如果你有类似的面试经历，或者对其中任何一个有不同的看法，请在评论中写下来吧！

[**笔磁**](https://tipsnguts.medium.com/) 是广受欢迎的高级开发人员面试电子书的作者，该书解决了亚马逊明星面试格式的问题:

[**高级开发者面试综合方法(40+例题)**](https://tipsnguts.gumroad.com/l/crrzat/zp1vks8) (对于第 **100 名中等读者，**折扣为 **50%** )