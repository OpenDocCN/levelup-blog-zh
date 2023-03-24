# 3 种检验随机性的方法:鲍勃大叔的解决方案会让你大吃一惊。

> 原文：<https://levelup.gitconnected.com/3-ways-of-testing-randomness-uncle-bobs-solution-will-surprise-you-ed9740181d6d>

用仿制品？用兰姆达斯？拥抱随机性？哪种方式更好？

![](img/7388c19182c94d00f5cf95eff4806b25.png)

照片由 [Lucas Santos](https://unsplash.com/@_staticvoid?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 概观

在本文中，我们将探索三种不同的方法来测试使用随机生成的数字的代码。对于本文中的代码示例，我们将使用*武器*类，并尝试测试 *getDamege()* 方法:

```
class Weapon {
    private final int baseDamage = 100;
    private final int randomnessDelta = 5;

    public int getDamage() {
        int min = baseDamage - randomnessDelta;
        int max = baseDamage + randomnessDelta;
        return ThreadLocalRandom.current().nextInt(min, max + 1);
    }
}
```

## 1.依赖性倒置和模仿

首先，我们可以使用依赖倒置原则。这将允许我们打破*武器*类和*线程本地随机*类之间的直接依赖关系。

为此，我们需要用一个方法声明一个接口，该方法将接受上限和下限，并将返回它们之间的一个随机值—我们称之为 *WeaponRandomGenerator* :

```
class WeaponDi {
    private final RandomGenerator random;
    private final int baseDamage = 100;
    private final int randomnessDelta = 5;

    public WeaponDi(RandomGenerator random) {
        this.random = random;
    }

    public int getDamage() {
        int min = baseDamage - randomnessDelta;
        int max = baseDamage + randomnessDelta;
        return random.nextInt(min, max);
    }

    interface WeaponRandomGenerator {
        int nextInt(int min, int max);
    }
}
```

因此，我们现在可以使用 mocking 库轻松测试武器类。例如，您可以通过 Java 的 Mockito 实现这一点:

```
@Test
void whenMockingRandom_weaponShouldReturnMockedValue() {
    var mockedRandom = Mockito.mock(WeaponDi.RandomGenerator.class);
    Mockito.when(mockedRandom.nextInt(95, 105))
        .thenReturn(102);

    WeaponDi weapon = new WeaponDi(mockedRandom);

    assertThat(weapon.getDamage())
        .isEqualTo(102);
}
```

如果这个解决方案适合您，并且您想更深入地了解它，我推荐您阅读我的另一篇关于[依赖倒置、创建您自己的模拟对象和 MVC](https://medium.com/codex/mvc-violation-ruined-his-tic-tac-toe-game-2a9c88c8f940) 的文章。

## 2.使用 Lambda 表达式的依赖性反转

正如我们所看到的，上一个例子中的*weaporandomgenerator*接口包含一个函数。

因此，我们可以用一个接受两个整数参数并返回一个整数结果的函数来代替接口的用法。如果我们是 Java 的话，可以简单的用 *@FunctionalInterface* 来注释接口，应该就够了。

当在*生产中运行时，*我们将传递一个 lambda，它基于 *ThreadLocalRandom:* 生成一个随机数

```
new WeaponDi((min, max) -> ThreadLocalRandom.current().nextInt(min, max + 1));
```

然而，在*测试*中，我们将传递一个 lambda 表达式，该表达式始终返回相同的硬编码值:

```
@Test
void whenPassingALambda_weaponShouldReturnHardcodedValue() {
    WeaponDi weapon = new WeaponDi((min, max) -> 102);

    assertThat(weapon.getDamage())
        .isEqualTo(102);
}
```

## 3.拥抱随机性

不过，这两种解决方案都需要重构现有代码。可能会有我们无法改变它的情况——或者我们不想这样做。

对于这些场景，鲍伯·马丁叔叔提出了一个有趣的想法:他拥抱随机性，而是专注于测试结果的分布。

在这个简单的例子中，我们有 11 个可能的值:95 到 105。如果我们调用 *getDamage()* 方法 11.000 次，我们将期望得到每个值大约 1.000 次。

因此，我们可以简单地运行模拟 11.000 次，并期望 11 个值中的每一个都返回 700 到 1.300 次:

```
@Test
void given11_000simulations_theRandomDistributionShouldBeBalanced() {
    Weapon weapon = new Weapon();
    Map<Integer, Integer> numberOfResultsByDamage = new HashMap<>();

    for (int i = 0; i < 11_000; i++) {
        int damage = weapon.getDamage();
        var currentCount = numberOfResultsByDamage.getOrDefault(damage, 0);
        numberOfResultsByDamage.put(damage, currentCount + 1);
    }

    for(int value : numberOfResultsByDamage.values()) {
        assertThat(value).isBetween(700, 1300);
    }
}
```

这将是 Java8+的等价形式:

```
@Test
void given11_000simulations_theRandomDistributionShouldBeBalanced() {
    Weapon weapon = new Weapon();

    Map<Integer, Long> countByDmg = IntStream.range(0, 11_000).boxed()
        .map(x -> weapon.getDamage())
        .collect(Collectors.groupingBy(dmg -> dmg, Collectors.counting()));

    countByDmg.values().stream()
        .forEach(value -> assertThat(value).isBetween(700L, 1300L));
}
```

当然，测试失败的可能性很小，但这需要无数次的尝试。此外，是否有可能检查值的分布，计算标准偏差，然后提出一种更合适的断言方式，但您必须找出背后的数学原理。

## 结论

在本文中，我们讨论了使用随机生成值的 3 种不同的代码测试方法。最初，我们使用了依赖倒置原则和一个 mocking 库。在那之后，我们开始使用 lambda 表达式，这使得我们可以不用模仿就能进行测试。

最后，我们讨论了一种不同的方法，其中我们不再试图*模仿*随机生成器。相反，我们着重于测试值的分布是否平衡。

![](img/4a762db5e0472735cfe30bf69ef35087.png)

照片由[努贝尔森·费尔南德斯](https://unsplash.com/@nublson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 谢谢大家！

感谢你阅读这篇文章，请让我知道你的想法！欢迎任何反馈。

如果你想阅读更多关于干净的代码、设计、单元测试、函数式编程以及许多其他内容，请务必查看我的其他文章。你喜欢它的内容吗？考虑[关注或订阅](https://medium.com/@emanueltrandafir)电子邮件列表。

最后，如果你考虑成为一个中等会员，支持我的博客，这里是我的[推荐人](https://medium.com/@emanueltrandafir/membership)。

编码快乐！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入人才集体，找到一份令人惊喜的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)