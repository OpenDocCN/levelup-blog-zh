# Java 中太多的参数可能意味着细微的重复

> 原文：<https://levelup.gitconnected.com/too-many-parameters-in-java-might-indicate-subtle-duplication-ac1ab5af78ca>

![](img/c0804183ba22a0b2594bfcf347d43b51.png)

斯科特·布雷克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Java 以冗长著称，程序员不用编写带有九个或十个参数的构造函数。从技术上讲，构造函数或其他单元可以接受超过 200 个参数，但是对于日常使用来说，这显然太多了。

拥有这么多参数显然是不好的，尤其是如果它们中的大多数都是同一类型，因为很容易混淆参数的顺序。当然，如果它们都是同一类型的*，你可能应该使用一个数组或集合来传递它们。*

*然而，比严格必要的多一个参数也可能是不好的，并且可能表明您无意中不必要地重复了项目中的一些重要行。*

*在 NetBeans 这样的集成开发环境(IDE)中，您可以进行检查，用八个或更多参数标记项目中的单元。这可能应该重新配置为更低的数字，比如 5。*

*在 Java 版的*构建可维护软件*中，Joost Visser 建议将参数数量保持在不超过四个。*

*同样的指导方针可能适用于几乎所有其他编程语言，比如 C#、Scala 甚至 FORTRAN 和 COBOL。在 Malbolge 这样的语言中，这个问题可能没有实际意义。*

*在我读完 Visser 书中的那一章后不久，我查看了我的一个项目，并做了一些重构来遵守这个指导方针。*

*我有一个带五个参数的异常构造函数，我想了一些方法把它减少到四个。我还提出了一个辅助构造函数，它只接受三个参数，并填充第四个参数。*

*对于本文来说，这将是一个很好的例子，只不过该项目的领域是一个简单但不熟悉的主题。问题域需要是简单的*和*熟悉的东西。*

*上周，在进行另一个简单但更熟悉的主题的编程练习时，我发现了一个不太引人注目的例子，在这个例子中，我将一个 3 参数构造函数减少到只有两个参数。*

*这个练习是一个简单的工资处理程序。即使你从未有过需要“打卡”的工作，你理解这个练习应该没有问题。*

*每个员工都有一个“时间卡”，他们把它放在一个特殊的钟里，笨拙地称为“时间钟”，以标记他们上班和下班的时间，从而描绘出他们期望获得报酬的时间段。*

*例如，在星期一，一名员工在早上 7:59 打卡开始一天的工作，在下午 12:01 打卡去吃午饭。员工吃完午饭回来晚了一点，下午 1:04 打卡，等等。*

*典型的时间卡是为每个持续一周的支付期准备的。一周结束后，已填写的考勤卡将被收集并发送至工资单处理部门，新的考勤卡将在新的一周发放。*

*为了在软件中实现这一点，我们可能需要一个`Employee`类。当然还有一个`TimeCard`类。可能还需要一个类来表示时间段，例如星期一上午 7:59 到下午 12:01 的时间间隔。*

*我的第一份`TimeCard`构造函数草案看起来像这样:*

```
 *public TimeCard(Employee employee, LocalDateTime start,
                    LocalDateTime end) {
        this.cardOwner = employee;
        this.startDateTime = start;
        this.endDateTime = end;
    }*
```

*`start`参数可能是周一上午 12:00，`end`参数可能是周日晚上 11:59*

*`TimeCard`的一个实例将保存一个时间块列表，这些时间块落在卡片的开始和结束位置。如果员工忘记在工资期结束时最后一次打卡，如果他们不打电话询问，工资部门可能会推断出打卡时间。*

*是的，是的，我正在使用`java.time`包中的`LocalDateTime`类。我可能会收到评论说 Java time API 很糟糕，不应该使用它。一个更有成效的评论会解释为什么某些特定的第三方库要好得多。*

*尽管`java.time`有一个明显的缺陷:我找不到任何东西来表示一段时间，比如前面例子中的上午 7:59 到下午 12:01。当然，第三方时间图书馆对此有所帮助。*

*不过我准备为此做一个自己的时间段类，也许我会叫它`TimeBlock`。我提醒你，我这么做是在练习。如果我是为一个付费客户做这件事，我会认为客户不是付钱让我重新发明免费的现有第三方库。*

*这个工资处理的例子也符合 Joost Visser 的另一个准则:“编写一次代码”他给出了一个指导原则，即在一个程序中重复的连续行不能超过六行。*

*我觉得门槛可以降到三四线。一两行太低了，因为像 IntelliJ IDEA 这样的 IDE 会捕捉很多花括号。那将是一件无益的麻烦事。*

