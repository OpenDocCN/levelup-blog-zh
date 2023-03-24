# Finally 块不会总是执行！

> 原文：<https://levelup.gitconnected.com/the-finally-block-wont-always-execute-a325e43ff3eb>

![](img/408a0b63905fd35fc0660cea47f96ec9.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果将`System.Environment.FailFast()`包装在`try` / `finally`中，`finally`块将不会执行。

这是因为进程会立即终止。

据我所知这是唯一一次`try` / `finally`区块不会进入 finally 区块: