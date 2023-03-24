# 是什么让一个杰出的程序员不同于普通的程序员

> 原文：<https://levelup.gitconnected.com/what-sets-an-exceptional-programmer-apart-from-an-ordinary-programmer-73d3fce24e21>

![](img/0f38dc2b6603f2e18569e8af596c81ca.png)

沃齐米日·贾沃斯基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

编程是一门你可以随着时间掌握的学科。

一般来说，你花在代码上的时间(不一定是在显示器前)越多，你就会成为越好的程序员。

这就是我个人不喜欢*例外*和*普通*这样的标签的原因。

也就是说，这个问题总是困扰着面试官，因为他们被迫进行比较。面试候选人还会经历巨大的压力，如何比别人更好地展示自己，而不是仅仅表现得好——这才是工作真正需要的。

杰出程序员与普通程序员的区别在于面试/互动过程中展现的一系列想法。这些想法在最伟大的编程书籍中有详细的描述。

除了这些想法，杰出的程序员还表现出某些行为症状，这些症状是通过行业经验或良好的协作实践获得的。这些行为症状可以从他们的习惯中看出来，面试官希望在面试中看到它们。

在这里，我们的目标是通过卓越的本质。我们开始吧。

# #1:范围细化:

一位智者曾经说过:问正确的问题是 50%的解决方案。

当遇到代码问题时，优秀的程序员会开始解决问题。杰出的程序员首先尝试定义它。

这是因为优秀的程序员觉得他们知道的已经够多了。他们禁不住诱惑，表现出"*我知道你想解决的那种问题。这就是为什么你应该雇用我。*“我在我的故事[中描述了这种特质，这是最不被看好的高级开发人员面试技巧](https://betterprogramming.pub/the-most-underrated-technique-to-nail-senior-developer-interviews-f917025453b7)

另一方面，优秀的程序员开始展示这是如何做到的。

他们开始保持安静，倾听。他们试图思考所面临挑战的整体背景。他们已经知道如何编码解决方案。但即使他们不知道，他们也知道如何隔离复杂的部分，并使其与整体解决方案相比显得微不足道。

它们隐含地表明编码是解决方案的唯一(通常是最后)部分。

> 通过细化范围，一个优秀的程序员会驱使面试官走向他/她想要的解决方案。

当面试官问"*给定一组城镇居民，如何计算选民的平均年龄？*”，一个好的程序员会很快写出下面的解决方案:

```
function calculateAge(votersArray: [Citizen]) {
   if (votersArray.count < 1) { 
      return 0
   } sum = 0
   for person in votersArray {
      sum =+ voter.age
   } average = sum / votersArray.count
   return average
}
```

另一方面，一个杰出的程序员会问以下问题:

*   最低投票年龄是多少？(记住这是一个**公民**阵列，而不是现成的选民名单！)
*   是否有一个数据项来表示一个选民是否是选举机构的注册选民？

在这一点上，大多数面试官都没有给出具体的答案。他们宁愿让候选人自由地以他们能做到的最好的方式去实现它。(尽管他们在心里为提问分配了一些分数，为正确的提问分配了更多分数)

杰出的程序员会这样编写他/她的解决方案(该解决方案不遵循任何编程语言):

```
class Citizen {
    var age: Int
    var isRegistered: Bool
}function calculateAge(citizensArray: [Citizen], filterFunction: (Voter) -> Bool) {
   if (citizensArray.count < 1) {
     logMessage("Citizen list is empty!")    
     return 0
   }var votersCount = 0 //tracks only the eligible voters
for citizen in citizensArray {
      if (citizen.age >= MINIMUM_VOTER_AGE && citizen.isRegistered == true)
        sum =+ sum + citizen.age 
        votersCount += 1
      }   
   } average = votersCount > 0 ? (sum / votersCount) : 0
   return average
}**// Usage:**citizen1 = Citizen(age: 38, isRegistered: true)
citizen2 = Citizen(age: 30, isRegistered: true)
citizen3 = Citizen(age: 28, isRegistered: false)citizens = [citizen1, citizen2, citizen3]//finds average age of voters - returns 34
let average = calculateAge(citizensArray: citizens)
```

注意到细化范围带来的不同了吗？通过这样做，杰出的程序员-

*   展示他/她的对象设计技能，测试数据的抽象性和内聚性(公民年龄、注册等)。
*   不仅处理边界条件，而且有助于定义边界条件(15000 名注册选民对 89000 名公民)。通过细化范围，他/她帮助(并有效地推动)面试者找到他/她想要的解决方案。
*   报告错误。这里，通过日志记录来报告空的公民列表，但是异常也可以是报告它们的另一种方式。
*   通过在“使用”部分包含客户电话示例，展示他/她的解决方案的优势。

现在，如果你回顾一下优秀程序员的解决方案，你会发现它是对面试官讲述的问题的正确解决方案。

但是它似乎植根于在线编码测试环境，在那里检查边界条件被认为是完美编程的最终目标。这导致人们限制了问题的范围。

一个优秀的程序员将问题领域扩展到了编码之外，这样做的结果是创造出更多可用的代码，这些代码可以更容易地部署到生产中。

# #2:干净的代码:

范围细化导致人们解决了 50%的问题。干净的代码完成剩下的工作。

干净的代码不是一种特质，而是一种状态，是通过对同一段已经运行良好的代码进行数小时的工作而达到的。换句话说，它是无数小时重构的结果，是在对坚实的原则有清晰了解的情况下完成的。

> 干净的代码不是一种特质，而是一种通过数小时的重构才能达到的状态

如果你是那种从 StackOverflow 复制解决方案并且不经过至少 3 次迭代就发布的人，你可能在这方面有很多工作要做。我知道，因为我就是其中之一，并且在我作为高级开发人员的职业生涯中为此付出了巨大的代价。

我们用一个例子来理解这个。在一个典型的编码任务中，面试官会询问如何从 API 获取数据，并在 UI 中显示数据。

这通常是通过将 API 数据( ***api/restaurants*** )转换成域对象( **Restaurant** 数组)，并编写依赖于该域对象的 UI 代码来完成的。

一个在从物联网到流媒体等领域工作过的优秀程序员仍然有可能编写出这样的代码:

```
var restaurantArray = Array<Restaurant>()
if JSONDeSerializer().decode(httpResponse) != Error {
   restaurantArray = JSONDeSerializer().decode(httpResponse)
} else if XMLDeSerializer().decode(httpResponse) != Error {
   restaurantArray = XMLDeSerializer().decode(httpResponse)
}
```

问题？这段代码不仅对响应进行了两次解码(首先是在检查中，然后是在实际的转换中)，而且很快就可能变成意大利面条。如果除了 XML 和 JSON 之外还有更多的去序列化格式呢？

> If-else 是穷人的多态。

重申一下，问题不在于转换本身，而在于模式。如果您现在有一个处理检查 4 个字段的业务逻辑，如果您用上面显示的 **if-else 方法**来编码，它将容纳 10 个以上字段的可能性有多大？

有人把 if-else 恰当地称为穷人的多态性。

杰出的程序员已经意识到了这一点，并设计了类似这样的解决方案:

```
class APIDownloader {
   APIDownloader(deserializer: Deserializer) {
      this.deserializer = deserializer
   } function downloadData() {
      //downloading logic
      deserializer.decode(httpResponse)
   }
}
```

什么是 **Deserialiser** ？它是定义 **decode(HttpResponse)** 函数的接口/协议。它必须由 **JSONDeSerializer** 和 **XMLDeSerializer** 类实现/符合，这两个类提供了 **decode** ()函数的具体实现(大部分是开源的)

deserialiser 从哪里来？从外部 **APIDownloader** 。APIDownloader 并不负责决定它所处理的 HTTP 响应格式。其他一些类创建了一个类型为**反序列化器**的对象(可能是类 **JSONDeSerializer** 或 **XMLDeSerializer** 的实例)，并将它传递给 APIDownloader。

> 策略是将 if/else 推到您正在编码的类之外，最终用 switch/case 替换它

那个*其他类*可能是一个 **APIClient** 类，它甚至在发出 HTTP 请求之前就知道 HTTP 响应将是哪种格式。或者，它可以是对 **APIDownloader、**的单元测试，这使得 APIDownloader 的任务非常明确:只需下载数据，并将 XML/JSON 域对象返回给我。

如果你再想一次，这整个练习的目的是把 if-else 推到你正在编码的类的范围之外。APIDownloader 将 if-else 委托给**API client**/单元测试，以此类推**。当绝对有必要使用它时，可以用**开关盒**代替它，这使得编译器的工作相当容易(当然，取决于编程语言)**

在编程术语中，这种技术被称为依赖注入，它是干净代码的基石之一。

之所以如此，是因为它自动实施了干净代码的基本原则:

*   类设计和关注点分离(也称为实体中的 S)
*   单层抽象(APIDownloader 只处理下载，不处理转换 **)**
*   多态(**deserializer**抽象接口 **)**
*   测试驱动开发

如果你只知道如何使用依赖注入技术，你会更频繁地开始自我修正和重构你的代码。是的，它有它的局限性——特别是当你最终将 N 个参数注入到你的类中时，这时你应该简单地通过合并它们来设计一个单独的类。

但一般来说，当你开始使用 DI 时，它会很容易成为一种习惯。在你意识到之前，你会变成一个与众不同的你。

# #3:构建功能，而不是特性:

如果你重温我们在#1 中看到的例子，你会发现杰出程序员的方法中有一个明显的缺点。在演示范围细化功能时，他/她最终向 **Citizen** 类添加了两个字段( **isRegistered** 和 **age** )。

虽然这些是至关重要的细节，但它们使解决方案承载了一组额外的 *if* 条件(**citizen . AGE>= MINIMUM _ VOTER _ AGE&&citizen . is registered = = true)。**

可以添加到 **Citizen** 类中的属性有一个无穷无尽的列表。但是，就编码/展示解决方案所花费的时间而言，它能展示候选人的更多能力吗？一点也不。

面试官总是会发现这一点，尽管当他们拒绝这样的候选人时，他们只会说“*候选人最后做了超出范围的事情。*

他们可能会忽略报告(虽然这是一个事实)是这个声明:

> 没有展示其他能力。

优秀的程序员可以实现没有 bug 的特性，就像需求中描述的那样。当他们多走了一英里，他们的**复杂度与结果**图是线性的。在实现更多的过程中，他们最终使代码变得更加复杂。

优秀的程序员会实现有或没有错误的特性。但是这样做的话，他们会把代码留给有额外能力的继任者。这些能力确保了任何数量的属性或功能的增加都可以用最少的额外改动来处理。

优秀程序员的**复杂度与结果的**图会比线性图更好——可能是指数图。换句话说，他们将以更少的额外复杂性获得更好的结果。

在范围细化过程中，一个优秀的程序员可能还会问:

> 我们感兴趣的是整体平均年龄，还是某一特定选民群体的平均年龄(如右翼选民、女性选民、受过教育的选民等)。)

同样，面试官不会提供任何具体的东西，但他们会观察到更进一步的热情。然而，更重要的是，他们将有兴趣看到候选人如何完成*。*

*上句中粗体+斜体的 ***it*** 部分代表验证/评估任何数量的额外公民属性的能力，不会因不必要的 if/else 条件而增加代码，符合上面的#2。*

```
***type CitizenEvaluator =** **(Citizen) -> Bool**function calculateAge(citizensArray: [Citizen], **filterFunction**: **CitizenEvaluator**) {
   if (citizensArray.count < 1) {
     logMessage("Citizen list is empty!")    
     return 0
   } var eligibleVotersCount = 0 //tracks only the eligible voters
   for citizen in citizensArray {
      if (fliterFunction(citizen) == true)
        sum =+ sum + citizen.age
        eligibleVotersCount += 1
     }   
   } average = eligibleVotersCount > 0 ? (sum / eligibleVotersCount) : 0
   return average
}**// Usage:** ageEligibleFilterFunction = function(citizen) {
   return citizen.age >= MINIMUM_VOTER_AGE
}registrationFilterFunction = function(citizen) { 
   return citizen.isRegistered == true
}function maleVoterFilterFunction = function(citizen) { 
    return ageEligibleFilterFunction(citizen) && registrationFilterFunction(citizen) && citizen.gender == MALE
}femaleVoterGraduateFilterFunction = function(citizen) {
    return ageEligibleFilterFunction(citizen) && registrationFilterFunction(citizen) && (citizen.gender == FEMALE && citizen.education > GRADUATE)
}citizen1 = Citizen(age: 34, isRegistered: true, gender: MALE, education: GRADUATE)
citizen2 = Citizen(age: 23, isRegistered: true, gender: FEMALE, education: POSTGRADUATE)
citizen3 = Citizen(age: 9, isRegistered: false, gender: FEMALE, education: STUDY)
citizen4 = Citizen(age: 28, isRegistered: false, gender: FEMALE, education: GRADUATE)citizens = [citizen1, citizen2, citizen3, citizen4]//finds the average age of male voters
let maleAverage = calculateAge(citizensArray: citizens, filterFunction: **maleFilterFunction**)//finds the average age of female, post-graduate voters
let femalePGAverage = calculateAge(citizensArray: citizens, filterFunction: **femaleGraduateFilterFunction**)*
```

