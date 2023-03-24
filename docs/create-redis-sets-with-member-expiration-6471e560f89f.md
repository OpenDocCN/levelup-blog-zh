# 创建成员过期的 Redis 集

> 原文：<https://levelup.gitconnected.com/create-redis-sets-with-member-expiration-6471e560f89f>

![](img/f6894d645f03b7c883b7fafbdc14bbcc.png)

Unsplash 上@aronvisuals 的照片

Redis 是一个很好的内存中键值数据存储，支持多种类型的值。[有序集合](https://redis.io/topics/data-types-intro#sorted-sets)就是其中之一。根据文献记载`sorted sets`是:

> *排序集合，类似于集合，但其中每个字符串元素都与一个称为 score 的浮点数值相关联。元素总是按照它们的分数排序，因此与集合不同，可以检索一系列元素(例如，您可能会问:给我前 10 名，或后 10 名)。*

关于`sorted sets`他们没有说的是`sets members`的到期时间无法确定，至少在这篇博文发表之前是如此。为什么我们需要定义`sets members`到期？

在我的例子中，我需要创建一个电话服务，它可以生成并验证发送给有特定限制的用户的 OTP。之前的设计使用的是 [Golang 速率限制](https://godoc.org/golang.org/x/time/rate)，我认为它不能水平扩展。这就是为什么我想在这个案例中使用 Redis。生成 OTP 的要求只有这样:

这就是为什么我想出了`Redis sets`但`sets members`不能有自己的到期时间。然后我在谷歌上搜索，直到我找到了[这个问题的评论](https://github.com/redis/redis/issues/135#issuecomment-2361996)作者是 [@pietern](https://github.com/pietern) 。这是一个相当不明智的举动，但至少是可行的。这就是为什么我试图实现它。Github 上的评论又一次拯救了我的工作。

这个想法很简单，使用有序集合上的分数作为到期时间。获取分数在`current millis`和`current millis + Y minutes`之间的有效成员，删除分数在 0 和`current millis`之间的过期成员。所以最小的伪代码应该是这样的:

```
# define variables
timeLimit := Y
requestLimit := X
key := +6212312341234
otp := randomString(6)
now := currentMillis()
exp := now + timeLimit# get total of generated phone number in key
validOTPs := redisQuery("ZRANGEBYSCORE $key $now $exp")# Limitting request
if count(validOTPs) >= requestLimit
then exit# Add members to key
redisQuery("ZADD $key $exp $otp")
```

我觉得这个方法已经足够好了，也是实现起来最简单的一个。您可以添加可选操作，如`adding expiration to the key`或`removing members that are no longer valid`。但是这个操作足以给`sets member`加上有效期。您还可以使用这种方法来限制 OTP 验证，以避免暴力破解。

*原发布于*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/create-redis-sets-with-member-expiration/)*。*