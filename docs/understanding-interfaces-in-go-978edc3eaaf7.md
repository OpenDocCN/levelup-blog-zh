# 理解 Go 中的接口

> 原文：<https://levelup.gitconnected.com/understanding-interfaces-in-go-978edc3eaaf7>

![](img/462356578c39f227d705a17d8dda3328.png)

对于编程新手和没有使用过接口的程序员来说，接口可能看起来是一个奇怪的概念，因为他们要么对接口不太有信心，要么可能是因为他们到目前为止使用的工具不太依赖于使用接口。

好吧，界面应该让你的生活更轻松，而不是让你紧张！我写这篇博客来告诉你为什么。

我们将以我最喜欢的游戏之一《巫师 3》为基础，来理解界面的概念。

在游戏中，考虑以下两类经常偶遇并以打斗告终的角色:
巫师
怪物

这两种类型的角色都有一些共同的特征:
名字
等级
攻击
生命力(或生命值)

然而，他们也有自己独特的特点。比如，一个巫师可能属于某一派，一个怪物会属于某一类怪物。

考虑到这些因素，让我们为巫师和怪物创建我们的[结构](https://gobyexample.com/structs):

```
package mainimport "fmt"**type character struct {
 name     string
 level    int
 attacks  map[string]int
 vitality int
}****type witcher struct {
 character
 school string
}****type monster struct {
 character
 monsterType string
}**func main() {

}
```

很好，所以我们看到了角色结构中的共同特征，其他*巫师*和*怪物*结构也有它们自己的独特字段，除了我们从*角色*中重用的字段。

现在，让我们创造一个巫师和一个怪物:

```
...func main() { geralt := **witcher**{
  **character**: **character**{
   name:     "Geralt",
   level:    112,
   attacks: map[string]int{"swordHeavy": 1250, "swordQuick": 750},
   vitality: 3000,
  },
  school: "Wolf",
 } ekkimmara := **monster**{
  **character**: **character**{
   name:     "Ekkimmara",
   level:    114,
   attacks: **m**ap[string]int{"heavySlash": 1440, "rush": 800},
   vitality: 6500,
  },
  monsterType: "Vampire",
 }}
```

所以我们有我们的巫师杰洛特，他有两种攻击:一种重剑攻击和一种快剑攻击。这两种攻击都是地图[中的关键，与它们将造成的伤害相对应。Ekkimmara 也有自己的攻击和伤害。](https://gobyexample.com/maps)

酷，我们列出了对这些角色的攻击以及这些攻击会造成的伤害。

如果我们现在仔细想想，这是这两个角色的共同特征。如果他们在战斗中相遇，他们都可以通过攻击对方来造成伤害，并且他们可以在成为任何攻击的受害者后受到伤害。

这是我们可以开始考虑实现一个通用行为的接口的地方！

一个接口将帮助我们把巫师和 ekkimmara 放入一个共同的类型:战斗角色，然后我们可以用它来分享共同的行为。这可以通过代码变得更清楚:

```
**type combatCharacters interface {

   attack(attackName string) int
   takeDamage(damage int)
   getName() string
   getVitality() int****}**
```

一个接口将保存一个方法签名列表，任何实现这些方法的结构(或者更精确的类型)也将是类型 **combatCharacters。**

在 Go 中，接口是隐式实现的。你所要做的就是把在一个接口中定义的方法附加到一个结构上，这样这个结构就有资格成为这个接口所提到的类型。

因此，如果我们将上面提到的方法附加到我们的 witcher 和 monster 结构，那么为这些结构获得的任何实例也将是类型 **combatCharacters。**

这将有助于创建一个造成攻击和伤害的通用函数。这个函数可以把一个巫师当作攻击者，把一个怪物当作受害者。

我们即将创建的函数的一瞥:

```
...
*// our interface for reference*type **combatCharacters** interface {

   attack(attackName string) int
   takeDamage(damage int)
   getName() string
   getVitality() int}...func **doDamage**(
  *attacker* **combatCharacters**, 
  *victim* **combatCharacters**, 
  attackName string
) { *// since the attack method was part of our interface    
    // we will get the damage inflicted by an attacker
    // attackName is a string and an int is returned* **damageInflicted := attacker.attack(attackName)** *// since the takeDamage method was part of our interface
    // we can use it to record damage inflicted on a victim*

    **victim.takeDamage(damageInflicted)**}...
```

上面的函数可以将任何类型为**战斗角色**的攻击者和类型为**战斗角色**的受害者作为参数。

如前所述，这可以通过简单地实现我们的接口中定义的方法来实现。

我们将首先为我们的巫师做这件事:

```
...type witcher struct {
 character
 school string
}func (w witcher) **attack(attackName string) int** {
 return w.attacks[attackName]
}*// pointer to witcher taken 
// to reflect actual damage to a witcher's vitality*
func (w *witcher) **takeDamage(damage int)** {
 w.vitality = w.vitality - damage
}func (w witcher) **getName() string** {
 return w.name
}func (w witcher) **getVitality() int** {
 return w.vitality
}...
```

请注意，我们已经将所有必需的方法签名(根据我们的接口)添加到了我们的 witcher 结构中，使其符合类型 **combatCharacters** 。

在 takeDamage 方法中，请注意我们使用了一个指向 witcher struct 类型值的指针。这样做实际上是为了改变巫师的生命力，以防他在攻击中受到伤害。

对怪物做同样的事情:

```
...type monster struct {
 character
 monsterType string
}func (m monster) **attack(attackName string) int** {
 return m.attacks[attackName]
}func (m *monster) **takeDamage(damage int)** {
 m.vitality = m.vitality - damage
}func (m monster) **getName() string** {
 return m.name
}func (m monster) **getVitality() int** {
 return m.vitality
}...
```

除了使用的结构类型之外，没有太大的区别。这也符合现在**战斗角色**的类型。

现在，剩下要添加的是我们描述敌人之间冲突的功能:

```
...func doDamage(
  attacker **combatCharacters**, 
  victim **combatCharacters**, 
  attackName string
) {
    damageInflicted := attacker.**attack**(attackName)
    victim.**takeDamage**(damageInflicted)

    fmt.Println(
      attacker.**getName**(), 
      "attacked", 
      victim.**getName**(), 
      "did", 
      damageInflicted, 
      "damage",
    )

    fmt.Println(
      victim.**getName**(), 
      "now has", 
      victim.**getVitality**(), 
      "health remaining",
    )
}...
```

粗体突出显示的是我们在界面中提到的方法。

这意味着，我们现在可以执行类似于:

```
...doDamage(geralt, ekkimmara, "swordHeavy")...
```

geralt 和 ekkimmara 都符合战斗角色类型，将被传递到函数中。在这种情况下，杰勒特将成为攻击者，而埃克金马拉将成为“剑重”攻击的受害者。

总的来说，我们的代码如下所示:

```
package mainimport (
 "fmt"
)**type combatCharacters interface {
 attack(attackName string) int
 takeDamage(damage int)
 getName() string
 getVitality() int
}**type character struct {
 name     string
 level    int
 attacks  map[string]int
 vitality int
}type witcher struct {
 character
 school string
}func (w witcher) attack(attackName string) int {
 return w.attacks[attackName]
}func (w *witcher) takeDamage(damage int) {
 w.vitality = w.vitality - damage
}func (w witcher) getName() string {
 return w.name
}func (w witcher) getVitality() int {
 return w.vitality
}type monster struct {
 character
 monsterType string
}func (m monster) attack(attackName string) int {
 return m.attacks[attackName]
}func (m *monster) takeDamage(damage int) {
 m.vitality = m.vitality - damage
}func (m monster) getName() string {
 return m.name
}func (m monster) getVitality() int {
 return m.vitality
}func doDamage(
  attacker **combatCharacters**, 
  victim **combatCharacters**, 
  attackName string,
) {
    damageInflicted := attacker.**attack**(attackName)
    victim.**takeDamage**(damageInflicted)

    fmt.Println(
      attacker.**getName**(), 
      "attacked", 
      victim.**getName**(), 
      "did", 
      damageInflicted, 
      "damage",
    )

    fmt.Println(
      victim.**getName**(), 
      "now has", 
      victim.**getVitality**(), 
      "health remaining",
    )
}func main() {
 geralt := witcher{
  character: character{
   name:     "Geralt",
   level:    112,
   attacks:  map[string]int{**"swordHeavy": 1250**, "swordQuick": 750},
   vitality: 3000,
  },
  school: "Wolf",
 } ekkimmara := monster{
  character: character{
   name:     "Ekkimmara",
   level:    114,
   attacks:  map[string]int{"heavySlash": 1440, "rush": 800},
   **vitality: 6500**,
  },
  monsterType: "Vampire",
 } **doDamage(&geralt, &ekkimmara, "swordHeavy")**}
```

请注意，我们将 geralt 和 ekkimmara 的地址作为参数传递，这符合我们的 takeDamage 方法定义，我们实际上是在该方法中改变了活力:

```
func (w *witcher) takeDamage(damage int) {
 w.vitality = w.vitality - damage
}...func (m *monster) takeDamage(damage int) {
 m.vitality = m.vitality - damage
}
```

在运行完整的代码时，您应该在终端中获得以下输出:

```
Geralt attacked Ekkimmara did 1250 damage
Ekkimmara now has 5250 health remaining
```

所以现在你知道了，接口是很好的俱乐部共同行为和重用通用函数！

这是 Ekkimmara 攻击我们的巫师:

```
doDamage(&ekkimmara, &geralt, "heavySlash")
```

输出是:

```
Ekkimmara attacked Geralt did 1440 damage
Geralt now has 1560 health remaining
```

顺便提一下，每个类型都将实现空接口[:interface { }，因为每个类型至少没有方法！所以每个类型都可以作为空接口——接口{}类型。这也是因为接口在 Go 中是隐式实现的。通过使用一个*实现*关键字或类似的东西，你不能明确地说出某个东西使用了某个接口。](https://tour.golang.org/methods/14)

因此，在 Go 的文档中，您可能经常会发现这样的内容:

```
[func Println(a ...interface{}) (n int, err error)](https://golang.org/pkg/fmt/#Println)
```

上面是非常常见的 fmt 的方法签名。Println()。

如您所知，它可以接受任意类型的可变数量的参数，因此定义中包含一个空接口！这样做是为了使任何类型的值都可以作为参数传递。

还要记住，在运行时，一个值只能有一种类型。只要需要，Go 运行时会将不同类型的值转换为接口{}类型。更多信息请点击。