*注意 **CitizenEvaluator 类型，**，它是一个函数的类型别名，该函数接受 Citizen 参数，并基于 Citizen 对象的各种属性返回布尔值。它作为一个参数被添加到 **calculateAge()** 函数中，从而强制其所有客户端自己提供合适的 if 条件，从而使 **calculateAge()** 摆脱任何 **if-else** 的疯狂。*

***ageEligibleFilterFunction**和 **registrationFilterFunction 都属于**公民评估者类型**。****maleVoterFilterFunction**和**femaleVoterFilterFunction**也是 citizen evaluator****、**类型，但它们另外依赖 ageEligibleFilterFunction 和 registrationFilterFunction——与对象组合设计模式不同，其中一个对象可以包含其他嵌套对象。***

***如您所见，使用上面显示的功能块方法，可以在彼此之上构建高度定制的强大功能链。***

***使用这种方法，优秀的开发人员可以展示几个特点:***

*   *****交流中的抽象:**在提问时，优秀的程序员不会讨论预期解决方案的技术方面(例如，*我应该使用函数式编程吗？*)。他/她只是要求使用与功能相关的术语。只有当他/她要讨论代码问题/质量时，才有必要在代码级别上交谈。这种能力被称为在正确的抽象层次上工作，它在真正的程序员生活中是非常必要的。***
*   *****功能理解**:该解决方案不仅显示了使用最少的行可以实现多少功能，还显示了一个简单的统计平均值可以揭示多少关于数据集合的细节(选民教育、选民性别等)。).***
*   ***简单性:普通程序员可以以线性方式添加细节，但不会增加优秀程序员开发的功能的复杂性。在某种程度上，优秀的程序员能够为产品添加无限多的特性，而不需要他/她自己去添加。即使是一般的程序员也可以使用这些代码来构建具有增量特性集的产品。***
*   *****简洁:**公民属性列表可以是无穷无尽的，但两项(性别和教育)足以证明他/她可以在正确的背景下处理问题，甚至在试图解决问题之前。***

***当面试官遇到如此优秀的候选人时，他们甚至不太可能考虑其他候选人，除非他们担心自己会被更好的替代者抹杀。***

# ***结论:***

***实现功能是面试中的首要要求。这一结论在很大程度上受到了编码面试学校的启发，这些学校通过分离和分类他们应该解决的问题来训练候选人改善他们的陈述，以安慰面试官。***

***虽然这是一个可以接受的真相，但它不是完全的真相。在招聘技术含量高的职位时，面试官在寻找他们提问范围之外的东西。他们希望有人能补充他们的想法，从而指导解决过程。***

***有人可以成为达芬奇不朽名言的活纪念碑:***

> ***简单是最复杂的。***

***掌握这三个强大的属性使一个人成为杰出的程序员，而不仅仅是杰出的候选人。那么，面试中的成功就成了副产品，而不是人生的目标。***

***[**笔磁铁**](https://tipsnguts.medium.com/) 是广受欢迎的高级开发人员面试电子书的作者，该书解决了亚马逊明星面试格式的问题:***

***[**高级开发人员面试综合方法(40+例题)**](https://tipsnguts.gumroad.com/l/crrzat/zp1vks8) (对于第 **100 名中等读者，**折扣为 **50%** )***