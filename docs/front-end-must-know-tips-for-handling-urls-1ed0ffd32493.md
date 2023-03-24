# 前端必须知道—处理 URL 的提示

> 原文：<https://levelup.gitconnected.com/front-end-must-know-tips-for-handling-urls-1ed0ffd32493>

## 处理 URL 的提示

![](img/0f30a32928cf50e1f9b2c95229a79b0e.png)

今天我们将学习专用于处理 URL 查询的接口:URLSearchParams

**简单使用**

简单地新建一个 URLSearchParams 实例:

```
let url = '?wd=Maxwell&skill=JavaScript&year=2022';
let searchParams = new URLSearchParams(url);

for (let p of searchParams) {
  console.log(p);
}
// ["wd", "Maxwell"]
// ["skill", "JavaScript"]
// ["year", "2022"]
```

**获得单场**

如果现在我只想得到单个字段的值呢？只需要调用这个实例的 get 方法。

```
searchParams.get('wd') // "Maxwell"
searchParams.get('skill') // "JavaScript"
searchParams.get('year') // "2022"
```

有时候不知道某个字段是否存在，就想事先检查一下。使用示例的 has 方法来确定。

```
searchParams.has('wd') // true
searchParams.has('age') // false
```

**添加字段**

该实例提供了添加字段的`append` 方法，该方法采用两个参数，前者是键，后者是值。

```
searchParams.append('age', 26);
searchParams.has('age'); // true
searchParams.get('age'); // 26
```

**删除字段**

现在你不再需要年份字段了，直接用 delete 就可以了:

```
searchParams.delete('year');
searchParams.has('year'); // false
```

**设置字段**

有时候你想重写一个字段而不是添加(追加)一个字段，当你需要使用 set 方法的时候，比如我们认为 Maxwell 不仅会 JavaScript，还会 Python，C++

```
searchParams.set('skill', 'Python C++ JavaScript');
```

**转换成字符串**

修改实例后，有时需要再次转换成字符串，用于路由跳转等。，使用 toString 方法。

```
searchParams.toString(); // "wd=Maxwell&skill=JavaScript+Ptyhon+C++&year=2022&age=26"
```

这都是运营网址的黑科技。如果你对我的文章感兴趣，可以关注我。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)