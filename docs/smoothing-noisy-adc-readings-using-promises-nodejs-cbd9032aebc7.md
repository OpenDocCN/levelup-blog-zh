# 使用 Promises (NodeJS)平滑高噪声 ADC 读数

> 原文：<https://levelup.gitconnected.com/smoothing-noisy-adc-readings-using-promises-nodejs-cbd9032aebc7>

![](img/f8c18b0e5f1350c4122f4beb1d4c6afd.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com/s/photos/charts?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

使用 Javascript 从模拟传感器获得可靠读数！

在将模拟传感器与微控制器集成时，对信号进行滤波以提高信噪比非常重要。在硬件端，这可以通过使用高通或低通或带通滤波器来实现。ADC 将来自硬件滤波器的滤波信号转换为数字值。然后，我们对该值进行数字滤波，以获得可靠的读数。在 ADS1115 和 Raspberry Pi 上测试。

## ⭐️实施

*   设置样本大小、采样间隔和要忽略的异常值
*   收集阵列中的 ADC 样本
*   移除异常值
*   计算平均值

> 注:注入实际的模拟传感器读数代替代码中的“伏特”。