# Optional.stream()

> 原文：<https://levelup.gitconnected.com/optional-stream-36de5ef2b11d>

![](img/c5c94a17f14bd67d2682e1e95b8da8d1.png)

本周，我了解了`Optional`的一个漂亮的“新”特性，我想在这篇文章中分享一下。从 Java 9 开始就有了，所以它的新颖性是相对的。

让我们从下面的序列开始计算订单的总价:

```
public BigDecimal getOrderPrice(Long orderId) {
    List<OrderLine> lines = orderRepository.findByOrderId(orderId);
    BigDecimal price = BigDecimal.ZERO;       // 1
    for (OrderLine line : lines) {
        price = price.add(line.getPrice());   // 2
    }
    return price;
}
```

1.  为价格提供一个累加器变量
2.  将每一行的价格加到总价中

如今，使用流而不是迭代可能更合适。以下代码片段与上一个代码片段等效:

```
public BigDecimal getOrderPrice(Long orderId) {
    List<OrderLine> lines = orderRepository.findByOrderId(orderId);
    return lines.stream()
                .map(OrderLine::getPrice)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

让我们关注一下`orderId`变量:它可能是`null`。

处理`null`值的必要方式是在方法开始时检查它——并最终抛出:

```
public BigDecimal getOrderPrice(Long orderId) {
    if (orderId == null) {
        throw new IllegalArgumentException("Order ID cannot be null");
    }
    List<OrderLine> lines = orderRepository.findByOrderId(orderId);
    return lines.stream()
                .map(OrderLine::getPrice)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

功能性的方法是将`orderId`包裹在`Optional`中。这是使用`Optional`的代码的样子:

```
public BigDecimal getOrderPrice(Long orderId) {
    return Optional.ofNullable(orderId)                        // 1    
            .map(orderRepository::findByOrderId)                // 2   
            .flatMap(lines -> {                                 // 3  
                BigDecimal sum = lines.stream()
                        .map(OrderLine::getPrice)
                        .reduce(BigDecimal.ZERO, BigDecimal::add);
                return Optional.of(sum);                       // 4     
            }).orElse(BigDecimal.ZERO);                        // 5    
}
```

1.  用`Optional`包裹`orderId`
2.  查找相关订单行
3.  使用`flatMap()`得到一个`Optional<BigDecimal>`；`map()`会得到一个`Optional<Optional<BigDecimal>>`
4.  我们需要将结果包装到一个`Optional`中，以符合方法签名
5.  如果`Optional`不包含值，则总和为`0`

`Optional`降低代码可读性！我相信可读性应该每次都胜过代码风格。

幸运的是，`Optional`提供了一个`stream()`方法(从 Java 9 开始)。它允许简化功能管道:

```
public BigDecimal getOrderPrice(Long orderId) {
    return Optional.ofNullable(orderId)
            .stream()
            .map(orderRepository::findByOrderId)
            .flatMap(Collection::stream)
            .map(OrderLine::getPrice)
            .reduce(BigDecimal.ZERO, BigDecimal::add);
}
```

下面是每行的类型摘要:

1.  `Optional.ofNullable(orderId)` : `Optional<Long>`
2.  `stream()` : `Stream<Long>`
3.  `map(orderRepository::findByOrderId)` : `Stream<List<OrderLine>>`
4.  `flatMap(Collection::stream)` : `Stream<OrderLine>`
5.  `map(OrderLine::getPrice)` : `Stream<BigDecimal>`
6.  `reduce(BigDecimal.ZERO, BigDecimal::add)` : `BigDecimal`

功能代码不一定意味着可读的代码。有了最后的变化，我相信两者都有。

**更进一步:**

*   [Option.stream() Javadoc](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/util/Optional.html#stream())

*原载于 2021 年 2 月 21 日* [*一个 Java 怪胎*](https://blog.frankel.ch/optional-stream/) *。*