*我相信你已经明白为什么“代码复制”是不好的。但是对于程序员和我们用来编辑程序的 ide 来说，重复有时是微妙而难以识别的。*

*这就是我从这个工资处理例子中得到的。我的第一份`TimeBlock`构造函数草案看起来像这样:*

```
 *public TimeBlock(LocalDateTime start, LocalDateTime end) {
        this.startTime = start;
        this.finishTime = end;
    }*
```

*很简单，对吧？它只需要两个参数，从技术上来说，我们只是复制了右花括号。技术上来说。*

*但是如果`end`在`start`之前呢？你典型的物理时间卡时钟就是为了避免这种问题而建立的。员工不可能在打卡上班前打卡下班。目前的`TimeBlock`构造者没有办法阻止这种事情的发生。*

*如果`end`在`start`之前，构造函数应该抛出某种异常，比如可能是`InvalidTimeSpanException`，或者最好是一个带有适当错误消息的老式`IllegalArgumentException`。*

*因为我喜欢做测试驱动的开发，即使是像这样的练习，我在改变构造函数之前写一个测试。测试第一次就失败了，这是应该的。然后我改变构造函数，让它通过测试。*

```
 *public TimeBlock(LocalDateTime start, LocalDateTime end) {
        **if (end.isBefore(start)) {
            String excMsg = "Block can't start (" + start.toString() 
                    + ") before it ends (" + end.toString() + ")";
            throw new IllegalArgumentException(excMsg);
        }**
        this.startTime = start;
        this.finishTime = end;
    }*
```

*然后我继续为我认为`TimeBlock`需要具备的其他特性编写测试，比如布尔函数`contains()`和`overlaps()`。另外，`TimeBlock`可能应该实现`Comparable`接口。*

*我们需要`contains()`来确保某人不能在卡的范围之外的一段时间内打卡上下班，例如上一个支付期的周日下午 6:00 到晚上 10:00。*

*至于`overlaps()`，我认为它将有助于提供一种使用图形用户界面编辑时间段的方法。我还没有充实项目的这一部分，现在都在命令行上。*

*然后我继续工作`TimeCard`，最终整个项目通过了所有的测试，看起来一切正常。但是第二天，我注意到`TimeCardTest`和`TimeBlockTest`都包含了当`end`参数在`start`参数之前时的测试。*

*我喜欢使用 Try-Catch-Fail 测试异常，但是为了使本文简短，我将使用 JUnit 4 预期异常属性。因此，从`TimeCardTest`开始，我有:*

```
 *@Test(expected = IllegalArgumentException.class)
    public void testBadConstructorParams() {
        TimeCard **badCard** = new TimeCard(TEST_PERIOD_END, 
                    TEST_PERIOD_START);
    }*
```

*来自`TimeBlockTest`:*

```
 *@Test(expected = IllegalArgumentException.class)
    public void testBadConstructorParams() {
        TimeBlock **badBlock** = new TimeBlock(TEST_PERIOD_END, 
                    TEST_PERIOD_START);
    }*
```

*这两个测试都通过了，当然，我让它们在测试中以完全相同的方式通过了相应的类。*

*很明显，这是重复，无论是在构造函数中还是在测试中，静态检查工具应该足够聪明来捕捉它…除了它低于六行的阈值。*

*然后我终于意识到:如果`TimeCard`构造函数只接受一个`TimeBlock`类型的参数，而不是接受一个`start`参数和一个`end`类型的`LocalDateTime`参数，会怎么样？*

*不再是三个参数，`TimeCard`构造函数现在只接受两个参数:类型为`Employee`的参数和时间段参数。*

*这将需要一些重构，但是，感谢您的 IDE，它应该只需要您几分钟的时间来更改需要更改的内容。*

*所以现在`TimeCardTest`中的坏参数测试变得完全没有必要了。如果试图用无效的开始和结束时间构造一个`TimeBlock`实例导致异常，那么就不可能用无效的开始和结束时间构造一个`TimeCard`实例。*

*在这个例子中，注意到在构造函数和它们相应的测试中有一个微妙的重复情况，这使我们减少了一个构造函数参数列表，在这个微妙的情况下，有一个以上的参数是绝对必要的。*

*此外，我认为它显示了面向对象编程(OOP)范式的价值。有时感觉 OOP 范例迫使我们毫无意义地创建几十或几百个类。*

*治疗这种感觉的方法是试着想象一个简单的练习，比如工资处理程序只使用原语。您可能实际上接近构造函数和其他单元的 255 个参数的技术限制。*