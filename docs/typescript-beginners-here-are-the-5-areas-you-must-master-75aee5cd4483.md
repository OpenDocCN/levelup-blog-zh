# 打字稿:需要掌握的 5 个领域

> 原文：<https://levelup.gitconnected.com/typescript-beginners-here-are-the-5-areas-you-must-master-75aee5cd4483>

## 在漫威世界中我们最喜爱的英雄和反派的帮助下，让我们一起来看看需要掌握的 5 个关键领域

![](img/d0b4bfc4bfdbfa33c6b327aed46b1669.png)

TypeScript 很受欢迎。最近由 T2 Stack Overflow 进行的一项调查显示，TypeScript 是仅次于 Rust 的第二受欢迎的语言。这比 2019 年的评级上升了 8 位。

更重要的是，如果你正在使用它，你是一个很好的公司。TypeScript 目前被微软、Asana、Lyft、Slack、所有 Angular 2+开发者、许多 React & Vue.js 开发者以及[数千家其他公司](https://stackshare.io/typescript)使用，因此它已经并将继续经受考验。

我是 80/20 法则的信徒，我认为有一个例子可以证明，对 20%的语言有很好的理解可以让你解决你遇到的 80%的日常问题。

在漫威宇宙中我们最喜爱的英雄和反派的帮助下，让我们一起来看看占 80%的 5 个需要掌握的关键领域。

# **1。确定类型**

在我们进一步讨论之前，让我们先讨论一下 TypeScript 中的类型，它通常被分为两类——**原始类型**和**复杂类型**。

TypeScript 支持 JavaScript 中所有的**原始数据类型**。

```
let Bool: boolean = CaptainMarvel.name === "Carol Danvers";let Number: number = 2022; 
**// Planned release for Captain Marvel 2**let String: string = "Binary Ignition";
**// Captain Marvels superpower**let Undefined: undefined = undefined;
**// Like where she was for most of EndGame**let Null: null = null;   
**// Like Captain Marvels Weaknesses****// Yet to ever use these, but when the days comes, I'll be ready**
let bigInt: bigint = 9007199254740993n
let Symbol: symbol = Symbol('foo');
```

## **推断并显式声明类型**

我们在上面看到的是我们在一个值和冒号后声明类型。这些是显式类型。如果没有显式声明类型，Typescript 将为您推断类型。这是什么意思？嗯，看下面…

```
**// The qoute that broke a million hearts**let string = "I love you"
string = "3000"
// Error: Type 'string' is not assignable to type 'number
```

没错，Typescript 正在后台做一些奇怪的事情，并且在您不需要做任何事情的情况下为您执行一些类型检查。

记住显式类型和推断类型，这非常有帮助，但也会让你挠头想知道错误从何而来。

正是在**复杂类型**中，一些真正的奇迹发生了。让我们看看我们遇到的最常见的一个。

## **对象类型**

在 JavaScript 中，我们分组和传递数据的基本方式是通过对象。在 TypeScript 中，我们通过*对象类型*来表示那些。我们可以通过将一些原始类型放在一起来实现这一点。

```
**// We can represent an object type like this**avenger:{name:string; superhuman:boolean}**// Or we could use the type alias, which when dealing with larger objects you may deam necessary**type Avenger: {
  name:string;
  superhuman:boolean;
}**// this allows us to do do exciting stuff like this**const isSuperHuman = (avenger: **Avenger**) => {
  console.log( avenger.name + (avenger.superhuman ? " is" : " is           
  not") + " superhuman");
}
```

## **任何**

`any`类型允许我们将任何特定的值赋给那个变量，在某种程度上模拟普通的 Javascript。当没有其他选项，或者没有类型定义可用时，这很有用。例如，第三方 API 或您正在使用的软件包。

```
let infinityStones:string = "Six"
infinityStones = 6
*// Error: Type 'number' is not assignable to type 'string'***// No longer a problem when I exchange string for an any type**
let infinityStones:any = "Six"
infinityStones = 6
```

**数组和元组**

更难理解的事情之一是 Typescript 中元组的概念。

```
**// This is an array**
let hero: string[] = ['Ironman', '49', 'true', '3000'];**// This is a tuple**
let hero: [string, number, boolean, number] = ['Ironman', 49, true, 3000];
```

具有固定数量的按特定顺序排列的值类型的数组称为元组。

元组保持严格的长度和排序。我喜欢把元组想象成我创建的它们自己的严格和独特的类型。我**不能**像扩展数组类型那样扩展它们。

就像给一个数组戴上手铐，他们就拿不出别的东西了。

```
let hero: [string, number, boolean, number] = ['Hulk', 49, true, 3000];**// A tuple can even keep the Hulk under control**
hero[3] = 'Smash';   // Error: Type 'string' is not assignable to type 'number'
```

现在，如果我需要解开手铐并扩展数组，会发生什么呢？使用`concat`方法是可能的。这将把你的元组变成一个`any[]`类型。这显然没有那么严格，但总比编译器错误好，对吗？

*对我来说，我想知道我什么时候把我的元组的类型改成 any[]。因为如果我的元组没有足够的可扩展性来覆盖我需要的模式，也许我应该重新考虑我的元组，或者也许我根本不应该使用元组？*

不管怎样，我认为这里有健康辩论的空间。

```
**// This is a tuple**
const ThanosFavoriteThings: [ string, number, string] = ['Gamora', 6, 'Infinity Stones'];ThanosFavoriteThings[3] = 'Power'
*// Error: Type '"Power"' is not assignable to type 'undefined'
// Error: Tuple type '[string, number, string]' of length '3' has no element at index '3'.***// Calling .concat() on a tuple returns an array with a type of any[]**
const AllThingsThanosLikes = ThanosFavoriteThings.concat(['Power']);**// An array is expandable**
AllThingsThanosLikes[5] = 'Gardening';console.log(AllThingsThanosLikes);
**// This will print ["Gamora", 6, "Infinity Stones", "Power", "Gardening"]**
```

# **2。Enums 的喜悦**

来自传统服务器端语言的人可能已经使用了一段时间。在类型可以接受任何预定义值集的任何地方使用枚举是一种好的做法。

想想“**北，南，东，西”**或**“男性，女性，非二元”**或“**复仇者联盟，奥创时代，无限战争，残局”**。我喜欢在创建多选下拉菜单或复选框选择的解决方案时使用它们。

枚举具有自动递增或可以设置为字符串或整数的枚举器。我通常更喜欢使用字符串值，尽管它们不会自动递增，但它们在调试时很有帮助，提高了可读性。

```
**// Auto Increment**
enum Movies {
  TheAvengers       // '0': "The Avengers "
  AgeOfUltron       // '1': "Age of Ultron"
  InfinityWar       // '2': "Infinity War "
  Endgame           // '3': "Endgame "
}**// Set Integer Value** enum MovieRelease {
  TheAvengers  = 2012
  AgeOfUltron  = 2015
  InfinityWar  = 2018
  Endgame      = 2019
}**// Set String Value**
enum MovieName {
  TheAvengers   = "The Avengers"
  AgeOffUltron  = "Age of Ultron"
  InfinityWar   = "Infinity War"
  Endgame       = "Endgame"
}const MovieName: MovieName = MovieName.InfinityWar
const MovieRelease: MovieRelease = MovieName.InfinityWarconsole.log(`${MovieName} was released in ${MovieRelease}`)
**// Logs "Infinity War was released in 2018"**
```

# **3。当你需要更多的灵活性时**

当使用现有的 Javascript 时，您可能希望重新分配现有的变量。Typescript 为我们提供了两种解决方案。

`any`类型，我们之前提到过，可以赋给任何变量。

或者可以通过在类型之间添加管道来创建的联合类型。它允许我们做一些非常有趣的事情

像这样…

```
let Thor: string | number | boolean;
Thor = 'Mjolnir';   **// Thors famous hammer**
Thor = 8;           **// The number of marvel movies he has featured** Thor = 'Avengers 4' **// The next movie Thor has a role in** Thor = Thor > Hulk  **// The great debate continues**
```

或者这个…

```
**// Lets create a mini battle game, working of the following pretence**Scarlet Witch > Dr. Strange 
**// I think Scarlet Witch beats Strange, since she nearly bet Thanos on her own**Dr. Strange > Dormmamu 
**// He's already managed to outsmart the ruler of the dark dimension once**Dormmamu < Scalet Witch 
**// Dormmamu kicks ass here, In fairness I think he beats most**--------------------------------------------------------------------**// This is a union of string literal types** type BattleCharacter = 'Scarlet Witch' | 'Dormmamu' | 'Dr. Strange';const Battle = (choice: BattleCharacter): void => {
  let result: string = '';
  switch (choice) {
    case 'Scarlet Witch':
      result = 'Dormmamu';
      break;
    case 'Dormmamu':
      result = 'Dr. Strange';
      break;
    case 'Dr. Strange':
      result = 'Scarlet Witch';
      break;
  }
console.log('Me: ', result);
}const number = Math.floor(Math.random()*3);let BattleCharacter: [BattleCharacter, BattleCharacter, BattleCharacter] = ['Scarlet Witch' | 'Dormmamu' | 'Dr. Strange'];Battle(choices[number]);
```

# **4。缩放我们新发现的控件**

接口定义了由我们到目前为止所看到的所有类型组成的对象。

正是有了接口，我们才能真正开始控制我们现在对对象模态值的控制，使用它们来确保将正确的数据传递给属性和函数。

这很好，但更好的是接口是可组合的。TypeScript 允许您在一个接口中嵌套接口或从另一个接口继承。

```
**// We can turn this...**interface Avenger {
  id: number;
  name: string;
  stats: {
    weapon: string;
    stength: number;
    superHuman: boolean;
  }
  movies: {
    [filmId: string]: {
      name: string;
      releaseDate: number;
      rating: number;
    }
  }
}**// into this...**interface Avenger {
  id: number;
  name: string;
  stats: Stats;
  movies: { [filmId: string] : Movie}}interface Stats {
  weapon: string;
  stength: number;
  superHuman: boolean;
}interface Movie {
  name: string;
  releaseDate: number;
  rating: number;
}
```

结果代码有点长，但是我们用一个大的接口解决了问题。现在，我们可以更容易地阅读带有命名类型的代码，并且可以在代码的其他地方重用较小的接口。

将类型组合在一起是保持代码有组织性和灵活性的基本方法。

# **5。将所有这些放在一起看函数**

函数是任何 Javascript 应用程序的基本构建块，因此自然地，它们将在 Typescript 中扮演重要的角色，我们将首先使用它来实现可读性、类型检查、调试和防止错误。

## **参数类型**

Typescript 允许我们为传递给函数的参数提供类型注释，这意味着它还需要能够处理可选参数。

为了表明参数是有意可选的，我们在它的名字后面添加了一个`?`操作符。这告诉 TypeScript 该参数是允许未定义的，并不总是必须提供。如果没有选择参数，它将推断参数类型，同样的方式推断初始化变量的类型与其初始值的类型相同。

## **函数返回类型**

所以我们可以检查进入函数的内容，你最好相信我们可以对输出的内容做同样的事情。同样，如果你不声明函数的返回类型，Typescript 会推断出函数的返回类型，否则，我们可以显式地为函数指定返回类型，包括不返回任何东西的函数。

这是通过类型值`void`完成的。

空有点像`any`的反义词，是完全没有任何类型。您通常会将此视为不返回值的函数的返回类型。通常，如果一个函数没有 return 语句，你可能会想使用它。

```
class Avenger {

  constructor(obj: Avenger){
    this.name = obj.name;
    this.stats = obj.stats;
    this.movies = obj.movies;
  }

  attack():void {
    const x = this.stats.superHuman ?
    this.stats.strength * 2 : this.stats.strength alert(this.name + ' can use ' + this.stats.weapon + 'to cause'      + x + 'damage')
  } bestMovie(): string {
    const movie = this.movies.reduce((prev, movie) => {
      return (prev === undefined || movie.rating > prev.rating) ?
        movie : prev
    })
    return movie.name
  }
}const IronMan = new Avenger({
  name: "Tony Stark",
  stats: {
    weapon: "Rockets",
    strength: 8,
    superHuman: false
  },
  movies: [
    { name: "Iron Man", releaseDate: 2008, rating: 7.9 },
    { name: "Iron Man 2", releaseDate: 2010, rating: 7.0 },
    { name: "Iron Man 3", releaseDate: 2013, rating: 7.2 }
  ]
})
```

现在我们有了。我希望你已经发现这是有用的。感谢您的阅读。如果你喜欢复仇者联盟这个主题，你可能也会喜欢我们在[创造的一些东西！！书呆子](https://www.notnotnerdy.com/)