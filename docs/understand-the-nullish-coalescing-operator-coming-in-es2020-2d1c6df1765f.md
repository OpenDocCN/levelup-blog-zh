# 了解 ES2020 中的 Nullish 合并运算符

> 原文：<https://levelup.gitconnected.com/understand-the-nullish-coalescing-operator-coming-in-es2020-2d1c6df1765f>

## 了解第三阶段提案中即将推出的 JavaScript 运算符。

![](img/219287354246be5c1812ede6701b1b1e.png)

**无效合并运算符**

`??` →无效合并运算符。

无效合并运算符`??`，是一个短路运算符，与`&&`和`||`一样，如果左侧操作数是`**null**`或`**undefined**`，它将返回右侧操作数。否则，它返回左侧操作数。

使用`??`时，falsy 值(空字符串`('')`、`0`、`false`、`NaN`)仍将返回左操作数。而在`||`或`&&`中，它将返回右侧操作数。

```
let data = {};**let name = data.name ?? "Anonymous";** // returns "Anonymous"data = {name : "John"};**name = data.name ?? "Anonymous";** **// return "John"**data = {name : ""};**name = data.name ?? "Anonymous";** **// return ""**
```

nullish 合并操作符将取代我们为变量设置默认值的方式。考虑功能:

```
function printName(user) {
   console.log(`Hi ${user.name} From JavaScript Jeep`);
}
```

在上面的函数中，我们有一个`user`参数。如果`user`对象不包含`name`，那么它将导致`undefined`。在旧版本的 JavaScript 中，我们用以下模式处理这个问题:

```
function printName(user = {}) {

   const name = user.name ? user.name : "Anonymous"; // Or another way

    // const name = user.name || "Anonymous"; console.log(`Hi ${name} From JavaScript Jeep`);}
```

但是使用零化合并:

```
function printName(user = {}) { **const name = user.name ?? "Anonymous";** console.log(`Hi ${name} From JavaScript Jeep`);}
```

# 为什么我们需要零化合并？

当使用短路运算符`&&`和`||`时，我们需要处理所有的 falsy 值。

`||` →这将返回它找到的第一个真值。否则，它返回最后一个 falsy 值。

```
var a = 10, b = 0, c = null;a || b; // 10 (first truthy value)b || c; // null (last falsy value)
```

`&&` →这将返回它找到的第一个错误值。否则，它返回最后一个真值。

```
var a = 10, b = 0, c = null, d = 20;**a && b && c ;  // 0 (first falsy value)****a && d; // 20 (last truthy value)**
```

这些短路操作符的问题在于，在 JavaScript 中:

`0, false, '', NaN` →也是虚伪的价值观

```
var name ;name = name || “Anonymous”; // Anonymous.
```

**但是考虑到我们允许空字符串作为有效名称，那么:**

```
var name = '';name = name || “Anonymous”; // Anonymous.
```

所以对于这种情况，最好的解决方案是 nullish 合并操作符，它只在左操作数是`undefined`或`null`时设置默认值。

```
var name = ''; name ?? "Anonymous"; // **''**
```

评估示例:

```
**false ?? true; **  //false**0 ?? 1;   **       // 0**'' ?? 'Anonymous';** //''

**null ?? 0 ;**      // 0**undefined ?? 0 ;** //0
```

另一个:

```
function returnNull() {
   return null;
}function returnZero() {
   return 0;
}**returnNull() ?? returnZero(); // 0**
```

与 OR 和 and 逻辑运算符一样，如果左侧既不是`null`也不是`undefined`，则不对右侧表达式求值。

```
var a = 0;var b = 0;var c = a ?? ++b; a; // 0b; // 0c; // 0
```

# 没有使用`&&`和`||`运算符的链接。

不能将 AND ( `&&`)和 OR(`||`)运算符直接与`??`结合使用。

```
// incorrect waynull || undefined ?? 0 ;// correct way(null || undefined) ?? 0;
```

[](https://www.sitepoint.com/?ref=jagathishsaravanan) [## 学习 HTML、CSS、JavaScript、PHP、Ruby 和响应式设计

### 通过 SitePoint 教程、课程和书籍学习网页设计和开发——html 5、CSS3、JavaScript、PHP、移动应用程序…

www.sitepoint.com](https://www.sitepoint.com/?ref=jagathishsaravanan) 

## 参考: [V8](https://v8.dev/features/nullish-coalescing) ， [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator) 。

跟随 [Javascript 吉普🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----2d1c6df1765f--------------------------------)