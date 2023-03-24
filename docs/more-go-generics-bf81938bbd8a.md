# 更多 Go 仿制药

> 原文：<https://levelup.gitconnected.com/more-go-generics-bf81938bbd8a>

![](img/954a3be9e7e9625f43d840b2dc6dab88.png)

自从我发现了 Go2 Go 游乐场后，我一直很开心地在那里玩了一段时间。今天，受到对[科特林的序列](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/-sequence/)的美好回忆的启发，我花了大约一个小时实现了我的最爱。我已经在 Go 中编写了许多这样的一次性变体至少十几次了。我期待**而不是**一遍又一遍地写这些。以下是大约一个小时的收获:

鉴于这些情况，运行以下命令:

会产生:

```
Pets: [{family:Dog species:Pitbull} {family:Dog species:Chow} {family:Cat species:Tabby} {family:Bird species:Parrot} {family:Dog species:Beagle}]
Any dog: true
AssociateBy family: map[Bird:{family:Bird species:Parrot} Cat:{family:Cat species:Tabby} Dog:{family:Dog species:Beagle}]
Filter dogs: [{family:Dog species:Pitbull} {family:Dog species:Chow} {family:Dog species:Beagle}]
Find dog: &{family:Dog species:Pitbull}
FindLast dog: &{family:Dog species:Beagle}
Fold total by family: map[Bird:1 Cat:1 Dog:3]
GroupBy family: map[Bird:[{family:Bird species:Parrot}] Cat:[{family:Cat species:Tabby}] Dog:[{family:Dog species:Pitbull} {family:Dog species:Chow} {family:Dog species:Beagle}]]
FlatMap species: [Pitbull Chow Tabby Parrot Beagle]
Numbers: [1 9 5 2 7 0]
Filter odd: [1 9 5 7]
Fold sum: 24
```

它处理*宠物*或*整数*或任何东西。

## 而代码…

简洁明了:

如果对此感兴趣，请跳转到 golang/go 上的[提案](https://github.com/golang/go/issues/45955)讨论！