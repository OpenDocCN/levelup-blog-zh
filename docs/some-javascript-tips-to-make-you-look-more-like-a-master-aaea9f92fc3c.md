# 一些让你看起来更像大师的 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/some-javascript-tips-to-make-you-look-more-like-a-master-aaea9f92fc3c>

## 好的代码让人耳目一新，容易理解，也让你有成就感。

![](img/34fb54fca6c0ed8be7a6c23e2aa675b6.png)

作为程序员，我们也需要大量的写作技巧来编写代码。好的代码可以让人耳目一新，易于理解，舒适自然，同时让你充满成就感。因此，我收集了一些 JS 开发技巧，用来帮助您编写令人耳目一新、易于理解和舒适的代码。

## 字符串提示

1.  生成随机 ID

```
const RandomId = len => Math.random().toString(36).substr(3, len);
    const id = RandomId(10);
    console.log(id);   //l5fy28zzi5
```

2.格式化货币

```
 const ThousandNum = num => num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    const money = ThousandNum(2022112213);
    console.log(money);   //2,022,112,213
```

3.生成星级评定

```
const StartScore = rate => "★★★★★☆☆☆☆☆".slice(5 - rate, 10 - rate);
    const start = StartScore(3);
    console.log(start);   // ★★★☆☆
```

4.生成随机十六进制颜色值

```
 const RandomColor = () => "#" + Math.floor(Math.random() * 0xffffff).toString(16).padEnd(6, "0");
    const color = RandomColor();
    console.log(color);   // #bd1c55
```

5.操作 URL 查询参数

```
 const params = new URLSearchParams(location.search.replace(/\?/ig, "")); 
    // location.search = "?name=young&sex=male"
    params.has("young"); // true
    params.get("sex"); // "male"
```

## 数字提示

1.  舍入

> `Math.floor()`为正数，`Math.ceil()` 为负数。

```
 const num1 = ~~2.69;
    const num2 = 3.69 | 0;
    const num3 = 5.69 >> 0;

    console.log(num1);  // 2
    console.log(num2);  // 3
    console.log(num3);  // 5
```

2.互补零点

```
 const FillZero = (num, len) => num.toString().padStart(len, "0");
    const num = FillZero(187, 6);
    console.log(num);   //000187
```

3.转移价值

> 仅对 null，""，false，数字字符串有效

```
 const num1 = +null;
    const num2 = +"";
    const num3 = +false;
    const num4 = +"189";

    console.log(num1);  //0
    console.log(num2);  //0
    console.log(num3);  //0
    console.log(num4);  //189
```

4.时间戳

```
 const timestamp = +new Date("2022-11-21");
    console.log(timestamp);    //1668988800000
```

5.精确小数

```
 const RoundNum = (num, decimal) => Math.round(num * 10 ** decimal) / 10 ** decimal;
    const num = RoundNum(5.78602, 2);
    console.log(num);  //5.79
```

6.确定奇偶校验

```
 const OddEven = num => !!(num & 1) ? "odd" : "even";
    const num = OddEven(6);
    console.log(num);  //even
```

7.取最小值和最大值

```
 const arr = [31,22, 21,18,12,78,25];
    const min = Math.min(...arr);
    const max = Math.max(...arr);

    console.log(min);  //12
    console.log(max);  //78
```

## 数组提示

1.  克隆阵列

```
const _arr = [0, 1, 2];
const arr = [..._arr];
// arr => [0, 1, 2]
```

2.合并数组

```
const arr1 = [0, 1, 2];
const arr2 = [3, 4, 5];
const arr = [...arr1, ...arr2];
// arr => [0, 1, 2, 3, 4, 5];
```

3.去重复数组

```
const arr = [...new Set([0, 1, 1, null, null])];
// arr => [0, 1, null]
```

4.异步累加

```
async function Func(deps) {
    return deps.reduce(async(t, v) => {
        const dep = await t;
        const version = await Todo(v);
        dep[v] = version;
        return dep;
    }, Promise.resolve({}));
}
const result = await Func(); // Needs to be used under async bracket
```

5.在数组的开头插入成员

```
let arr = [1, 2]; 
// Choose any one of the following methods
arr.unshift(0);
arr = [0].concat(arr);
arr = [0, ...arr];
// arr => [0, 1, 2]
```

6.在数组末尾插入成员

```
let arr = [0, 1]; // Choose any one of the following methods
arr.push(2);
arr.concat(2);
arr[arr.length] = 2;
arr = [...arr, 2];
// arr => [0, 1, 2]
```

7.创建指定长度的数组

```
const arr = [...new Array(3).keys()];
// arr => [0, 1, 2]
```

8.减少而不是映射和过滤

```
const _arr = [0, 1, 2];

// map
const arr = _arr.map(v => v * 2);
const arr = _arr.reduce((t, v) => {
    t.push(v * 2);
    return t;
}, []);
// arr => [0, 2, 4]

// filter
const arr = _arr.filter(v => v > 0);
const arr = _arr.reduce((t, v) => {
    v > 0 && t.push(v);
    return t;
}, []);
// arr => [1, 2]

// map 和 filter
const arr = _arr.map(v => v * 2).filter(v => v > 2);
const arr = _arr.reduce((t, v) => {
    v = v * 2;
    v > 2 && t.push(v);
    return t;
}, []);
// arr => [4]
```

## 对象提示

1.  克隆对象

```
const _obj = { a: 0, b: 1, c: 2 }; 
// Choose any one of the following methods
const obj = { ..._obj };
const obj = JSON.parse(JSON.stringify(_obj));
// obj => { a: 0, b: 1, c: 2 }
```

2.合并对象

```
const obj1 = { a: 0, b: 1, c: 2 };
const obj2 = { c: 3, d: 4, e: 5 };
const obj = { ...obj1, ...obj2 };
// obj => { a: 0, b: 1, c: 3, d: 4, e: 5 }
```

3 .对象文字

```
const env = "prod";
const link = {
    dev: "Development Address",
    test: "Testing Address",
    prod: "Production Address"
}[env];
// link => "Production Address"
```

4.创建空对象

```
const obj = Object.create(null);
Object.prototype.a = 0;
// obj => {}
```

5.删除对象的无用属性

```
const obj = { a: 0, b: 1, c: 2 }; // Only want to take b and c
const { a, ...rest } = obj;
// rest => { b: 1, c: 2 }
```

感谢您的阅读，我们将在后续文章中继续为您编译更多 Javascript 开发技巧。如果你对我的文章感兴趣，可以关注我。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)