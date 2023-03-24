# 一个 Go 接口提示，直到泛型…

> 原文：<https://levelup.gitconnected.com/a-go-interface-tip-until-generics-7da75ba25352>

![](img/954a3be9e7e9625f43d840b2dc6dab88.png)

在我们进入 Go 中的泛型之前，我有一个关于接口的技巧可以分享。不要给一个接口添加方法，这些方法只需要接口本身来完成它们的工作。这看起来有点神秘，所以让我们看一个例子。假设你想让你系统中的东西变得*可定价*。这意味着他们需要能够提供一个价格，包括费用和税率。所以你写道:

```
type Priceable interface {
 Price() float64
 Fees() float64
 TaxRate() float64
}
```

这对你有用，但是你发现自己在重复写:

```
extendedPrice := pricable.Price() * priceable.TaxRate() + priceaable.Fees()
```

你决定所有可定价的东西*都应该有一个`ExtendedPrice()`，于是毫不犹豫地把它添加到界面上:*

```
*type Priceable interface {
 Price() float64
 Fees() float64
 TaxRate() float64
 ExtendedPrice() float64
}*
```

*不要！现在你已经迫使世界将`ExtendedPrice()`添加到所有实现 *Priceable 的东西中！*由于`ExtendedPrice()`只依赖于 *Priceable* 接口本身，因此将其作为一个函数添加，并将 *Priceable* 作为一个参数:*

```
*type Priceable interface {
 Price() float64
 Fees() float64
 TaxRate() float64
}func ExtendedPrice(p Priceable) float64 {
 return p.Price() * p.TaxRate() + p.Fees()
}*
```

*也许这看起来很尴尬，因为部分*可定价的*是方法，部分是函数。或者你可能会说 *duh* *当然！*不管怎样，即使在优秀的人的代码中，我也会周期性地遇到这个错误，迫使我一遍又一遍地添加相同的该死的实现来使用给定的接口。*