# 在 Go 中构建不可变的数据结构

> 原文：<https://levelup.gitconnected.com/building-immutable-data-structures-in-go-56a1068c76b2>

![](img/7e1b3068b5b62acef8f8fb284b97a1ee.png)

由 [Frantzou Fleurine](https://unsplash.com/@frantzou?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/solid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

共享状态很容易理解和使用，但是会导致难以追踪的细微错误。尤其是当你的数据结构只有一部分是通过引用传递的时候。切片就是一个很好的例子。稍后我会更详细地解释这一点。

不可变数据结构在处理经过几个转换阶段的数据时非常有用，或者说*状态*。不可变仅仅意味着原始结构没有改变，而是用新的属性值创建了该结构的新副本。

我们先来看一个简单的案例:

```
type Person struct {
    Name           string
    FavoriteColors []string
}
```

显然我们可以实例化一个`Person`并随意修改属性。这种方法本身没有任何问题。然而，当您进入更复杂的嵌套结构(通过通道传递引用、切片和副本)时，数据的共享副本可能会被修改，从而产生非常微妙的错误。

# **为什么我以前没有遇到过这些问题？**

如果您没有大量使用通道，或者您的代码以非常线性的方式工作，那么您不太可能会遇到微妙的错误，因为根据定义，一次只有一件事情在操作您的数据。

然而，除了避免错误之外，不可变数据结构还有其他好处:

1.  由于状态从未原地改变，因此最好进行一般调试，并记录转换的每一步，以便以后仔细检查。
2.  撤销，或“回到过去”的能力不仅是可能的，而且是微不足道的，因为它是通过赋值来完成的。
3.  共享状态被广泛认为是一个坏主意，因为要正确和安全地实现它，需要仔细放置/测试内存锁定的性能和复杂性。

# **吸气剂和*萎*和**

*getter*返回数据，*setter*变异状态， *withers* 创建新状态。

使用 getters 和 withers，我们可以精确地控制哪些属性可以被改变。这也给了我们一个很好的方法来记录过渡(稍后)。

新代码如下所示:

```
type Person struct {
    name           string
    favoriteColors []string
}func (p Person) WithName(name string) Person {
    p.name = name
    return p
}func (p Person) Name() string {
    return p.name
}func (p Person) WithFavoriteColors(favoriteColors []string) Person {
    p.favoriteColors = favoriteColors
    return p
}func (p Person) FavoriteColors() []string {
    return p.favoriteColors
}
```

这里需要注意的重要事项是:

1.  `Person`属性是私有的，因此外部包不能绕过这些方法。
2.  `Person` **的功能不**接收`*Person`。这确保了结构通过值传递并通过值返回。
3.  请注意，我使用了“With”一词，而不是“Set ”,以区分返回值是重要的，原始对象没有像 setter 暗示的那样被修改。
4.  这个包中的其他代码仍然可以访问这些属性(因此是可变的)。永远不要直接与属性交互，即使在同一个包中也要使用这些方法。
5.  每个萎者返回`Person`，这样他们就可以被锁住:

```
me := Person{}.
    WithName("Elliot").
    WithFavoriteColors([]string{"black", "blue"})

fmt.Printf("%+#v\n", me)// main.Person{name:"Elliot", favoriteColors:[]string{"black", "blue"}}
```

# **处理切片**

到目前为止，它仍然不理想，因为我们正在返回一个最喜欢的颜色的切片。由于切片是通过引用传递的，因此我们可以看到一个可能被忽略的错误示例:

```
func updateFavoriteColors(p Person) Person {
    colors := p.FavoriteColors()
    colors[0] = "red"

    return p
}func main() {
    me := Person{}.
        WithName("Elliot").
        WithFavoriteColors([]string{"black", "blue"})

    me2 := updateFavoriteColors(me)

    fmt.Printf("%+#v\n", me)
    fmt.Printf("%+#v\n", me2)
}// main.Person{name:"Elliot", favoriteColors:[]string{"red", "blue"}}
// main.Person{name:"Elliot", favoriteColors:[]string{"red", "blue"}}
```

我们打算改变第一种颜色，但是它也有改变`me`变量的副作用。因为这不会阻止代码在更复杂的应用程序中继续运行，所以试图寻找这样的突变会非常令人沮丧和耗时。

一种解决方案是确保我们从不按索引分配，而总是分配新的切片:

```
func updateFavoriteColors(p Person) Person {
    return p.
        WithFavoriteColors(append([]string{"red"}, p.FavoriteColors()[1:]...))
}// main.Person{name:"Elliot", favoriteColors:[]string{"black", "blue"}}
// main.Person{name:"Elliot", favoriteColors:[]string{"red", "blue"}}
```

在我看来，这既笨拙又容易出错。更好的方法是一开始就不要返回切片。扩展 getters 和 withers，只对元素(而不是整个切片)进行操作:

```
func (p Person) NumFavoriteColors() int {
    return len(p.favoriteColors)
}func (p Person) FavoriteColorAt(i int) string {
    return p.favoriteColors[i]
}func (p Person) WithFavoriteColorAt(i int, favoriteColor string) Person {
    p.favoriteColors = append(p.favoriteColors[:i],
        append([]string{favoriteColor}, p.favoriteColors[i+1:]...)...)
    return p
}
```

现在我们可以安全地使用:

```
func updateFavoriteColors(p Person) Person {
    return p.WithFavoriteColorAt(0, "red")
}
```

查看这个伟大的维基来学习切片技巧:[https://github.com/golang/go/wiki/SliceTricks](https://github.com/golang/go/wiki/SliceTricks)

# **构造函数**

在某些情况下，我们可以假设结构默认值是合理的。但是，最好总是创建一个构造函数，这样如果我们将来需要更改默认值，它就存在于一个地方:

```
func NewPerson() Person {
    return Person{}
}
```

您可以随意实例化`Person`,但是我更喜欢总是通过设置器来进行状态转换，以保持一致性:

```
func NewPerson() Person {
    return Person{}.
        WithName("No Name")
}
```

# **接口**

到目前为止，我们仍然使用公共结构。这对于测试来说可能是痛苦的，因为我们受到这些方法的支配，并且创建模拟可能会有不想要的副作用。

我们可以创建一个同名的接口，并通过将其重命名为`person`来使该结构私有:

```
type Person interface {
    WithName(name string) Person
    Name() string
    WithFavoriteColors(favoriteColors []string) Person
    NumFavoriteColors() int
    FavoriteColorAt(i int) string
    WithFavoriteColorAt(i int, favoriteColor string) Person
}type person struct {
    name           string
    favoriteColors []string
}
```

现在，我们可以通过仅覆盖我们希望存根的逻辑来创建测试模拟:

```
type personMock struct {
    Person
    receivedNewColor string
}func (m personMock) WithFavoriteColorAt(i int, favoriteColor string) Person {
    m.receivedNewColor = favoriteColor
    return m
}
```

测试代码可能看起来像这样:

```
mock := personMock{}
result := updateFavoriteColors(mock)

result.(personMock).receivedNewColor // "red"
```

# **记录变化**

正如我前面提到的，全状态转换非常适合调试，我们可以通过挂钩 withers 来捕获所有或部分转换:

```
func (p person) nextState() Person {
    fmt.Printf("nextState: %#+v\n", p)
    return p
}func (p person) WithName(name string) Person {
    p.name = name
    return p.nextState() // <- Use "nextState" whenever you return.
}
```

如果您有更复杂的逻辑，或者如果您愿意，您可以使用`defer`来代替:

```
func (p person) WithFavoriteColors(favoriteColors []string) Person {
    defer func() {
        p.nextState()
    }() p.favoriteColors = favoriteColors
    return p
}
```

现在我们可以看到变化:

```
nextState: main.person{name:"No Name", favoriteColors:[]string(nil)}
nextState: main.person{name:"Elliot", favoriteColors:[]string(nil)}
nextState: main.person{name:"Elliot", favoriteColors:[]string{"black", "blue"}}
```

你可能需要添加更多的信息。例如时间戳、堆栈跟踪或其他定制上下文，以便于调试。

# **历史和回滚**

我们可以收集状态作为历史，而不是打印更改:

```
type Person interface {
    // ...
    AtVersion(version int) Person
}type person struct {
    // ...
    history        []person
}func (p *person) nextState() Person {
    p.history = append(p.history, *p)
    return *p
}func (p person) AtVersion(version int) Person {
    return p.history[version]
}func main() {
    me := NewPerson().
        WithName("Elliot").
        WithFavoriteColors([]string{"black", "blue"})

    // We discard the result, but it will be put into the history.    
    updateFavoriteColors(me) fmt.Printf("%s\n", me.AtVersion(0).Name())
    fmt.Printf("%s\n", me.AtVersion(1).Name())
}// No Name
// Elliot
```

这对于最后需要进行自省的时候非常有用。如果以后出现问题，记录要记录的所有历史也是有用的，否则历史可以随实例一起丢弃。

*原载于 2018 年 12 月 30 日*[*http://Elliot . land*](http://elliot.land/post/building-immutable-data-structures-in-go)*。*